---
{"dg-publish":true,"permalink":"/notes/epicsdbbuilder-integration-plan/","title":"Epicsdbbuilder Integration Plan","tags":["kind/concept","domain/embedded-systems","tech/containers/epics-containers","tech/epics/ibek","tech/epics/epicsdbbuilder"],"dg-note-properties":{"title":"Epicsdbbuilder Integration Plan","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"slack","tags":["kind/concept","domain/embedded-systems","tech/containers/epics-containers","tech/epics/ibek","tech/epics/epicsdbbuilder"],"summary":"Plan for integrating the epicsdbbuilder Python library into ibek as a new entity type, generating DB files from IOC instance scripts with access to the ibek entity object tree.","personal":false}}
---


## Overview

Plan for integrating [`epicsdbbuilder`](https://github.com/epicsdbbuilder) into [[raw/articles/epics-containers\|epics-containers]] and [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]].

## Design

- A new **entity type** is added to [[notes/ibek-runtime-support-helm-architecture\|ibek]].
- The entity accepts:
  - A **Python script** sourced from the IOC instance config folder (i.e. supplied per IOC instance, not per runtime support image).
  - An **output path** for the generated `.db` file.
- The script runs **within the ibek process** and is given access to the **ibek entity object tree**, so it can inspect which devices have been declared.
- The script's sole responsibility is to produce a `.db` file using the `epicsdbbuilder` library.

## Scope

| Concern | In scope |
|---|---|
| DB file generation | ✅ |
| Access to ibek entity tree | ✅ |
| Replacing substitution/template DBs | ❌ (additive) |

## Related

- [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]]
- [[notes/shared-vs-movable-ioc-architecture\|shared-vs-movable-ioc-architecture]]
- [[notes/vae-builder-ibek-rtems-ioc-migration\|vae-builder-ibek-rtems-ioc-migration]]

> Source captured: raw/articles/epicsdbbuilder-integration-plan.md
