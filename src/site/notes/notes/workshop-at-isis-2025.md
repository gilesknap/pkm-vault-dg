---
{"dg-publish":true,"permalink":"/notes/workshop-at-isis-2025/","title":"Workshop at ISIS 2025","tags":["kind/how-to","tech/containers/epics-containers","domain/accelerator","domain/software","domain/embedded-systems"],"dg-note-properties":{"title":"Workshop at ISIS 2025","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/how-to","tech/containers/epics-containers","domain/accelerator","domain/software","domain/embedded-systems"],"summary":"Planning notes and demo outline for the EPICS Containers workshop at the ISIS EPICS Collaboration Meeting, September 2025.","personal":false}}
---


# Workshop at ISIS 2025

Scheduled **Thursday 09:30–11:00** and **11:15–12:45** (3 hours total).

- Indico event: [https://indico.stfc.ac.uk/event/1233/timetable/#20250410](https://indico.stfc.ac.uk/event/1233/timetable/#20250410)
- Event home: [https://indico.stfc.ac.uk/event/1233/](https://indico.stfc.ac.uk/event/1233/)

Raw source: [[raw/articles/workshop-at-isis-2025\|raw/articles/workshop-at-isis-2025]]

## Outline

- **1 hour talk**
  - experiment `example-services` for everyone
  - demo example-services briefly
- Q&A 30 mins
- Break
- **1 hour demos**
  - `example-services`
  - dev-container
  - Kubernetes beamline (esp. [[notes/argocd-for-beamlines\|ArgoCD]])
- Q&A and/or experiment with `example-services`

## Changes to Slides

- Ansible instead of `install.sh` (see [[notes/firewalld-ansible-cluster\|firewalld-ansible-cluster]] for Ansible patterns)
- More explanation of *why* we do YAML for each of: Ansible, support, IOC
- Better WHY — one slide for containers, another for K8S

## example-services Demo

- Easy to launch
- Isolated PVs
  - Accessible locally
  - Using 2 gateways
  - Auto-generated screens (mounted local FS)

## Dev Container Demo

Show ADSimDetector and `example-services`.

- All dev environment inside — no shared filesystems
- Everything compiled from source
- Local reproduction of the CI that built the container
- Show EPICS inside and `ioc-adsimdet`, `example-services` outside
- Can make changes to instance without recompile
- Auto-generated screens
- Autosave?
- Isolated PVs
- Port forward to Phoebus
- Can rebuild support modules
- Can test changes to [[notes/ibek-runtime-support-helm-architecture\|ibek]] support
- Show port forwards — use `example-services` `ioc.yaml` (now matches dev container)
  - Port 8080 is ffmpeg

## K8S Beamline Demo

- Pull notes from last workshop
- Dashboard — show what epics-containers uses:
  - Deployment, Service, StatefulSet, Pods, PVCs
- Auto-generated screens (over HTTP)

## Issues with DLS Legacy

Context for the *why* slides — see also [[notes/vacvalve-vs-dlsplc\|vacvalve-vs-dlsplc]] and [[notes/epics-containers-breaking-changes-5-0-0\|epics-containers-breaking-changes-5-0-0]].

- Local changes to templates mean we are essentially closed source
- Shared filesystem with network of dependencies makes it hard to run IOCs offsite
- Dependency issues create extra work when updating base support modules, requiring multiple releases through the build system
- Home-grown build system is single-threaded and cumbersome
- Recompile of thousands of IOCs required when OS upgrades happen
