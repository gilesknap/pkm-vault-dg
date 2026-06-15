---
{"dg-publish":true,"permalink":"/notes/add-dls-only-support-module/","title":"Add a new DLS-only Support Module","tags":["kind/how-to","tech/containers/epics-containers","kind/documentation","tech/epics/support-module"],"dg-note-properties":{"title":"Add a new DLS-only Support Module","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/epics-containers","kind/documentation","tech/epics/support-module"],"summary":"Step-by-step guide for creating a new DLS-only ibek support module (e.g. SR-VA) within an ioc-xxx repo.","personal":false}}
---


## Add a new DLS-only Support Module

Example use-case: `SR-VA` within [[ioc-dlslinuxvac\|ioc-dlslinuxvac]].

See also: [[notes/epics-containers-page-index\|epics-containers-page-index]] for broader context.

### Steps

1. **Clone the target IOC repo**

```bash
git clone --recursive git@gitlab.diamond.ac.uk:controls/containers/iocs/ioc-dlslinuxvac
cd ioc-dlslinuxvac/ibek-support-dls
```

2. **Create the module directory and stub YAML**

```bash
mkdir SR-VA
cp dlsPLC/dlsPLC.ibek.support.yaml SR-VA/SR-VA.ibek.support.yaml
```

3. **Minimal `SR-VA.ibek.support.yaml`** (templates-only module)

```yaml
# yaml-language-server: $schema=../../ibek-support/_scripts/support_install_variables.json

module: SR-VA
version: 3-3

organization: https://gitlab.diamond.ac.uk/controls/support
```

4. **Generate the full `ibek.support.yaml`** via `builder2ibek.support`

```bash
/scratch/hgv27681/work/builder2ibek.support/builder2ibek.support.py \
  /tmp/SR-VA \
  ibek-support-dls/SR-VA.ibek.support.yaml \
  2:SR03C 8:10 9:11 15:10 16:11
```

> **⚠️ Known failure:** `builder` cannot resolve `auto_XXX` templates inside XML templates  
> (e.g. `SR-VA/etc/makeIocs/common.xml` references `SR-VA.auto_rgaPowerCycle`).

> **TODO:** Fix that problem and extend `builder2ibek.support` to convert XML templates to `sub_entities`.

5. **Push changes to `ibek-support-dls`**

6. **Add to the Dockerfile**

```dockerfile
COPY ibek-support-dls/SR-VA/ SR-VA
RUN ansible.sh SR-VA
```

### Related

- [[notes/epics-containers-page-index\|epics-containers-page-index]]
- [[notes/auto-generate-schema-odin-eiger\|auto-generate-schema-odin-eiger]] — similar ibek.support.yaml authoring pattern
- [[notes/debugging-iocs-in-cluster\|debugging-iocs-in-cluster]]
- [[notes/combine-ixx-deployment-and-ixx-services\|combine-ixx-deployment-and-ixx-services]]
