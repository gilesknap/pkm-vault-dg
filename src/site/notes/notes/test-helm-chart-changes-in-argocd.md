---
{"dg-publish":true,"permalink":"/notes/test-helm-chart-changes-in-argocd/","title":"Test Helm Chart Changes in ArgoCD","tags":["kind/how-to","tech/containers/helm","tech/containers/argocd","tech/containers/epics-containers","kind/deployment","domain/software"],"dg-note-properties":{"title":"Test Helm Chart Changes in ArgoCD","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/helm","tech/containers/argocd","tech/containers/epics-containers","kind/deployment","domain/software"],"summary":"How to iteratively test local Helm chart changes via ArgoCD without repeatedly republishing the chart.","personal":false}}
---


How to test local Helm chart changes in [[ArgoCD and EPICS Containers\|ArgoCD]] without repeatedly re-publishing the chart. Uses `bl47p-ea-fastcs-01` as the running example.

See also: [[notes/fastcs-charts-deployment-and-debugging\|fastcs-charts-deployment-and-debugging]] and [[notes/helm-chart-structure-and-json-schema-references\|helm-chart-structure-and-json-schema-references]].

## Overview

The workflow avoids re-publishing the chart to a registry on every iteration. Instead, you temporarily point the IOC's `Chart.yaml` at a local file-path dependency, bundle the zipped chart into the services repo, and let ArgoCD pick it up from the branch.

> **IMPORTANT — do NOT commit the `Chart.yaml` changes permanently.**

---

## Step 1 — Update `Chart.yaml` to use a local chart

First confirm `Chart.yaml` is **not** a symlink, then replace the dependency block:

```yaml
# A Helm Chart for an IOC instance
apiVersion: v2
name: ec-service
version: 1.0.0

type: application

dependencies:
  - name: fastcs
    # version: 0.9.2-alpha.4
    # repository: "oci://ghcr.io/diamondlightsource/charts"
    version: 0.1.0
    repository: "file://../../../fastcs/Charts/fastcs"
```

---

## Step 2 — Verify rendering with `helm template`

```bash
# Remove previous chart cache and lock file first
rm -r services/bl45p-ea-fastcs-01/charts services/bl45p-ea-fastcs-01/Chart.lock

# Render with both values files (mirrors what ArgoCD passes)
helm template --dependency-update bl45p-ea-ioc-01 services/bl45p-ea-fastcs-01 \
  --values services/bl45p-ea-fastcs-01/values.yaml \
  --values services/values.yaml \
  --debug
```

---

## Step 3 — Bundle and push the zipped chart

```bash
git add -f services/bl47p-ea-fastcs-01/charts/fastcs-instance-0.1.0.tgz
git commit -m'trying another chart change' --amend
git push -f
```

Then **refresh the ArgoCD app** (branch tracking). ArgoCD will pick up the bundled `.tgz`.

---

## Step 4 — Finalise once happy

1. Tag and release the Helm repo.
2. Remove the bundled chart from the services repo: `git rm services/bl47p-ea-fastcs-01/charts/fastcs-instance-0.1.0.tgz`
3. Restore `Chart.yaml` to point at the new release tag.
4. Commit and push the services repo.

---

## Related

- [[notes/fastcs-charts-deployment-and-debugging\|fastcs-charts-deployment-and-debugging]]
- [[notes/helm-chart-structure-and-json-schema-references\|helm-chart-structure-and-json-schema-references]]
- [[notes/ioc-instance-and-fastcs-helm-chart-overlap\|ioc-instance-and-fastcs-helm-chart-overlap]]
- [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]]
- [[notes/remote-login-to-argocd\|remote-login-to-argocd]]

^[raw/articles/test-helm-chart-changes-in-argocd.md]
