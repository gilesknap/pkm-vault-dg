---
{"dg-publish":true,"permalink":"/notes/debugging-pvxs/","title":"Debugging PVXS","tags":["kind/how-to","tech/containers/epics-containers","domain/embedded-systems","domain/software","tech/epics/pvxs","kind/debugging","tech/containers/podman"],"dg-note-properties":{"title":"Debugging PVXS","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/epics-containers","domain/embedded-systems","domain/software","tech/epics/pvxs","kind/debugging","tech/containers/podman"],"summary":"How-to guide for building, running, and packet-capturing PVXS inside a Podman container for debugging PVA client/server networking issues.","personal":false}}
---


How-to guide for debugging [[notes/pvxs-python-pvget\|PVXS]] inside an [[raw/articles/epics-containers\|epics-containers]] development container, covering build setup, IOC launch, client constraints, packet capture, and manual startup-script editing.

> **Context:** Notes from the [[notes/demos-epics-collaboration-meeting-2025\|EPICS Collaboration Meeting Workshop 2025]]. Uses Michael Davidsaver's `nat-traversal` development fork of PVXS.

## Get the code

```bash
# Michael's development fork of pvxs
git clone git@github.com:mdavidsaver/pvxs-dev -b nat-traversal
# some example IOC instance definitions
git clone git@github.com:epics-containers/example-services
```

## Launch an example IOC

First build PVXS inside the container:

```bash
$ podman run --security-opt label=disable \
    -p5064:5064/udp -p5064:5064/tcp -p5075:5075/tcp -p5076:5076/udp \
    -itv $(pwd)/pvxs-dev:/epics/support/pvxs \
    -v $(pwd)/example-services/services/bl01t-ea-test-01/config/:/epics/ioc/config \
    --rm ghcr.io/epics-containers/ioc-generic-developer:2025.4.2b1

cd /epics/support/pvxs
ln -s /epics/support/configure/RELEASE configure/RELEASE.local
make clean; make

/epics/ioc/start.sh
```

## Launch a Client on a separate machine

Copy the built pvxs over to the IOC machine first.

> ⚠️ The client **must** run on a separate machine: Podman has already bound to port `5076` above (without `SO_REUSEPORT`), so a PVA client on the same host will collide. CA is fine because it uses distinct client/server ports.

```bash
$ podman run --security-opt label=disable \
    --network host \
    -itv $(pwd)/pvxs-dev:/epics/support/pvxs \
    -v $(pwd)/example-services/services/bl01t-ea-test-01/config/:/epics/ioc/config \
    --rm ghcr.io/epics-containers/ioc-generic-developer:2025.4.2b1

./bin/linux-x86_64/pvxget bl01t:SUM
caget bl01t:SUM
```

## Known issue: PVXS + Podman 4.9.4 client timeout

At the time of writing PVXS did **not** work in this scenario under Podman 4.9.4 — the client simply times out. See upstream fix: [epics-base/pvxs#116](https://github.com/epics-base/pvxs/pull/116).

Michael's NAT-traversal fix works in general and is fine in Docker, but fails in Podman 4.9.4. The packet traces below (Docker left, Podman 4.9.4 right) illustrate the divergence:

![[Pasted image 20250917081602.png\|Pasted image 20250917081602.png]]

Related networking context: [[notes/epics-iocs-cluster-host-network-false\|epics-iocs-cluster-host-network-false]] and [[notes/wireshark-docker-bridge-vs-loopback-search-protocol\|wireshark-docker-bridge-vs-loopback-search-protocol]].

## Packet capture inside the container network namespace

```bash
# find the process id of the IOC in the container
pgrep ioc
# enter the NETWORK namespace (replace 3098117 with actual PID)
sudo nsenter -t 3098117 -n
# now run tshark / tcpdump as normal
# Note: only the network namespace changes — host filesystem is still accessible
```

## Manual edits to the example IOC startup script

To bypass the epics-containers framework, after the first run of `start.sh` a fully standard startup script is written to `/epics/runtime/st.cmd`. Edit it with vim, then execute directly:

```bash
/epics/ioc/bin/linux-x86_64/ioc /epics/runtime/st.cmd
```

## See also

- [[notes/debugging-iocs-in-cluster\|debugging-iocs-in-cluster]] — debugging IOCs running inside a Kubernetes cluster
- [[notes/podman-docker-compose-how-to\|podman-docker-compose-how-to]] — general Podman/Docker Compose patterns
- [[notes/running-example-services-simulation-beamline\|running-example-services-simulation-beamline]] — running the full example-services stack
