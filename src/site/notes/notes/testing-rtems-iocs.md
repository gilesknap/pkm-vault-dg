---
{"dg-publish":true,"permalink":"/notes/testing-rtems-iocs/","title":"Testing RTEMS IOCs","tags":["kind/concept","domain/embedded-systems","domain/controls","tech/hardware/rtems","tech/containers/epics-containers","kind/testing","tech/containers/kubernetes"],"dg-note-properties":{"title":"Testing RTEMS IOCs","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/concept","domain/embedded-systems","domain/controls","tech/hardware/rtems","tech/containers/epics-containers","kind/testing","tech/containers/kubernetes"],"summary":"Step-by-step guide for configuring, building, and testing RTEMS IOCs using rtems-proxy, TFTP/NFS, and the DLS dev network crates.","personal":false}}
---


also see [Diamond Confluence: RTEMS IOC Testing](https://confluence.diamond.ac.uk/x/s4OfCg)

Related: [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]] · [[notes/vae-builder-ibek-rtems-ioc-migration\|vae-builder-ibek-rtems-ioc-migration]] · [[notes/githublab-issues-rtems\|githublab-issues-rtems]] · [[notes/test-crate-ts-io547\|test-crate-ts-io547]] · [[notes/epics-containers-page-index\|epics-containers-page-index]]

## Settings

Example settings for a test crate (e.g. `bl04i-ea-ioc-01`). These go into `rtems-proxy` configuration files.

### `global values.yaml`

```yaml
# For testing on the Controls Dev Network Crates
- name: RTEMS_TFTP_PATH
  value: /nfsv2-tftp
- name: RTEMS_IOC_GATEWAY
  value: <IOC_GATEWAY_IP>
- name: RTEMS_IOC_NETMASK
  value: 255.255.240.0
- name: RTEMS_NFS_IP
  value: <NFS_TFTP_IP>
- name: RTEMS_TFTP_IP
  value: <NFS_TFTP_IP>
```

### Individual IOC `values.yaml`

```yaml
# For testing on the Controls Dev Network Crate
- name: RTEMS_IOC_IP
  # test crate in G13 Rack 4 bottom
  value: <IOC_IP>
- name: RTEMS_CONSOLE
  value: <console-host>:7027
```

## Setup Proxy

```bash
rtems-proxy dev /workspaces/i04-services bl04i-ea-ioc-01
source /tmp/dev_proxy.sh
```

This copies the latest binaries from the NFS server to the `/tftp` folder.

## How rtems-proxy Works

The `rtems-proxy` tool is installed in the container. On `rtems-proxy start` it copies binaries from `/epics/ioc/` and `/epics/runtime` to `/nfs-tftp`. In DEV mode `/nfs-tftp` is rsynced to the NFS server.

## Monitoring

View rsync log in VSCode:

```bash
code /tmp/rsync-background.log
```

Get a shell on the `nfs-tftp` server:

```bash
module load k8s-i04
# tab-complete to find the pod name
kubectl exec -tin i04-beamline nfs-tftp-8b84789cb-wqvv6 -- bash
ls -l iocs/bl04i-ea-ioc-01/ioc/bin/RTEMS-beatnik

# OR for testing with the G13 crate:
module load pollux
kubectl exec -tin p45-beamline nfs-tftp-5bbbfb5844-xhf6c -- bash
```

## Build IOC Runtime Assets

Edit `/epics/ioc/start.sh` — change the line near the end to:

```bash
rtems-proxy start --no-connect
```

This keeps a direct terminal connection to the crate open for testing.

```bash
cd /epics/ioc
make
/epics/ioc/start.sh
```

Check `/tmp/rsync-background.log` to confirm assets are synced to the NFS server.

## Configure the Crate

```bash
# ensure no existing telnet connection, and that values.yaml is correct
rtems-proxy configure
```

## Connect to the Console

```bash
# I04 while conserver is running
console BL04I-EA-IOC-01

# Test rig in G13
telnet <console-host> 7027

# Boot sequence (the go -a address is the entrypoint)
dla=malloc 0x4000000
tftpGet -d/dev/enet1 -fbl04i-ea-ioc-01/ioc/bin/RTEMS-beatnik/ioc.boot \
  -m255.55.240.0 -g<IOC_GATEWAY_IP> -s<NFS_TFTP_IP> -c<IOC_IP> -adla -v -r3
go -a4000000
```

## Using DLS TFTP Server

```bash
# Copy ioc.boot to the DLS work tftpboot area
# (use podman ps to find the devcontainer name)
podman cp musing_feynman:/nfsv2-tftp/ioc/bin/RTEMS-beatnik/ioc.boot \
  /dls_sw/work/tftpboot/dev/giles

# At the crate console:
dla=malloc 0x4000000
tftpGet -d/dev/enet1 -fgiles/ioc.boot \
  -m255.255.240.0 -g<IOC_GATEWAY_IP> -s<DLS_TFTP_IP> -c<IOC_IP> -adla -v -r3
go -a4000000
```

## Source

Migrated from `raw/articles/testing-rtems-iocs.md` (original: `giles/Current/work/ec/RTEMS/TESTING RTEMS IOCS.md`).
