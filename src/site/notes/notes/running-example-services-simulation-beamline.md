---
{"dg-publish":true,"permalink":"/notes/running-example-services-simulation-beamline/","title":"Running Example Services Simulation Beamline","tags":["kind/how-to","tech/containers/epics-containers","domain/software","domain/embedded-systems"],"dg-note-properties":{"title":"Running Example Services Simulation Beamline","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/epics-containers","domain/software","domain/embedded-systems"],"summary":"Step-by-step setup and run instructions for the epics-containers example-services simulation beamline across Mac, WSL2, Debian, and RPM Linux.","personal":false}}
---


## Overview

How to get the [`epics-containers/example-services`](https://github.com/epics-containers/example-services) simulation beamline running on different platforms using Docker or Podman.

Related: [[notes/podman-docker-compose-how-to\|podman-docker-compose-how-to]] · [[notes/demos-epics-collaboration-meeting-2025\|demos-epics-collaboration-meeting-2025]] · [[notes/minikube-with-podman-outside-docker\|minikube-with-podman-outside-docker]] · [[notes/epics-containers-page-index\|epics-containers-page-index]]

> Source: `raw/articles/running-example-services-simulation-beamline.md`

---

## Platform Detection

```bash
uname   # mac → Darwin
```

---

## Setup by Platform

### Any Linux with Docker installed

No setup required.

### WSL2 (any Linux distro)

Docker Desktop → Settings → Resources → WSL Integration → **Enable** → Apply and Restart.

### Mac

Install: `podman`, `podman-compose`, `xquartz`.

```bash
export DISPLAY="$(/usr/sbin/ipconfig getifaddr en0):0"
```

### Debian Linux (no Docker)

```bash
sudo apt install git podman docker-compose-v2
systemctl enable --user podman.socket --now
```

### RPM Linux (no Docker)

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
sudo dnf install git docker-compose-plugin
systemctl enable --user podman.socket --now
```

### Podman Integration with Compose

```bash
systemctl enable --user podman.socket --now
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/podman/podman.sock
```

---

## Run

```bash
git clone https://github.com/epics-containers/example-services.git
cd example-services
source ./environment.sh
docker compose up -d
```

---

## Troubleshooting

### Docker user permission failure

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot
```
