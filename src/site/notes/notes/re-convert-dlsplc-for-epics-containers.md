---
{"dg-publish":true,"permalink":"/notes/re-convert-dlsplc-for-epics-containers/","title":"Re-Convert DlsPLC For Epics Containers","tags":["kind/how-to","tech/containers/epics-containers","domain/embedded-systems","domain/controls","org/diamond-light-source","tech/epics/ioc","tech/devops/copier","tech/epics/ibek"],"dg-note-properties":{"title":"Re-Convert DlsPLC For Epics Containers","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/epics-containers","domain/embedded-systems","domain/controls","org/diamond-light-source","tech/epics/ioc","tech/devops/copier","tech/epics/ibek"],"summary":"Step-by-step how-to for pulling latest traditional dlsPLC changes into epics-containers during the IOC migration transition period.","personal":false}}
---


Steps to take to import the latest traditional dlsPLC into epics-containers.

These steps are required while transitioning between traditional IOCs and epics-containers. During this time the traditional support module is the master where changes are made; these steps show how to pull those changes into epics-containers as needed by IOC instances wanting new features from dlsPLC.

See also: [[notes/vacvalve-vs-dlsplc\|vacvalve-vs-dlsplc]] for context on dlsPLC vs alternatives, [[notes/ibek-runtime-configuration\|ibek-runtime-configuration]] for ibek support yaml conventions, [[notes/ideas-for-migration-of-generic-ioc\|ideas-for-migration-of-generic-ioc]] for broader migration strategy, and [[notes/argocd-and-epics-containers\|argocd-and-epics-containers]] for deployment.

## Copier Updates

Always update copier template projects to the latest template first:

```bash
uvx copier update --trust -A
```

Do this for any of:
- `xxx-services`
- `xxx-deployment`
- `ioc-xxx`

## dlsPLC Support Module

Get the latest changes and re-apply the containerisation fixes:

```bash
git clone git@gitlab.diamond.ac.uk:controls/support/dlsplc.git
git checkout epics-containers
git rebase origin/master
git push
```

Redo the vdct to template conversion:

```bash
module load uv
uvx vdct2template dlsPLCApp/Db
git add .
git commit -am'redo vdct to template conversion'
```

Make a tag that matches the current tag on master + `ec1` suffix:

```bash
git tag 3-0-61ec1
git push origin 3-0-61ec1
```

## Generic IOC Changes

Example: `ioc-dlslinuxvac`

```bash
git clone --recursive git@gitlab.diamond.ac.uk:controls/containers/iocs/ioc-dlslinuxvac
```

Inside `ibek-support-dls/dlsPLC`:

- Fix up `support.yaml` to reflect latest `builder.py` changes:

```bash
/scratch/hgv27681/work/builder2ibek.support/builder2ibek.support.py \
  /dls_sw/prod/R3.14.12.7/support/dlsPLC/3-0-61 \
  dlsPLC.ibek.support.yaml -o dlsPLC.overrides.yaml
```

> The above uses an override file to ensure the conversion is correct. If new `builder.py` additions using Python logic or XML template files have appeared, a manual update of the overrides file will be required. For example, `TemplateFile = "NX102_fastValveFullInterface.xml"` will need fixing up when ibekifying Front Ends.

- Edit `dlsPLC.install.yml` and update `version:` to match the `ec1` tag from the dlsPLC support module step above.
- Push changes to a branch (merge to main once working).
- Check it builds:

```bash
cd ioc-dlslinuxvac
./build
```

- Commit and push updates to `ioc-dlslinuxvac`.
- Tag to release a container image to the GitLab Container Registry.

## Update IOC Instances from Builder XML

Example: `sr03c-va-ioc-01` in the `va-services` repo.

Re-convert the latest XML IOC definition to `ioc.yaml`:

```bash
uvx builder2ibek xml2yaml \
  /dls_sw/prod/R3.14.12.7/support/SR-BUILDER/2025-Run4-1/etc/makeIocs/SR03C-VA-IOC-01.xml \
  --yaml services/sr03c-va-ioc-01/config/ioc.yaml
```

- Use the devcontainer to verify that the new `ioc.yaml` is schema-compliant.
- Verify that the changes to `ioc.yaml` look correct.

## Related

- [[notes/vacvalve-vs-dlsplc\|vacvalve-vs-dlsplc]] — dlsPLC context and alternatives
- [[notes/ibek-runtime-configuration\|ibek-runtime-configuration]] — ibek support YAML conventions
- [[notes/ideas-for-migration-of-generic-ioc\|ideas-for-migration-of-generic-ioc]] — broader migration strategy
- [[notes/argocd-and-epics-containers\|argocd-and-epics-containers]] — deployment via ArgoCD
- [[notes/combine-ixx-deployment-and-ixx-services\|combine-ixx-deployment-and-ixx-services]] — copier template projects
- [[notes/copy-autosave-legacy-to-epics-containers\|copy-autosave-legacy-to-epics-containers]] — other legacy-to-EC migration patterns
