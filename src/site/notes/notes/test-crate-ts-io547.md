---
{"dg-publish":true,"permalink":"/notes/test-crate-ts-io547/","title":"Test Crate TS-IO547","tags":["kind/reference","tech/hardware/rtems","tech/epics/core","tech/hardware","kind/configuration","domain/networking","kind/troubleshooting","tech/hardware/mvme5500","domain/embedded-systems"],"dg-note-properties":{"title":"Test Crate TS-IO547","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/reference","tech/hardware/rtems","tech/epics/core","tech/hardware","kind/configuration","domain/networking","kind/troubleshooting","tech/hardware/mvme5500","domain/embedded-systems"],"summary":"Hardware configuration, boot parameters, and network settings for MVME5500 test crates TS-IO547 and TS-IO523 running RTEMS at DLS.","personal":false}}
---


Configuration and troubleshooting reference for the MVME5500 test crates used for [[notes/rtems-6\|RTEMS]] [[notes/epics-containers-page-index\|EPICS]] IOC development at DLS. Both crates sit in the same rack in the p45-beamline namespace on pollux.

## Access

| Crate | Port | Command |
|-------|------|---------|
| TS-IO547 com port | 7027 | `telnet ts0001 7027` |
| TS-IO547 crate monitor | 7008 | `telnet ts0001 7008` |
| IOC523 com port | 7007 | `telnet ts0001 7007` |

NFS/TFTP service IP (p45-beamline namespace on pollux): [172.23.168.190](http://172.23.168.190:111/)

## Reset a Crate

Connect to the crate monitor port and send:

```
R,7E
```

Response `r,5E` confirms restart.

## TS-IO547 Network Parameters

| Value | Meaning |
|-------|---------|
| 172.23.168.190 | TFTP and NFSv2 server IP |
| 172.23.240.254 | Gateway IP |
| 255.255.240.0 | Subnet mask |
| 172.23.250.39 | Address of crate |

## TS-IO547 MotBoot (RTEMS — p45 NFS/TFTP service)

```
MVME5500> gevs
mot-/dev/enet0-snma=255.255.240.0
mot-/dev/enet0-gipa=172.23.240.254
mot-boot-device=/dev/em1
mot-/dev/enet0-sipa=172.23.168.190
rtems-client-name=TS_IO547
epics-ntpserver=EPICS_TS_NTP_INET
mot-/dev/enet0-cipa=172.23.250.39
epics-script=172.23.168.190:/iocs:bl04i-va-ioc-01/runtime/st.cmd
mot-script-boot
dla=malloc 0x230000
tftpGet -d/dev/enet1 -fbl04i-va-ioc-01/ioc/bin/RTEMS-beatnik/ioc.boot -m255.255.240.0 -g172.23.240.254 -s172.23.168.190 -c172.23.250.39 -adla
go -a0095F000
reset
```

## TS-IO547 MotBoot (RTEMS — official NFS mount)

```
MVME5500> gevs
mot-/dev/enet0-snma=255.255.240.0
mot-/dev/enet0-gipa=172.23.240.254
mot-boot-device=/dev/em1
mot-/dev/enet0-sipa=172.23.247.36
rtems-client-name=TS_IO547
epics-ntpserver=EPICS_TS_NTP_INET
mot-/dev/enet0-cipa=172.23.250.39
epics-script=172.23.247.36:/exports/iocs:rtems/epics/BL04I-VA-IOC-01/runtime/st.cmd
mot-script-boot
dla=malloc 0x230000
tftpGet -d/dev/enet1 -fbl04i-va-ioc-01/ioc/bin/RTEMS-beatnik/ioc.boot -m255.255.240.0 -g172.23.240.254 -s172.23.168.190 -c172.23.250.39 -adla
go -a0095F000
reset
```

## TS-IO547 VxWorks Boot Parameters

```
boot device          : gei0 h
processor number     : 0
host name            : cs03r-cs-vserv-36
file name            : /dls_sw/targetOS/vxWorks/Tornado-2.2/target/config/mv5500/vxWorks
inet on ethernet (e) : 172.23.250.39:fffff000
inet on backplane (b): 0.0.0.0
host inet (h)        : 172.23.247.36
gateway inet (g)     : 172.23.240.254
user (u)             : epics
ftp password (pw)    : see hints
flags (f)            : 0x1000
target name (tn)     : ioc547
startup script (s)   : /dls_sw/prod/etc/redirector/redirect.cmd
```

| Name | Meaning |
|------|---------|
| inet on ethernet | Crate IP with hex-encoded subnet mask |
| host inet | NFS server IP |
| gateway inet | Gateway to other subnets |

## Second Test Crate — IOC523

In the same rack, above TS-IO547. Com port: `telnet ts0001 7007`

### IOC523 MotBoot (cs03r-cs-vserv-36 as NFS server)

```
MVME5500> gevs
mot-/dev/enet0-cipa=172.23.250.15
mot-/dev/enet0-snma=255.255.240.0
mot-/dev/enet0-gipa=172.23.240.254
mot-boot-device=/dev/em1
rtems-client-name=TS_IO523
epics-script=172.23.247.36:/exports/iocs:rtems/epics/BL04I-VA-IOC-01/runtime/st.cmd
mot-script-boot
dla=malloc 0x230000
tftpGet -d/dev/enet1 -fbl04i-va-ioc-01/ioc/bin/RTEMS-beatnik/ioc.boot -m255.255.240.0 -g172.23.240.254 -s172.23.168.190 -c172.23.250.15 -adla
go -a0095F000
reset
mot-/dev/enet0-sipa=172.23.247.36
```

### IOC523 Previous Settings (Early 2024)

```
mot-/dev/enet0-cipa=172.23.250.15
mot-/dev/enet0-snma=255.255.240.0
mot-/dev/enet0-gipa=172.23.240.254
mot-boot-device=/dev/em1
rtems-client-name=bl20j-mo-brick-00
epics-script=172.23.247.36:/exports/iocs:rtems/epics/SR25A-PC-IOC-01/stSR25A-PC-IOC-01.boot
mot-script-boot
dla=malloc 0x300000
tftpGet -d/dev/enet1 -frtems/epics/SR25A-PC-IOC-01/SR25A-PC-IOC-01.boot -m255.255.240.0 -g172.23.240.254 -s172.23.247.36 -c172.23.250.15 -adla -r3
go -a0095F000
```

## Hardware

![[Pasted image 20250928125329.png\|Pasted image 20250928125329.png]]

See also: [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]] for how RTEMS IOC binaries are built and deployed to these crates.
