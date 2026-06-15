---
{"dg-publish":true,"permalink":"/notes/combine-ixx-deployment-and-ixx-services/","title":"Combine IXX Deployment And IXX Services","tags":["kind/concept","tech/containers/epics-containers","kind/design-decision","tech/containers/argocd","kind/deployment","domain/software"],"dg-note-properties":{"title":"Combine IXX Deployment And IXX Services","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/concept","tech/containers/epics-containers","kind/design-decision","tech/containers/argocd","kind/deployment","domain/software"],"summary":"Rejected design decision: combining ixx-deployment and ixx-services repos would dirty git history with deploy commits — keep them separate.","personal":false}}
---


## Summary

Design exploration and rejection of merging `ixx-deployment` and `ixx-services` repos into one, within the [[raw/articles/epics-containers\|epics-containers]] ecosystem.

## Analysis

Combining the two repos is theoretically possible by switching which root app ArgoCD tracks from the UI. This would allow `ec` to deploy into the currently tracked branch.

### Problems identified

- If `ec` deploys into a branch while that branch is being edited, a `git pull` would be needed to stay in sync — merge conflict risk is non-trivial.
- Explaining the model to users is harder than the current separation.
- The simpler model — tracking `main` in the root App and always deploying to `main` via `ec` — is preferable.

### Clincher

All start/stop/deploy commits would dirty the history of a combined services+deployment repo. **Decision: do not combine. FORGET ABOUT IT.**

## Prototype branches

Experimental work was done on these branches (now considered dead ends):

- [gilesknap/hgv27681-services@combine-deploy-services](https://github.com/gilesknap/hgv27681-services/tree/combine-deploy-services)
- [epics-containers/edge-containers-cli@combine-deploy-services](https://github.com/epics-containers/edge-containers-cli/tree/combine-deploy-services)

## Related

- [[notes/epics-containers-breaking-changes-5-0-0\|epics-containers-breaking-changes-5-0-0]]
- [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]]
- [[notes/beamline-control-system-roadmap-whiteboard-2025-2028\|beamline-control-system-roadmap-whiteboard-2025-2028]]

> Source migrated from `giles/Current/work/ec/xOutdated/Combine ixx-deployment and ixx-services.md`
