---
{"dg-publish":true,"permalink":"/notes/adexternal-integration-gige-ioc/","title":"ADExternal Integration for GigE IOC","tags":["kind/concept","tech/containers/epics-containers","domain/embedded-systems","domain/software"],"dg-note-properties":{"title":"ADExternal Integration for GigE IOC","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/concept","tech/containers/epics-containers","domain/embedded-systems","domain/software"],"summary":"Plan and architecture notes for converting Diamond's GigE IOC to ibek using ADExternal and ZMQ-based plugins.","personal":false}}
---


## Overview

Initial goal: convert Alex Wells' GigE IOC to [[ibek\|ibek]]. Requires:
- Auto-conversion of XML templates
- ADExternal built into ioc-adaravis

Related project context: [[notes/epics-containers-page-index\|epics-containers-page-index]]

## Key Repositories

- **GIGE-BUILDER**: https://gitlab.diamond.ac.uk/controls/support/GIGE-BUILDER
  - Branch with complex XML chaining: `more_cameras` e.g. `BR01C-DI-CAM-03.xml` is the top of a chain of 4 XML includes.
- **ADExternal**: https://gitlab.diamond.ac.uk/controls/support/adexternal
- **Workers (python)**: https://gitlab.diamond.ac.uk/controls/support/adexternal/-/tree/master/worker/python

## Builder Project on Filesystem

Do not change this path: `file:///dls_sw/work/R3.14.12.7/support/GIGE-BUILDER/`

## Architecture

Each ADExternal plugin has a single **worker type** connected via a **unix socket**.

- `builder.py` sets up one worker per instance by default; the architecture supports multiple workers for load balancing (start more than one).
- **Frame data** is passed via **shared memory**; metadata and sync signals go over the socket.
- `builder.py` generates a workers startup script that launches one of each ADExternal instance's worker script.
- The `ADExternal` builder object takes `CLASS_NAME` which is both the class name and the Python module name. Appending `.py` gives the script to launch.
- Workers are assumed to be Python; `builder.py` expects `<classname>.py` to exist in the worker directory.

## ZMQ Integration

Two of the IOCs use a **ZMQ wrapper** to send Gaussian Fit Results to a Python soft IOC that calculates **Emittance**. Ask Alex Wells which two IOCs these are.

## Source

^[raw/articles/adexternal-integration-gige-ioc.md]
