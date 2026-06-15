---
{"dg-publish":true,"permalink":"/notes/ibek-rtems-ioc-build-process/","title":"IBEK RTEMS IOC Build Process","tags":["kind/concept","domain/embedded-systems","domain/controls","tech/epics/ibek","tech/hardware/rtems","tech/epics/ioc","tech/epics/builder","kind/architecture","kind/deployment"],"dg-note-properties":{"title":"IBEK RTEMS IOC Build Process","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"slack","tags":["kind/concept","domain/embedded-systems","domain/controls","tech/epics/ibek","tech/hardware/rtems","tech/epics/ioc","tech/epics/builder","kind/architecture","kind/deployment"],"summary":"Whiteboard diagram and notes covering the proposed build and deployment pipeline for RTEMS IOCs using VAE Builder and IBEK, including XML-to-ioc.yaml conversion and TFTP/NFSv2 delivery.","personal":false}}
---


## Overview

This note captures the proposed architecture and process for building and deploying IOCs targeting the [[RTEMS\|RTEMS]] real-time OS using the VAE Builder and [[notes/ibek-runtime-support-helm-architecture\|IBEK]] toolchain.

The pipeline converts legacy XML IOC descriptions to `ioc.yaml` format and deploys the resulting IOC image to target hardware via TFTP and NFSv2.

## Whiteboard Diagram

![[20260211_093544_IMG_20260211_092949.jpg\|20260211_093544_IMG_20260211_092949.jpg]]

The diagram illustrates:
- **Input**: XML IOC descriptions → converted to `ioc.yaml` by the VAE Builder
- **Build**: IBEK consumes `ioc.yaml` to produce a configured IOC binary/image
- **Deployment**: Finished IOC pushed to target RTEMS hardware over TFTP/NFSv2

## Related Work

See [[notes/vae-builder-ibek-rtems-ioc-migration\|vae-builder-ibek-rtems-ioc-migration]] for the migration plan from legacy XML to this pipeline, and [[notes/ibck-runtime-support-directory-structure\|ibck-runtime-support-directory-structure]] for the on-disk layout expected by IBEK at runtime.

The broader roadmap context is captured in [[notes/beamline-control-system-roadmap-whiteboard-2025-2028\|beamline-control-system-roadmap-whiteboard-2025-2028]].

^[raw/articles/ibek-rtems-ioc-build-process.md]
