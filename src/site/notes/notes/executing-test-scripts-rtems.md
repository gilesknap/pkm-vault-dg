---
{"dg-publish":true,"permalink":"/notes/executing-test-scripts-rtems/","title":"Executing Test Scripts (RTEMS)","tags":["kind/how-to","domain/embedded-systems","tech/containers/epics-containers","tech/hardware/rtems","kind/testing","tech/cli"],"dg-note-properties":{"title":"Executing Test Scripts (RTEMS)","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","domain/embedded-systems","tech/containers/epics-containers","tech/hardware/rtems","kind/testing","tech/cli"],"summary":"Step-by-step commands for running RTEMS test scripts via the RTEMS console and the build container, covering tftp boot and objcopy.","personal":false}}
---


Source: `raw/articles/executing-test-scripts-rtems.md`

## RTEMS console

Copy the boot file to the tftp directory and open a serial console:

```bash
cp nfs.boot /dls_sw/work/tftpboot/dev/giles
telnet <console-host> 7027
```

Then at the RTEMS prompt:

```
reset
dla=malloc 0x4000000
tftpGet -d/dev/enet1 -fgiles/nfs2.boot  -m255.255.240.0 -g<IOC_GATEWAY_IP> -s<DLS_TFTP_IP> -c<IOC_IP> -adla -v -r3
go -a04000000
```

## In the build container

Convert the ELF executable to a raw binary for TFTP boot:

```bash
powerpc-rtems6-objcopy -O binary build/powerpc-rtems6-beatnik-default/nfs01.exe /nfs2.boot
```

## Related

- [[notes/testing-rtems-iocs\|testing-rtems-iocs]] — higher-level notes on the RTEMS IOC test workflow
- [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]] — how the IOC is built before these scripts are run
- [[notes/chat-chris-johns-rtems-epics-2025-03-11\|chat-chris-johns-rtems-epics-2025-03-11]] — upstream RTEMS/EPICS discussion context
- [[notes/githublab-issues-rtems\|githublab-issues-rtems]] — tracked issues for RTEMS work
