---
{"dg-publish":true,"permalink":"/notes/helm-chart-configmap-hashing-problem/","title":"Helm Chart ConfigMap Hashing Problem","tags":["kind/concept","tech/containers/helm","tech/containers/kubernetes","tech/containers/epics-containers","domain/software"],"dg-note-properties":{"title":"Helm Chart ConfigMap Hashing Problem","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/concept","tech/containers/helm","tech/containers/kubernetes","tech/containers/epics-containers","domain/software"],"summary":"Analysis and solution for propagating a ConfigMap hash from a parent Helm chart down to a child IOC chart to trigger pod restarts on config changes.","personal":false}}
---


## Status

Resolved in [[notes/epics-containers-breaking-changes-5-0-0\|epics-containers-breaking-changes-5-0-0]] via library charts and Helm schema generation in `ec-helm-charts` 5.0.0.

## The Problem

In services repos, the contents of the `config/` folder are ingested into a ConfigMap, which IOCs (rendered via the [[notes/ioc-instance-and-fastcs-helm-chart-overlap\|ioc-instance chart]]) mount at runtime. When Helm/[[notes/argocd-for-beamlines\|ArgoCD]] deploys an update to the config folder, the IOC pod should be restarted automatically.

The natural approach is to hash the ConfigMap in `templates/configmap.yaml` in the **parent** chart and pass that hash as a pod label to the **child** chart — a label change triggers a redeployment. However, Helm provides no native mechanism to pass a render-time computed value from parent to child.

**Workaround in use:** embed a commit hash in pod labels, requiring ArgoCD to track tags rather than `main` (tracking `main` would restart every IOC on every merge).

This issue has been under active discussion for ~9 years in the [Helm repo (issue #1892)](https://github.com/helm/helm/issues/1892).

## Proposed Solutions

### 1. Put everything in `values.yaml`

Add config-file contents as multi-line strings in the child namespace of `values.yaml`. The child can then hash these and build the ConfigMap itself, eliminating the `templates/` and `config/` folders in the parent.

**Downside:** developers can no longer edit `ioc.yaml` / `ioc.db` as normal files; schema checking and file-type-aware editing are lost.

### 2. Setting values at render time in the parent

The idea: make the parent render first, compute a value, then pass it to children. After investigation this is **not possible** — the parent namespace is not visible inside child templates:

```
<.Values.parent.render_time_value>: nil pointer evaluating interface {}.render_time_value
```

Additionally, a value set inside one template file is not visible in a second template file in the same chart — it is scoped to the current template only.

**Partial workaround that was ultimately used** in the final solution:

```yaml
{{- $_ := set .Values "configFolderConfigMap" (.Files.Glob "config/*").AsConfig -}}
{{- $_ := set .Values "configFolderHash" (.Values.configFolderConfigMap | sha256sum) -}}
```

### 3. Make the child a Library Chart

See [Helm Library Charts docs](https://helm.sh/docs/topics/library_charts/). A library chart renders in the context of the parent, giving it direct access to the config folder.

**Demo:** `helm-test/library_test/test.sh` in [epics-containers/helm-test](https://github.com/epics-containers/helm-test).

**Downsides:**
- Library charts have no `values.yaml` defaults of their own — all values come from the parent.
- Values must be placed at the root of the parent's `values.yaml` (no `child:` namespace).
- Requires passing multiple values files per `helm install`: global defaults from `services-template-helm`, beamline-level overrides, and per-IOC settings.

### 4. Combination of Application and Library Charts

A library chart handles config ingestion; an application chart provides value defaults and renders the StatefulSet. However, this re-creates the original problem: the application chart still cannot receive the hash from the library chart context.

## Full Solution (ioc-instance Chart)

Combines approaches 2 and 3:

- Use a **library chart** (approach 3) so the child renders in the parent context.
- Use `set .Values` (approach 2) to compute the hash once and reuse it without re-expanding the config folder.
- Supply library-chart default values as a [Helm export](https://stackoverflow.com/questions/76954247/define-a-default-values-yaml-file-in-a-library-dependency-chart), moving defaults back into `ioc-instance` and eliminating the extra values file.
- Demo in [helm-test/ioc_instance_demo](https://github.com/epics-containers/helm-test/blob/main/ioc_instance_demo) — output matches the old application chart except `configHash:` replaces `rollme:`.
- **Bonus:** symlinked files in `config/` are included in the hash.

## Remaining Issue: Values Schema Generation

Schema generation used [helm-values-schema-json](https://github.com/losisin/helm-values-schema-json). With the library-chart approach, the default values file gains extra nesting:

```yaml
# library chart exports
exports:
  defaults:
    image: ""
```

whereas the parent supplies values at the root:

```yaml
image: ""
```

Filed as [issue #283](https://github.com/losisin/helm-values-schema-json/issues/283).

**Proposed fix using YAML anchors and aliases:**

```yaml
ioc-instance: &ioc-instance
  # @schema description: the container image URI
  image: ""
  # @schema description: The location where the IOCs will run.
  location: ""
  # ... etc.

exports:
  defaults:
    ioc-instance:
      <<: *ioc-instance
```

This lets the schema tool process the top-level `ioc-instance:` block normally while the library export is kept in sync via the alias.

## Related

- [[notes/ibch-runtime-support-helm-shared-schema-architecture\|ibch-runtime-support-helm-shared-schema-architecture]] — shared schema architecture that builds on this solution
- [[notes/helm-chart-structure-and-json-schema-references\|helm-chart-structure-and-json-schema-references]] — broader Helm chart structure notes
- [[notes/epics-containers-breaking-changes-5-0-0\|epics-containers-breaking-changes-5-0-0]] — 5.0.0 release that ships the library-chart approach
- [[notes/argocd-for-beamlines\|argocd-for-beamlines]] — ArgoCD deployment context
- [[notes/ioc-instance-and-fastcs-helm-chart-overlap\|ioc-instance-and-fastcs-helm-chart-overlap]] — ioc-instance chart overlap discussion
