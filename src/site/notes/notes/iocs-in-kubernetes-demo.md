---
{"dg-publish":true,"permalink":"/notes/iocs-in-kubernetes-demo/","title":"IOCs in Kubernetes Demo","tags":["kind/how-to","tech/containers/epics-containers","domain/software","domain/embedded-systems"],"dg-note-properties":{"title":"IOCs in Kubernetes Demo","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/epics-containers","domain/software","domain/embedded-systems"],"summary":"Live walkthrough demo of EPICS IOCs running in Kubernetes on the p47 training beamline, covering Phoebus, Grafana/Prometheus, StackRox, and ArgoCD.","personal":false}}
---


## Overview

A live demo script walking through EPICS IOCs orchestrated in Kubernetes, using the p47 training beamline at Diamond Light Source. Prepared for the [[Workshop at ISIS 2025\|Workshop at ISIS 2025]] / [[Demos EPICS Collaboration Meeting 2025\|Demos EPICS Collaboration Meeting 2025]].

Source: `raw/articles/iocs-in-kubernetes-demo.md`

---

## Training Beamline p47

One of DLS's training beamlines, used for training on the hardware-triggered scanning stack. Fully converted to Kubernetes; serves as a test bed for the containerised stack. Components:

- A PMAC clipper motion controller
- Two motors driving a sample stage
- A PandaBox
- A GigE sample camera
- A GigE overview camera

GitHub project: `p47-services`

---

## Phoebus

The overview screen is hand-coded; all engineering screens behind it are **auto-generated and served over HTTP from the cluster**.

- Launch: `./opi/phoebus-launch.sh`
- Synoptic exposes start/stop controls for cameras and motors.
- Engineering screens are served over HTTP (visible in the tab URL).

See also: [[ArgoCD for Beamlines\|ArgoCD for Beamlines]]

---

## Kubernetes

Container orchestration platform; dominant in industry. Open-sourced by Google in 2014, now managed by the **Cloud Native Computing Foundation (CNCF)** under the Linux Foundation.

Core orchestration tasks:
- Launch IOCs on appropriately resourced servers
- Monitor status
- Collect logs
- Attach to IOC console
- Deliver alerts

---

## Pollux Cluster — Landing Page

- URL: [http://pollux.diamond.ac.uk](http://pollux.diamond.ac.uk)
- **Staging cluster** for the main central cluster; shared by test/training beamlines.
- Production beamlines have dedicated clusters.
- Cluster setup managed via Ansible by the DLS cloud team.

### Installed Services

| Service | Purpose |
|---|---|
| **Grafana** | Time-series dashboards for cluster status |
| **Prometheus** | Time-series DB; data source for Grafana |
| **Alert Manager** | Threshold-based alerting |
| **Dashboard** | Browse/manage K8S resources |
| **KeyCloak** | Central Authentication Service |
| **StackRox** | Security monitoring; CVE detection |
| **Kyverno** | Fine-grained policy management |

---

## Kubernetes Dashboard

Namespace in focus: `p47-beamline`.

### Pods

One pod per IOC device:
- 1× PandaBox IOC
- 2× camera IOCs
- 1× motion IOC
- `p47-gateways` — PVA + CA gateway (two containers; used here for VPN connectivity)
- `epics-opis` — HTTP server for auto-generated engineering screens
- Additional pods for Bluesky DAQ beamline-local services

IOC pods are scheduled to the **specific node** with hardware NICs attached; other pods run on generic data-centre nodes.

### Deployments & StatefulSets

Pods are wrapped in Deployments or StatefulSets. If a pod is deleted the owning Deployment auto-recreates it. Live instance logs are accessible from here.

### PVCs (Persistent Volume Claims)

- Each IOC gets a private data area.
- Shared volumes mounted by all IOCs.
- **`p47-opi-claim`**: IOCs write auto-generated screens here (each to its own subfolder); `epics-opis` mounts root read-only and serves all screens over HTTP.
- A debug service (`epics-pvcs-xxx`) mounts all shared PVCs under `/mounts` for interactive inspection (e.g. restoring autosave files).

### Services & Ingresses

IOCs run on the host network and don't need a Service. `epics-opis` does:
- Ports 443 and 80 forwarded to internal cluster address.
- Ingress exposes the URL externally.
- Multiple pods can be added to the Deployment for load-balancing.

### ConfigMaps

IOC instance configuration is supplied via ConfigMap, mounted as a directory into the container filesystem (e.g. `bl47p-mo-ioc-01`). This is what makes a generic IOC container into a specific IOC.

See also: [[Helm Chart Structure and JSON Schema References\|Helm Chart Structure and JSON Schema References]]

---

## Grafana / Prometheus

- **Dashboard**: Node Exporter / Nodes → select `bl47p-ea-serv-01`
- Shows CPU, memory, network stats for the node.
- Selectable time range (e.g. last 7 days reveals PVA image transmission network spikes).
- Per-pod view: K8S / Compute Resources / Namespace (Pods) → `p47-beamline` — useful for tracking memory leaks.

---

## StackRox

Live security monitoring: scans running containers for **Common Vulnerabilities and Exploits (CVEs)**; alerts when risk thresholds are exceeded.

Demo findings for `p47-beamline` on Pollux:
- `ioc-adaravis` flagged for `xz-utils` dependency (known backdoor injected into SSH in 2024) — however the exploit never shipped in released Linux distro versions, so no live risk.
- `netcdf` library has moderate/low issues only; no Critical/Important flags.

**Key takeaways:**
1. Only use released versions of distros and libraries.
2. Run StackRox (or equivalent) to get alerts when vulnerabilities appear.
3. Containers give additional protection: isolated, single-process, no SSH server.

---

## ArgoCD

Continuous deployment: defines all beamline services in a Git repo; ArgoCD monitors the repo and **synchronises cluster state to match**.

- UI: [https://argocd.diamond.ac.uk/applications?proj=p47-beamline](https://argocd.diamond.ac.uk/applications?showFavorites=false&proj=p47-beamline)
- Each card = one ArgoCD Application (e.g. `bl47p-dcam-01`).
- Child resources visible per Application: ConfigMap, PVC, StatefulSet, Pod.
- From the Pod: view logs, open a shell.

### Application Fields

| Field | Meaning |
|---|---|
| Repository | Repo defining the service |
| Target Revision | Tag or branch to track |
| Path | Subpath in repo |
| Destination | Cluster to deploy to |
| Namespace | Namespace to deploy to |

Most IOCs track a **specific tag** (no changes until explicitly updated). During active development a **branch** (e.g. `workshop2025`) is tracked so changes sync automatically.

### Services Repo (`p47-services`)

Each service has:
- `values.yaml` — Kubernetes deployment definition
- `config/ioc.yaml` — EPICS runtime assets

Example: `ioc.yaml` for `bl47p-ea-dcam-01` specifies `MAX_ROIS=4`; Phoebus engineering screens reflect exactly 4 ROIs. Changing to `MAX_ROIS=8` and pushing to `workshop2025` triggers an ArgoCD sync.

### Discoverability & GitOps Benefits

- Single source of truth for what is running on the beamline.
- Full history of what was running at any past point.
- Chain of discovery from ArgoCD → repo → every line of source code.
- Beamline can be restored to any historical state.
- Full delete-and-restore is possible, even in a live demo.

**Restore procedure (after deleting ArgoCD root):**
```bash
module load argus
cd p47-deployment
kubectl apply -f apps.yaml -n p47-beamline
```
PVCs are the slow step; other services spring up once PVCs are ready.

See also: [[ArgoCD for Beamlines\|ArgoCD for Beamlines]], [[EPICS Containers Page Index\|EPICS Containers Page Index]]

---

## Related Notes

- [[Demos EPICS Collaboration Meeting 2025\|Demos EPICS Collaboration Meeting 2025]]
- [[Workshop at ISIS 2025\|Workshop at ISIS 2025]]
- [[EPICS Containers Page Index\|EPICS Containers Page Index]]
- [[ArgoCD for Beamlines\|ArgoCD for Beamlines]]
- [[Helm Chart Structure and JSON Schema References\|Helm Chart Structure and JSON Schema References]]
- [[EPICS IOCs Cluster Host Network False\|EPICS IOCs Cluster Host Network False]]
- [[Read Only Access Beamline Namespaces\|Read Only Access Beamline Namespaces]]
