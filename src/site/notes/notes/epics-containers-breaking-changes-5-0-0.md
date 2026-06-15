---
{"dg-publish":true,"permalink":"/notes/epics-containers-breaking-changes-5-0-0/","title":"EPICS Containers 5.0.0 Breaking Changes Update Guide","tags":["tech/containers/epics-containers","tech/containers/helm","tech/containers/kubernetes","kind/how-to","kind/breaking-changes"],"dg-note-properties":{"title":"EPICS Containers 5.0.0 Breaking Changes Update Guide","type":"note","created":"2026-06-01","updated":"2026-06-02","source":"import","tags":["tech/containers/epics-containers","tech/containers/helm","tech/containers/kubernetes","kind/how-to","kind/breaking-changes"],"summary":"Step-by-step guide for updating beamline services repos to EPICS Containers 5.0.0, covering copier update, shared values.yaml, and Panda IOC values changes.","personal":false}}
---


# EPICS Containers 5.0.0 Breaking Changes Update Guide

Examples here are for p47. See also [[notes/epics-containers-page-index\|epics-containers-page-index]] for broader context and [[notes/helm-chart-structure-and-json-schema-references\|helm-chart-structure-and-json-schema-references]] for schema details.

## How to Update to 5.0.0

Start with:
```bash
cd p47-services
uvx copier update -A
```

Manage any merge conflicts, but don't worry about `services/values.yaml` — just make it look like the [Shared services/values.yaml](#shared-servicesvaluesyaml) section below. Also update any Panda IOC values as shown in [All Panda values.yaml](#all-panda-valuesyaml).

## Shared services/values.yaml

Should look like this for beamlines:
```yaml
# yaml-language-server: $schema=../.helm-shared/values.schema.json

# shared values for all services
global:
  domain: p47
  location: bl47p
  enabled: true

# shared values for IOCs
shared: &shared
  hostNetwork: true

  # running as ixxdetector account uid,gid gives access to /dls/ixx/data
  podSecurityContext:
    runAsUser: 37988
    runAsGroup: 37988

# shared settings for all IOCs using the ioc-instance helm chart
ioc-instance:
  <<: *shared

# shared setting for all legacy IOCs using the dev-c7 helm chart
dev-c7:
  <<: *shared
```

Key points:
- `global.domain` and `global.location` identify the beamline.
- `hostNetwork: true` is set via the YAML anchor and merged into both `ioc-instance` and `dev-c7` chart defaults.
- `podSecurityContext` sets `runAsUser`/`runAsGroup` to the `ixxdetector` account for `/dls/ixx/data` access.
- Schema is pulled from `../.helm-shared/values.schema.json` — see [[notes/ibch-runtime-support-helm-shared-schema-architecture\|ibch-runtime-support-helm-shared-schema-architecture]] for how the shared schema is structured.

## All Panda values.yaml

Update as follows:
```yaml
# yaml-language-server: $schema=https://github.com/epics-containers/ec-helm-charts/releases/download/3.4.4/ioc-instance.schema.json#/$defs/service

ioc-instance:
  image: ghcr.io/pandablocks/pandablocks-ioc:0.11.5

  args:
    - bash /epics/ioc/config/start.sh

  # don't use livenessProbe, preStop which dont exist in panda image.
  livenessExecutable: ""
  preStopExecutable: ""
```

Key points:
- Schema pin is to `ioc-instance.schema.json` at chart release `3.4.4`.
- `livenessExecutable` and `preStopExecutable` must be explicitly blanked because the PandaBlocks IOC image does not ship those executables.
- `args` passes the start script directly — no wrapper entrypoint.

## Related

- [[notes/epics-containers-page-index\|epics-containers-page-index]]
- [[notes/helm-chart-structure-and-json-schema-references\|helm-chart-structure-and-json-schema-references]]
- [[notes/ibch-runtime-support-helm-shared-schema-architecture\|ibch-runtime-support-helm-shared-schema-architecture]]
- [[notes/ioc-instance-and-fastcs-helm-chart-overlap\|ioc-instance-and-fastcs-helm-chart-overlap]]
- [[notes/epics-iocs-cluster-host-network-false\|epics-iocs-cluster-host-network-false]]

> Source: `raw/articles/epics-containers-5-0-0-breaking-changes-update-guide.md`
