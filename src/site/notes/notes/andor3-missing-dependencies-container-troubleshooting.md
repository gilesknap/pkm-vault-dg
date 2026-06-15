---
{"dg-publish":true,"permalink":"/notes/andor3-missing-dependencies-container-troubleshooting/","title":"Andor 3 Missing Dependencies in Container Troubleshooting","tags":["kind/how-to","tech/containers/epics-containers","domain/embedded-systems","kind/troubleshooting"],"dg-note-properties":{"title":"Andor 3 Missing Dependencies in Container Troubleshooting","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/how-to","tech/containers/epics-containers","domain/embedded-systems","kind/troubleshooting"],"summary":"Troubleshooting notes for missing shared-library dependencies when running the Andor 3 camera driver inside an EPICS container.","personal":false}}
---


# Andor 3 Missing Dependencies in Container Troubleshooting

Context: getting the Andor 3 detector working on **P99** inside an [[notes/epics-containers-page-index\|EPICS Containers]] deployment.

## Required libraries

- `libnuma-dev` — **definitely required**
- `intel-opencl-icd` — probably optional (listed on post-it; confirm before including)

> Source: post-it note photograph — see raw source for embedded image (`Pasted image 20250928124803.png`).

## RHEL 8 kernel driver

An RHEL 8 RPM for the Andor kernel drivers is now available:

- Jira ticket: [SCHD-8649](https://jira.diamond.ac.uk/servicedesk/customer/portal/2/SCHD-8649)

## Open action

- [ ] Get Andor 3 working on P99 — see [[andor3-p99-deployment\|andor3-p99-deployment]] (create if not yet filed)

## Related

- [[notes/epics-containers-breaking-changes-5-0-0\|epics-containers-breaking-changes-5-0-0]] — container toolchain context
- [[notes/slsdetector-notes\|slsdetector-notes]] — comparable detector-in-container pattern
