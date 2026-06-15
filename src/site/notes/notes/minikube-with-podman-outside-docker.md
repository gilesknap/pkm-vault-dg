---
{"dg-publish":true,"permalink":"/notes/minikube-with-podman-outside-docker/","title":"Minikube with Podman Outside Docker","tags":["kind/how-to","domain/software","domain/embedded-systems","tech/containers/epics-containers"],"dg-note-properties":{"title":"Minikube with Podman Outside Docker","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","domain/software","domain/embedded-systems","tech/containers/epics-containers"],"summary":"Step-by-step troubleshooting notes for running Minikube inside a Podman-backed container without a native Docker daemon.","personal":false}}
---


## Overview

Notes on getting [[Minikube\|Minikube]] to start inside a Podman-backed container where no native Docker daemon is present. The core problem is that Minikube detects the Podman socket as a Docker socket but rejects the version string (`4.9.4-rhel`) as below its minimum requirement.

Related context: [[notes/argocd-for-beamlines\|argocd-for-beamlines]], [[notes/debugging-iocs-in-cluster\|debugging-iocs-in-cluster]], [[notes/epics-containers-page-index\|epics-containers-page-index]], [[notes/podman-docker-compose-how-to\|podman-docker-compose-how-to]].

---

## Setup: mount Podman socket into a container

Mount the host Podman socket as `/var/run/docker.sock` so tooling that expects a Docker socket finds something:

```bash
podman run --privileged -it \
  -v /run/user/1200288/podman/podman.sock:/var/run/docker.sock \
  ubuntu
```

---

## Install Docker CLI + Minikube inside the container

```bash
apt update; apt install -y curl

# Docker CLI (static binary — no daemon needed)
curl -LO https://download.docker.com/linux/static/stable/x86_64/docker-28.1.1.tgz
tar xf docker-28.1.1.tgz
mv docker/docker /usr/bin
rm -r docker

# Minikube
curl -LO https://github.com/.../minikube-linux-amd64
install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

---

## Problem: Minikube rejects Podman's version string

Running `minikube start` fails immediately:

```
* Unable to pick a default driver.
  - docker: Not healthy: docker version is less than the minimum required
  - docker: Suggestion: Upgrade Docker … current version is 4.9.4-rhel
    https://minikube.sigs.k8s.io/docs/drivers/docker/#requirements
X Exiting due to DRV_DOCKER_NOT_RUNNING: Found docker, but the docker service
  isn't running.
```

Minikube sees `4.9.4-rhel` (the Podman server's version) and considers it below v18.09.0.

---

## Fix: switch Minikube to the Podman driver

Install `podman` inside the container and point Minikube at it directly:

```bash
apt install podman

minikube config set rootless true
minikube config set driver podman

# Tell Podman CLI to talk to the socket we mounted
export CONTAINER_HOST=/var/run/docker.sock

# --force because we appear to be root inside the container
minikube start --force
```

This gets further — inspect logs from a second shell:

```bash
minikube logs
```

---

## Status / Next steps

- `minikube start --force` progresses past driver detection but may surface further issues — see `minikube logs` output.
- Investigate whether `CONTAINER_HOST` should point at the native in-container Podman socket instead.
- Cross-reference [[notes/iocs-in-kubernetes-demo\|iocs-in-kubernetes-demo]] for cluster bring-up context.

---

> Source: `raw/articles/minikube-with-podman-outside-docker.md`
