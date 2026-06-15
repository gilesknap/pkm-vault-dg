---
{"dg-publish":true,"permalink":"/notes/andor-kernel-driver-rpm-release/","title":"Andor Kernel Driver RPM Release","tags":["kind/how-to","domain/embedded-systems","domain/software","domain/accelerator"],"dg-note-properties":{"title":"Andor Kernel Driver RPM Release","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/how-to","domain/embedded-systems","domain/software","domain/accelerator"],"summary":"Notes on the RHEL8 Andor SDK3 kernel driver RPM release (2-0) and deployment readiness.","personal":false}}
---


## Overview

The Andor SDK3 kernel driver RPM is working and has been re-released with a non-alpha `2-0` tag, along with light documentation of the edits made.

## RPM Location

`/dls_sw/prod/tools/RHEL7-x86_64/andor-sdk3/2-0/prefix/bitflow-rpm/andorsdk3-3.15.30126.1-3.15.30126.1.noarch.rpm`

## Status

- RPM is confirmed working.
- Tagged `2-0` (first non-alpha release).
- Considered safe to deploy to other clients during the shutdown.

## Related

See [[notes/andor3-missing-dependencies-container-troubleshooting\|andor3-missing-dependencies-container-troubleshooting]] for earlier dependency issues encountered during Andor SDK3 bringup.

See [[notes/beamline-control-system-roadmap-whiteboard-2025-2028\|beamline-control-system-roadmap-whiteboard-2025-2028]] for the broader context of deployment work at the beamline.

^[raw/articles/andor-kernel-driver-rpm-release.md]
