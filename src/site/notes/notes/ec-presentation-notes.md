---
{"dg-publish":true,"permalink":"/notes/ec-presentation-notes/","title":"EC Presentation Notes","tags":["kind/reference","tech/containers/epics-containers","kind/presentation","kind/workshop","tech/containers/kubernetes","tech/epics/ioc","tech/containers/helm","tech/devops/ci-cd","domain/networking","domain/embedded-systems","domain/software"],"dg-note-properties":{"title":"EC Presentation Notes","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/reference","tech/containers/epics-containers","kind/presentation","kind/workshop","tech/containers/kubernetes","tech/epics/ioc","tech/containers/helm","tech/devops/ci-cd","domain/networking","domain/embedded-systems","domain/software"],"summary":"Speaker notes and structure for the DLS epics-containers workshop, covering the 6-pillar framework introduction, demo plan, networking, Kubernetes, and IOC instance creation.","personal":false}}
---


## Overview

Prep and speaker notes for the DLS epics-containers workshop — the world's first. Delivered by Giles with Marcell. Session: ~40 min intro, hands-on tutorial (containerised IOCs on laptops), Kubernetes demo of a full test beamline, Q&A.

See also: [[notes/epics-containers-page-index\|epics-containers-page-index]], [[notes/argocd-for-beamlines\|argocd-for-beamlines]], [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]], [[notes/shared-vs-movable-ioc-architecture\|shared-vs-movable-ioc-architecture]], [[notes/ixx-sources-to-ixx-apis-architecture-diagram\|ixx-sources-to-ixx-apis-architecture-diagram]].

---

## Travel Checklist

Headphones, charge watch, log out of pc0116, glasses, mouse, keyboard, cables.

---

## Key Reminders

- Reset agenda — splash page for ORNL after DLS day.
- Add EC to demo list at the end, then delete the beamline.

---

## Introduction Script

DLS: welcome to the world's first epics-containers workshop.

> I'm Giles. I have been working in beamline controls at Diamond Light Source. For the last 3 years I have been mostly looking at containers for IOCs. With me is Marcell who has also been working on epics-containers for the last year.
>
> Today I'm going to introduce the epics-containers framework for 40 mins or so. Then you will get to build some containerised IOCs and run them locally on your laptops. After the break I'm going to demo a fully containerised test beamline to show some of what Kubernetes adds to the equation.
>
> In the end there will be more time to work on the tutorials and we will leave time for questions and discussion.
>
> Please treat this as a taster session. All of the tutorials we will try today and more are available online for you to experience at your leisure. There is a discussion forum and wiki on the GitHub organisation that you can use to get help and make suggestions.

---

## Preamble: Framework Principles

Although this presentation is about how DLS will be doing IOCs, the epics-containers framework is **not tied to Diamond**. Four principles make it widely useful:

1. Everything is open source and modular — standard EPICS build system, unmodified upstream support modules, minimal custom code, widely available FOSS tools.
2. Kubernetes is **not required** — many container benefits apply without it.
3. Currently one active collaborator in Rome running a production beamline entirely in Kubernetes using epics-containers.

---

## What Is epics-containers? (6 Pillars)

1. **Containers** — IOCs built and run inside containers holding all dependencies (system-level included). Supports podman and docker.
2. **Kubernetes** — orchestrates containers; each beamline/accelerator gets its own cluster, balancing resources across servers.
3. **Helm** — most popular Kubernetes package manager; used to deploy IOCs into a cluster. See [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]].
4. **Repositories** — all build/run assets held in source and container repos; replaces shared filesystem software delivery.
5. **CI** (GitHub Actions / GitLab CI) — continuously validates source and generates built assets.
6. **CD via Argo CD** — manages running IOCs via a deployment repository; auto-deploys on push. See [[notes/argocd-for-beamlines\|argocd-for-beamlines]].

This is the same technology stack used by DLS Data Acquisition and Analysis teams; Kubernetes provides a consistent platform for much of future DLS software.

---

## Why?

Because IOCs are decoupled from the OS:

- No need to install system library dependencies on the host.
- Not beholden to host OS version — no mass recompile on Red Hat upgrade day.
- No internal forks of support modules — build vanilla upstream in the container.
- Isolation protects against faults and malicious code (resource limits per IOC).

---

## How: Generic IOCs

**EMPHASISE: Generic IOCs are an important concept of epics-containers.**

### Making an IOC Instance

*INTRO: how easy is it to make a new beamline and add the first IOC to it?*

- Lots of YAML today — because Kubernetes.
- [[notes/ibek-runtime-support-helm-architecture\|ibek]] lets you specify IOC instances in YAML (not mandatory — you may supply your own startup script and substitution file).
- YAML approach enables pre-deploy verification; CI verifies instance config; schema-aware YAML editor guides editing.
- Every IOC instance YAML contains:
  - a name and description
  - a list of entities
- An entity ≈ a line in the builder XML / xeb. A VSCode plugin can edit these in tabular form.
- Example: one entity sets max array bytes, one declares a detector driver.

*SUMMARY: this assumes a generic IOC image (e.g. ADAravis) already exists. If not, one must be created first.*

---

## Networking

Containers run in a virtual container network by default — traffic goes through NAT, which is incompatible with some protocols, most notably **Channel Access**.

The tutorials configure networking to work around this; the diagram in the slides represents the tutorial networking setup.

See [[notes/wireshark-docker-bridge-vs-loopback-search-protocol\|wireshark-docker-bridge-vs-loopback-search-protocol]] for related network debugging notes.

---

## Kubernetes

Tutorials use docker compose to deploy IOCs locally. Compose is viable in production but requires manual SSH and per-server compose files. Kubernetes is the dominant orchestration tool for automating this at scale.

Related: [[notes/shared-vs-movable-ioc-architecture\|shared-vs-movable-ioc-architecture]], [[notes/combine-ixx-deployment-and-ixx-services\|combine-ixx-deployment-and-ixx-services]].

---

## Raw Source

Migrated from `raw/articles/ec-presentation-notes.md` (original path: `giles/Reference/Tech Info/Ec Presentation Notes.md`).
