---
{"dg-publish":true,"permalink":"/notes/fastcs-charts-deployment-and-debugging/","title":"FastCS Helm Charts","tags":["kind/concept","tech/containers/helm","tech/containers/kubernetes","tech/epics/fastcs","tech/containers/epics-containers","tech/devops/github-actions","kind/debugging","domain/software"],"dg-note-properties":{"title":"FastCS Helm Charts","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/concept","tech/containers/helm","tech/containers/kubernetes","tech/epics/fastcs","tech/containers/epics-containers","tech/devops/github-actions","kind/debugging","domain/software"],"summary":"Structure, CI workflow, schema generation, and in-cluster debug/dev features of the FastCS Helm charts in DiamondLightSource/FastCS.","personal":false}}
---


## Overview

The `Charts/` directory of the [DiamondLightSource/FastCS](https://github.com/DiamondLightSource/FastCS) repository contains [[notes/helm-chart-structure-and-json-schema-references\|Helm charts]] for deploying FastCS services.

Source: `raw/articles/fastcs-helm-charts.md`

## GitHub Actions Workflow

A standalone `_helm.yml` workflow handles all chart CI. It:

- Validates SemVer tags to the stricter form Helm requires.
- Finds all subfolders in `Charts/` and packages them as Helm charts.
- Publishes packaged charts to `ghcr.io/${{ github.repository_owner }}/charts/CHART_NAME`.
- Uploads `/schemas` contents as release artifacts.

The `_pypi` workflow depends on `_helm` via `needs` in `ci.yml`, ensuring PyPI publishes only happen with valid SemVer tags.

## Schema Generation

Values schema generation uses [helm-values-schema-json](https://github.com/losisin/helm-values-schema-json), driven by annotations in each chart's default `values.yaml`. The output file is always `values.schema.json`, placed alongside `values.yaml` and committed to the repo.

Generation is automated via a **pre-commit hook**:

```bash
git add . ; pre-commit
# or
pre-commit run --all-files
```

Because the schema file uses the standard name, it is packaged inside the Helm chart and picked up automatically by [[notes/argocd-for-beamlines\|ArgoCD]] schema validation.

See also: [[notes/auto-generate-schema-odin-eiger\|auto-generate-schema-odin-eiger]] and [[notes/helm-chart-structure-and-json-schema-references\|helm-chart-structure-and-json-schema-references]].

## `schemas/` Folder

This folder declares which schemas are published to the release. It contains:

- A **symlink** to each chart's `values.schema.json` with a unique name (e.g. `fastcs-values.schema.json`), enabling multiple schemas per repo.
- A **service schema file** (e.g. `fastcs-service.schema.json`) that references the above and validates `values.yaml` in [[notes/epics-containers-page-index\|epics-containers]] services repos where the charts are used as sub-charts. These are hand-coded and rarely change.

## In-Cluster Debugging / Development

The `fastcs` chart exposes two variables:

| Variable | Effect |
|---|---|
| `editable: true` | Creates a PVC; uses the debug container image; copies `/workspaces` and `/venv` into the PVC (editable install of project source); mounts PVC over the corresponding container folders. |
| `autostart: false` | Container starts with `sleep infinity` as PID 1 instead of its normal entrypoint. |

Used together, these enable full in-cluster debug or live editing.

### `ec` CLI Integration

Access is via the `ec` CLI — see [epics-containers/edge-containers-cli#207](https://github.com/epics-containers/edge-containers-cli/issues/207). The script will:

1. Inspect `editable` / `autostart` in the target IOC's `values.yaml`.
2. Port-forward the **debugpy** port (5678) from the pod to localhost.
3. If `editable`: mount the PVC locally via `pv-mounter` and open VSCode to `/workspaces/xxx`.
4. If `autostart: false`: exec into the container and launch debugpy to run the main program.
5. If `autostart: true`: exec into the container and attach debugpy to PID 1.

This allows attaching VSCode to debugpy in the cluster and, when editable, editing source code with live reflection.

A ready-made `launch.json` configuration is provided in [fastcs-example](https://github.com/DiamondLightSource/fastcs-example) and will also land in [[notes/diamondlightsource-copier-template-session\|python-copier-template]] in future.

See also: [[notes/python-debugging-in-cluster\|python-debugging-in-cluster]] and [[notes/debugging-iocs-in-cluster\|debugging-iocs-in-cluster]].
