---
{"dg-publish":true,"permalink":"/actions/epics-containers-ansible-todos/","title":"EPICS Containers Ansible Todos","tags":["domain/embedded-systems","domain/software","domain/accelerator"],"dg-note-properties":{"title":"EPICS Containers Ansible Todos","type":"action","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["domain/embedded-systems","domain/software","domain/accelerator"],"summary":"EPICS Containers Ansible Todos","personal":false,"status":"in_progress","priority":"Medium","due_date":"2026-08-01"}}
---


Migrated from `Ansible ec todos.md`. Tracks outstanding and completed Ansible integration work for [[notes/epics-containers-page-index\|epics-containers]]. Related breaking-change context: [[notes/epics-containers-breaking-changes-5-0-0\|epics-containers-breaking-changes-5-0-0]].

See also [[notes/ibek-rtems-ioc-build-process\|ibek-rtems-ioc-build-process]] for RTEMS-specific build context and [[notes/helm-chart-structure-and-json-schema-references\|helm-chart-structure-and-json-schema-references]] for schema conventions.

## Outstanding

- [ ] Switch to using a single `RELEASE.local` in `/epics/support` and patch an include into `configure/RELEASE` for that when needed.
- [ ] Consider Ansible for base tasks.
- [ ] Delete all `install.sh` and `/_globals` — this is a breaking change!
- [ ] Create artifact on release — list of support module versions compiled.
- [ ] Add `when:` to all lists so can control their use by target arch.

## Completed

- [x] Rename `ibek-support/*/*.yml` to `*.install.yml`
- [x] Schema for `xxx.install.yml`
- [x] Install Ansible in base
- [x] ~~Have the ioc role copy all protocol files into `/epics/runtime/protocol` and remove additional `PROTOCOL_PATH` entries in `st.cmd`~~ (superseded by item below)
- [x] Instead of above: add protocol file list to each `module.install.yml` and copy those into `/epics/runtime/protocol`
- [x] Update ioc-template to use Ansible (must be done before next)
- [x] Extra user-supplied tasks post and pre build
- [x] Extra user-supplied bash script post and pre build
- [x] Copy support vars instead of soft-linking the whole of ibek-support into the Ansible folder
- [x] Move ibek-support folder detection into support role to simplify all roles and `ansible.sh`
- [x] Use above to drop to one playbook only, plus very simple `ansible.sh`
- [x] Put `ansible.sh` in `PATH` instead of symlink
- [x] Add auto-save mount to converter when RTEMS
- [x] Write CI for ibek-support and ibek-support-dls that compile ALL support modules into a test container

## Ideas / Follow-ups

### Playground

Goal: fire up ioc-generic devcontainer, run `ansible.sh` module a few times, and get testing. Requires only installing Ansible and updating ioc-generic to latest ibek-support.

### Legacy Support

Ansible support should merge into ioc-template and ibek-support* without breaking legacy `ioc-xxx` projects that update to latest ibek-support.

- The update path via `ioc-template` copier update brings in required changes.
- The only breaking change while `install.sh` still exists is the single protocol-folder change. If `start.sh` is made agnostic about the existence of `/epics/support/protocol`, old `ioc-xxx` projects can update without breaking — but a manual `PROTOCOL_PATH` addition in `ioc.yaml` would be required.
- **Important:** if `PROTOCOL_PATH` changes are merged, the lakeshore340 tutorial needs updating — consider making it Ansible-based at that point.
