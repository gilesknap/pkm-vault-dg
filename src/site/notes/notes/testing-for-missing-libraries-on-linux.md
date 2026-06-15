---
{"dg-publish":true,"permalink":"/notes/testing-for-missing-libraries-on-linux/","title":"Testing for Missing Libraries on Linux","tags":["kind/how-to","tech/os/linux","domain/embedded-systems","domain/software"],"dg-note-properties":{"title":"Testing for Missing Libraries on Linux","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/os/linux","domain/embedded-systems","domain/software"],"summary":"How to diagnose missing shared libraries on Linux using ldd and ldconfig, with RHEL and EPICS IOC context.","personal":false}}
---


## Testing for Missing Libraries on Linux

> Context: Alex Malowany discussing RHEL7/RHEL8 IOCs in the [[raw/articles/epics-containers\|epics-containers]] ecosystem.

The baseline RHEL environment should be broadly compatible, except for libraries linked against at build time. Two key tools:

### `ldd` — for compiled binaries

Run `ldd <binary>` to list all shared library dependencies and flag any that cannot be resolved:

```sh
ldd /path/to/ioc-binary
```

Any entry showing `not found` identifies a missing library.

### `ldconfig -p` — for listing available libraries

Use `ldconfig -p` to dump all libraries currently registered in the dynamic linker cache:

```sh
ldconfig -p | grep libsomething
```

This is useful when:
- You want to verify a library *is* present before running the binary.
- You are debugging a [[notes/python-debugging-in-cluster\|Python IOC]] where `ldd` is less directly applicable (check that the `.so` extension modules resolve).

### Workflow

1. Run `ldd` on the compiled binary or shared object.
2. For any `not found` entries, search with `ldconfig -p` or `find /usr/lib* -name 'libfoo*'`.
3. Install the missing RPM package (e.g. `dnf provides libfoo.so`) or add the path to `LD_LIBRARY_PATH` / `ldconfig` configuration.

See also [[notes/andor3-missing-dependencies-container-troubleshooting\|andor3-missing-dependencies-container-troubleshooting]] for a concrete example of this process applied to a containerised EPICS driver.

> Source: `raw/articles/testing-for-missing-libraries-on-linux.md`
