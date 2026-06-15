---
{"dg-publish":true,"permalink":"/notes/epics-core-team-meeting-2025-09-07/","title":"EPICS Core Team Meeting 2025-09-07","tags":["kind/reference","tech/epics/core","tech/hardware/rtems","tech/containers","tech/epics/pvaccess","tech/epics/pvxs","kind/meeting-notes","domain/accelerator","domain/software"],"dg-note-properties":{"title":"EPICS Core Team Meeting 2025-09-07","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/reference","tech/epics/core","tech/hardware/rtems","tech/containers","tech/epics/pvaccess","tech/epics/pvxs","kind/meeting-notes","domain/accelerator","domain/software"],"summary":"Notes from the EPICS Core Team meeting on 2025-09-07 covering RTEMS6 NTP issues, PVAccess networking in containers, and PVXS for AreaDetector.","personal":false}}
---


## RTEMS

- Main topic: NTP PR from Chris Johns (targeting RTEMS6 support)
- NTP is needed for [[rtems6-ioc-support\|rtems6-ioc-support]] (and specifically for the I04 vac IOC where time is currently not set at all)
- **Disconnect between Michael Davidsaver and Chris Johns** — GitHub conversation not converging
  - Giles volunteered to join a mediating call with Michael (timezone-challenging)
- The NTP implementation lives in **libbsd**, a Unix library expecting `/etc/ntpd.conf`
  - Chris's approach: embed config in code, dump to `/etc` before starting NTP
  - Michael's objection: config files don't belong in epics-base; wants a generic mechanism for all libbsd-imported services
  - Michael's preferred pattern: supply config files via NFS/share, use iocShell commands to copy to ramfs then start services (NFS config alone would need to be embedded to bootstrap)
  - Broader concern: keep custom RTEMS code in epics-base to a minimum
- **RTEMS4 support likely being dropped**; RTEMS5 also under consideration
  - Heinz still buying VME crates and has significant RTEMS5 deployments
  - Boards discussed were Motorola variants — feeds into deprecation decisions

## Containers & PVAccess Networking

See also [[notes/epics-containers-breaking-changes-5-0-0\|epics-containers-breaking-changes-5-0-0]] and [[notes/argocd-for-beamlines\|argocd-for-beamlines]].

- Extensive discussion on PVAccess networking issues in containerised environments
- Root cause of PVA not working in the scenario Giles presented at a recent TechTalk:
  - Special handling of **localhost** when forwarding UDP unicast to IOC peers on the same machine
  - Michael believes he has a fix: inspect destination IP of incoming packets
  - Caveat: not available in cross-platform libs — would need platform-specific implementations
- **Current status**: fix may be coming; DLS not currently impacted; workarounds available via `--net host` or `pvagw`
- Ralf's question — what could epics-base change to improve containerised IOCs?
  - Idea floated: compose layers of pre-built shared libraries, build each support module version once
  - Giles described DLS's **build-from-source / flat structure** philosophy — general agreement it is reasonable
  - Counterpoint raised: build a large support set, then have iocShell load dynamic libraries at runtime per-instance
    - Could reduce generic IOC count; but memory footprint impact unclear (container image large regardless)
  - Giles's conclusion: **the EPICS build system works well in containers**; no changes needed from epics-base side

## PVXS for AreaDetector

See also [[notes/slsdetector-notes\|slsdetector-notes]] and [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]].

- Tom's issue (migrating AreaDetector to PVXS) mentioned — all members supportive
- Giles reported an issue connecting to AD PVA from a container client in `--net host` mode
  - No insights offered in meeting
  - Detailed network trace with Emilio planned but deprioritised given PVXS-for-AD direction

## Action Items

- [ ] Giles: Join Michael Davidsaver / Chris Johns call on RTEMS NTP PR (coordinate timezones)
- [ ] Giles: Investigate dynamic library loading at runtime — memory footprint implications for generic IOC strategy
- [ ] Giles: Decide whether to pursue AD PVA container networking trace with Emilio given PVXS migration path
