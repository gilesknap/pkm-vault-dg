---
{"dg-publish":true,"permalink":"/notes/pvxs-python-pvget/","title":"PVXS Python Pvget","tags":["kind/how-to","tech/epics/core","domain/embedded-systems","domain/software"],"dg-note-properties":{"title":"PVXS Python Pvget","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/epics/core","domain/embedded-systems","domain/software"],"summary":"How to perform a pvget in Python using the p4p library with the PVXS/PVA protocol.","personal":false}}
---


A minimal Python script to perform a `pvget` using the [[p4p\|p4p]] library, which implements the [[PVXS\|PVXS]] PV Access (PVA) protocol.

See also: [[raw/articles/pvxs-python-pvget\|raw/articles/pvxs-python-pvget.md]]

## Usage

Install `p4p` via pip, then run the script with a PV name as the sole argument.

## Script

```python
#!/usr/bin/env python3
import sys
try:
    from p4p.client.thread import Context
except:
    print('Error: p4p not found. Please install it using pip.')
    sys.exit(1)

if len(sys.argv) != 2:
    print('Usage: pvget <pv_name>')
    sys.exit(1)
pv_name = sys.argv[1]

try:
    ctx = Context('pva')
    val = ctx.get(pv_name, timeout=1.0)
except TimeoutError as e:
    print(f'Error: Timeout while getting {pv_name}')
    sys.exit(1)
print(val)
```

## Notes

- Uses the `pva` (PV Access) transport context from [[p4p\|p4p]].
- Default timeout is 1 second; adjust `timeout=` as needed.
- Exits with a non-zero code on missing dependency, wrong argument count, or timeout — safe for scripted use.
- Part of the broader [[EPICS\|EPICS]] control system ecosystem used at [[Diamond Light Source\|Diamond Light Source]].
