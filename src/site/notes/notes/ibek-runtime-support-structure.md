---
{"dg-publish":true,"permalink":"/notes/ibek-runtime-support-structure/","title":"Proposed Structure For IBEK Runtime Support","tags":["kind/concept","tech/containers/epics-containers","domain/embedded-systems","domain/software"],"dg-note-properties":{"title":"Proposed Structure For IBEK Runtime Support","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/concept","tech/containers/epics-containers","domain/embedded-systems","domain/software"],"summary":"Design proposal for the ibek-runtime-support directory layout, runtime.yaml schema, and per-beamline soft support module structure.","personal":false}}
---


## Overview

This note captures the proposed structure for **ibek-runtime-support** — the mechanism for defining beamline-specific ("soft") IBEK support modules that sit outside the generic IOC container image.

See also: [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]], [[notes/ibck-runtime-support-directory-structure\|ibck-runtime-support-directory-structure]], [[notes/ibch-runtime-support-helm-shared-schema-architecture\|ibch-runtime-support-helm-shared-schema-architecture]].

---

## Proposed Directory Structure

```
.helm_shared
ibek-runtime-support/
  runtime.schema.json
  gdaPlugins/                  # may be a submodule for shared things
    gdaPlugins.ibek.support.yaml
  i04support/
    i04support.ibek.support.yaml
    i04support.template
services/
  blxxi-di-cam-01/
    Chart.yaml
    config/
      ioc.yaml
      runtime.yaml
      ibek-runtime-support/
        gdaPlugins  # symlink
```

---

## Key Files

### `runtime.yaml`
- Contains entities whose `entity_models` are defined in **ibek-runtime-support**.
- Has a schema line pointing at `ibek-runtime-support/runtime.schema.json`.

### `runtime.schema.json`
- Built by a local script (name TBA) that runs:
  ```
  ibek support generate schema ibek-runtime-support/*/*.ibek.support.yaml
  ```
- Serves as the schema for all `runtime.yaml` files in the beamline.

### `*.ibek.support.yaml`
- Looks like standard ibek support yamls from an ibek support module.
- May refer to database templates in the same folder.
- May also refer to database templates from any support module.
- May also use device support from any support module.

---

## Whiteboard (Tom, 10 Sep 2025)

![[2025_09_10 09_31 Additional Soft Support.jpg\|2025_09_10 09_31 Additional Soft Support.jpg]]

---

## Related

- [[notes/epics-containers-page-index\|epics-containers-page-index]]
- [[notes/ideas-for-migration-of-generic-ioc\|ideas-for-migration-of-generic-ioc]]
- [[notes/argocd-and-epics-containers\|argocd-and-epics-containers]]
- [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]]
- [[notes/ibck-runtime-support-directory-structure\|ibck-runtime-support-directory-structure]]
- [[notes/ibch-runtime-support-helm-shared-schema-architecture\|ibch-runtime-support-helm-shared-schema-architecture]]

> Source: `raw/articles/ibek-runtime-support-structure.md` (originally `giles/Current/work/ec/_Current/How to do extra soft support.md`, dated 2025-11-17)
