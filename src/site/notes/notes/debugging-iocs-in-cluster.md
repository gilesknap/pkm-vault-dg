---
{"dg-publish":true,"permalink":"/notes/debugging-iocs-in-cluster/","title":"Debugging IOCs in-cluster","tags":["kind/how-to","tech/epics/core","tech/containers/kubernetes","domain/embedded-systems","domain/software"],"dg-note-properties":{"title":"Debugging IOCs in-cluster","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/epics/core","tech/containers/kubernetes","domain/embedded-systems","domain/software"],"summary":"Step-by-step guide to attaching VSCode (with GDB) to a live EPICS IOC running inside a Kubernetes cluster for interactive debugging.","personal":false}}
---


See also: [[notes/python-debugging-in-cluster\|python-debugging-in-cluster]], [[notes/epics-iocs-cluster-host-network-false\|epics-iocs-cluster-host-network-false]], [[notes/iocs-in-kubernetes-demo\|iocs-in-kubernetes-demo]], [[notes/argocd-for-beamlines\|argocd-for-beamlines]], [[notes/epics-containers-page-index\|epics-containers-page-index]]

GitHub issue: [Issue #181](https://github.com/epics-containers/edge-containers-cli/issues/181#issuecomment-2586492758)
Confluence notes: [New Things](https://confluence.diamond.ac.uk/display/~hgv27681/New+Things)

These are the steps to debug an IOC in-cluster. They should eventually be automated by the `ec` tool.

## 1. Patch the ioc-template chart values

```yaml
ioc-instance:
  image: USE_DEVELOPER_TARGET:VERSION

  # don't make the IOC the entrypoint
  startCommand: sleep
  startArgs: infinity

  # disable host networking to allow root user
  hostNetwork: false

  # choose root user for write access to the container filesystem
  securityContext:
    runAsUser: 0
    runAsGroup: 0
    readOnlyRootFilesystem: false
    runAsNonRoot: false

  # mount the config map elsewhere so we can copy and edit it
  # i.e. `cp -r /tmp/config /epics/ioc/` before starting the IOC
  iocConfig: /tmp/config

  # generous resources to handle the extra overheads of debugging
  resources:
    limits:
      cpu: 4
      memory: 4Gi
    requests:
      cpu: 1
      memory: 1Gi
```

See [[notes/epics-iocs-cluster-host-network-false\|epics-iocs-cluster-host-network-false]] for notes on why `hostNetwork: false` is required here.

## 2. Expose PVs outside the cluster (optional)

If you need to access PVs from outside the cluster, deploy a gateway. Example: [t03-services/gateways](https://github.com/gilesknap/t03-services/tree/main/services/gateways)

## 3. Stop the existing runtime IOC

> **ArgoCD caveat:** for [[notes/argocd-for-beamlines\|ArgoCD-based beamlines]] you must *remove* the IOC (set the `removed` flag in `apps/values.yaml`) rather than simply stopping it, because Helm cannot update a non-Helm deployment.

## 4. Deploy the debug IOC

```bash
ec deploy-local services/my-debugging-ioc
```

## 5. Set up cluster environment

```bash
module load pollux
```

## 6. Launch VSCode

Must be the **first instance** launched so it picks up the cluster kubeconfig. Required extensions:
- Kubernetes
- Remote Development

## 7. Attach to the pod

1. Open the Kubernetes explorer pane.
2. **Namespaces** → right-click → *Use Namespace* on the beamline namespace.
3. **Workloads → Pods → IOC** → right-click → **Attach Visual Studio Code**.

## 8. Open the workspace

- *Open Folder* → `/epics`

## 9. Prepare the config folder

```bash
cp -r /tmp/config /epics/ioc/config
```

## 10. Run a debug session

`/epics` contains a `.vscode` folder with pre-configured launchers:

- **(gdb) Attach to IOC**
- **Open a core dump (C++)**
- **Launch IOC in debugger**

## What you get

All facilities of a local devcontainer:

- SSH keys and git config
- `apt install` capability
- Full GDB debug sessions, including core-dump analysis
- `kubectl` access to the current namespace (no extra config needed)
- X11 forwarding

## What you don't get

- Local workspace mount into the container
  - Possible workaround: mount shared filesystem or use a PVC to avoid losing work on pod restart

## Raw source

[[raw/articles/debugging-iocs-in-cluster\|raw/articles/debugging-iocs-in-cluster.md]]
