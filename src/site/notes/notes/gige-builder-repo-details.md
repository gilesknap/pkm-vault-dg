---
{"dg-publish":true,"permalink":"/notes/gige-builder-repo-details/","title":"GigE Builder Repo Details","tags":["kind/reference","tech/epics/core","tech/epics/ioc","tech/hardware/gig-e","tech/hardware/camera","tech/devops/gitlab","tech/lang/xml","org/diamond-light-source","domain/embedded-systems","domain/controls"],"dg-note-properties":{"title":"GigE Builder Repo Details","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/reference","tech/epics/core","tech/epics/ioc","tech/hardware/gig-e","tech/hardware/camera","tech/devops/gitlab","tech/lang/xml","org/diamond-light-source","domain/embedded-systems","domain/controls"],"summary":"Details of Diamond's GitLab GigE camera builder repo, including XML crate structure and branch notes, shared by Alex Wells.","personal":false}}
---


## Diamond GigE Camera Builder Repo

Repo shared by Alex Wells (Diagnostics group at Diamond Light Source):

- **GitLab URL:** https://gitlab.diamond.ac.uk/controls/support/GIGE-BUILDER
- **Test crate:** `etc/makeIocs/LG18-DI-IOC-01.xml` — a test crate containing **3 layers of XML**.
- **Branch `more_cameras`:** introduces a fourth layer of XML, for a total of **4 nested XML layers**.

The repo is relevant to [[notes/adexternal-integration-gige-ioc\|adexternal-integration-gige-ioc]] (GigE IOC integration work at Diamond) and the broader [[notes/epics-containers-page-index\|epics-containers-page-index]] builder/IOC toolchain.

### Context

This was communicated directly by Alex Wells to Giles Knap. The XML layering depth is notable for anyone working on [[notes/vae-builder-ibek-rtems-ioc-migration\|vae-builder-ibek-rtems-ioc-migration]] or similar builder-pattern IOC tooling.

^[raw/articles/diamond-gige-camera-builder-repo.md]
