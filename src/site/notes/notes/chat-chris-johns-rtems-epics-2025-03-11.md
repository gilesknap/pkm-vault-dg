---
{"dg-publish":true,"permalink":"/notes/chat-chris-johns-rtems-epics-2025-03-11/","title":"Chat with Chris Johns re RTEMS and EPICS","tags":["kind/reference","kind/meeting-notes","domain/embedded-systems","tech/epics/core","domain/controls","tech/hardware/rtems","domain/networking","tech/net/ntp"],"dg-note-properties":{"title":"Chat with Chris Johns re RTEMS and EPICS","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/reference","kind/meeting-notes","domain/embedded-systems","tech/epics/core","domain/controls","tech/hardware/rtems","domain/networking","tech/net/ntp"],"summary":"Notes from a 2025-03-11 call with Chris Johns covering RTEMS libbsd networking status, rtems-deployment packaging plans, and NTP integration issues with EPICS base.","personal":false}}
---


Meeting with [[Chris Johns\|Chris Johns]] (RTEMS project), Tuesday 11 March 2025 at 07:00 GMT (16:00 Eastern Australia).

Source transcript: [[raw/articles/chat-chris-johns-rtems-epics-2025-03-11\|raw/articles/chat-chris-johns-rtems-epics-2025-03-11]]

## Networking in RTEMS

- Networking has been **removed from RTEMS 5/6** but remains available via the legacy networking stack.
- **libbsd** is the future path for networking and other kernel features (USB etc.):
  - `libbsd 6-12` is released but gives unstable networking.
  - `libbsd 6-14` is unreleased but expected to be much more stable; based on FreeBSD 15.
  - Chris has been testing on a Beatnik board.

## rtems-deployment

- Chris would like to move to `rtems-deployment`, which packages up all the steps currently handled with WAF etc.
- Considering a **target-architecture feature** that strips all non-essential libraries after a full build.
- Also considering shipping containers built on this stripped output.
- Relates to current work tracked in [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]].

## NTP

- NTP support is coming, but **Michael Davidsaver** has concerns with the current approach due to files embedded in EPICS base.
- Chris wants to leave the NTP code untouched and have it read standard config files.
- Proposed resolution: generate the config files on the NFS server at startup.
- Relevant to [[notes/vae-builder-ibek-rtems-ioc-migration\|vae-builder-ibek-rtems-ioc-migration]] integration work.

## Context / Funding

- Chris is currently working alone with one part-time assistant.
- Funding comes via the **Gemini telescope project** (NOIRLab): https://noirlab.edu/science/about

## Whiteboard / Screenshot

![[Pasted image 20250928124831.png\|Pasted image 20250928124831.png]]
