---
{"dg-publish":true,"permalink":"/notes/useful-epics-containers-things/","title":"Useful Epics Containers Things","tags":["kind/how-to","tech/containers/epics-containers","tech/devops/git","tech/containers/kubernetes","tech/cli","domain/embedded-systems"],"dg-note-properties":{"title":"Useful Epics Containers Things","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/epics-containers","tech/devops/git","tech/containers/kubernetes","tech/cli","domain/embedded-systems"],"summary":"Handy CLI recipes for epics-containers: un-shallowing git clones and running diagnostic pods in a Kubernetes beamline cluster.","personal":false}}
---


## Make a Shallow Git Clone into a Full Clone with Branches

When a repo has been cloned with `--depth`, restore full history and all remote-tracking branches:

```bash
git fetch --unshallow
git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"
git fetch origin
```

## Get Diagnostics Running in the Cluster

Spin up an ephemeral debug pod inside a beamline namespace, using the generic IOC developer image:

```bash
kubectl run --rm -tin i22-beamline debug-pod \
  --image ghcr.io/epics-containers/ioc-generic-developer:2025.2.1 -- bash
# caget xxxxxx
```

To run on the **host network** (needed for PV/CA visibility), add the override flag:

```bash
--overrides='{"kind":"Pod", "apiVersion":"v1", "spec": {"hostNetwork":true}}'
```

## Related

- [[notes/epics-containers-page-index\|epics-containers-page-index]]
- [[notes/epics-iocs-cluster-host-network-false\|epics-iocs-cluster-host-network-false]]
- [[notes/argocd-for-beamlines\|argocd-for-beamlines]]
- [[notes/python-debugging-in-cluster\|python-debugging-in-cluster]]
