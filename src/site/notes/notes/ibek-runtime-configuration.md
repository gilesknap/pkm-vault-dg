---
{"dg-publish":true,"permalink":"/notes/ibek-runtime-configuration/","title":"IBEK Runtime Configuration","tags":["kind/how-to","domain/embedded-systems","domain/software","tech/epics/ibek","tech/epics/core","tech/containers/kubernetes","tech/containers","tech/lang/yaml","tech/cli"],"dg-note-properties":{"title":"IBEK Runtime Configuration","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","domain/embedded-systems","domain/software","tech/epics/ibek","tech/epics/core","tech/containers/kubernetes","tech/containers","tech/lang/yaml","tech/cli"],"summary":"Practical CLI recipes for running ibek outside a container, generating runtime YAML, and producing ioc.yaml schemas.","personal":false}}
---


Reference for running `ibek` runtime commands locally (outside a container), generating schemas, and using the `generate2` subcommand. Source migrated from `giles/_New/Testing ibek and runtime.yaml.md`.

See also [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]] and [[notes/epics-containers-page-index\|epics-containers-page-index]] for broader context.

## Running ibek outside of a container

```bash
# make a pretend EPICS_ROOT
mkdir -p /tmp/epics/pvi-devs
export EPICS_ROOT=/tmp/epics
# copy in all the pvi devices
for i in /scratch/hgv27681/work/ibek-support/*/*.pvi.device.yaml; do ln -s $i /tmp/epics/pvi-defs; done
# spoof the autogen pvi device for aravis cameras
cp /tmp/epics/pvi-defs/ADAravis.pvi.device.yaml /tmp/epics/pvi-defs/auto-bl01c-di-dcam-01.pvi.device.yaml

# try out standard ibek generate
cd /scratch/hgv27681/work/b01-1-services
uvx ibek runtime generate services/bl01c-di-dcam-01/config/ioc.yaml ../ibek-support/*/*.ibek.support.yaml services/bl01c-di-dcam-01/config/ibek-runtime-support/*/*.ibek.support.yaml

# check results
ls /tmp/epics/runtime

# NOTE: you can pass --no-pvi instead of making the ibek-defs folder!
```

## Running ibek outside EASY mode

```bash
export EPICS_ROOT=/tmp
ibek runtime generate --no-pvi services/bl01c-di-dcam-01/config/ioc.yaml ../ibek-support/*/*.ibek.support.yaml
ibls /tmp/runtime
```

## Generating Schema for runtime.yaml

```bash
ibek ioc generate-schema ./ibek-runtime-support/*/*.ibek.support.yaml --output ./ibek-runtime-support/runtime.ibek.schema.json
```

## Generate2

```bash
mkdir -p /tmp/epics/ibek-defs
for i in /scratch/hgv27681/work/ibek-support/*/*.ibek.support.yaml; do ln -s $i /tmp/epics/ibek-defs; done
cd bl01-1-services
ibek runtime generate2 --no-pvi services/bl01c-di-dcam-01
```

## Related

- [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]] — RTEMS-specific ibek IOC build process
- [[notes/epics-containers-page-index\|epics-containers-page-index]] — epics-containers project index
- [[notes/auto-generate-schema-odin-eiger\|auto-generate-schema-odin-eiger]] — schema generation patterns for other IOCs
- [[notes/vae-builder-ibek-rtems-ioc-migration\|vae-builder-ibek-rtems-ioc-migration]] — ibek migration context
