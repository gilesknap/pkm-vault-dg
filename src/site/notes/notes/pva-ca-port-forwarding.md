---
{"dg-publish":true,"permalink":"/notes/pva-ca-port-forwarding/","title":"PVA CA and Port Forwarding","tags":["kind/troubleshooting","domain/software","domain/networking","tech/epics/pvxs","tech/epics/pva","tech/epics/ca","tech/containers/epics-containers","tech/containers/podman","tech/net/port-forwarding","tools/vscode"],"dg-note-properties":{"title":"PVA CA and Port Forwarding","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/troubleshooting","domain/software","domain/networking","tech/epics/pvxs","tech/epics/pva","tech/epics/ca","tech/containers/epics-containers","tech/containers/podman","tech/net/port-forwarding","tools/vscode"],"summary":"How PVA and CA behave differently under NAT/port-forwarding, and how name-server mode plus VSCode auto-forwarding resolves the issues in epics-containers.","personal":false}}
---


## PVA under NAT

With [[pvxs\|PVA]] you are compelled to set `EPICS_PVA_NAME_SERVERS=127.0.0.1` because the UDP handshake sets up a new ephemeral port for the TCP channel, and that ephemeral port fails to traverse NAT.

This is demonstrated to work with podman 4.9.4 and podman 5.0.3 in the [pva-demo release](https://github.com/epics-containers/pva-demo/releases/tag/2024.11.3.keep).

## CA under NAT

For [[notes/epics-iocs-cluster-host-network-false\|CA]] you can use UDP for the initial connection and only bind the container's port `5064` to localhost, limiting responses to local-host clients only.

> **Note:** CA is using UDP unicast to `5064` in this setup. This means only one IOC (or gateway) per IP address can bind to it.

### Non-standard external port mapping

Mapping a different external port for CA (e.g. `podman -p 11064:5064/udp -p 11064-11065:5065-5065`) **fails** — error details to be recorded.

This opens up using `EPICS_CA_NAME_SERVERS=127.0.0.1`, which appears to work the same way as the PVA name-server approach. This would keep the number of required environment variables down, but it diverges from traditional IOC practice — proceed with caution.

## VSCode Auto Port Forwarding

VSCode's automatic port forwarding works well here and avoids the repeated *port is in use* errors that static `devcontainer.json` port mappings create.

- Works with **CA name-server** mode.
- Works with **PVA** — the current version of `example-services` uses this, as does the gateways pod for `p47-beamline`.

See also: [[notes/debugging-iocs-in-cluster\|debugging-iocs-in-cluster]] and [[notes/fastcs-charts-deployment-and-debugging\|fastcs-charts-deployment-and-debugging]].
