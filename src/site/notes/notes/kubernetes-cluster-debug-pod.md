---
{"dg-publish":true,"permalink":"/notes/kubernetes-cluster-debug-pod/","title":"Kubernetes Cluster Debug Pod","tags":["kind/how-to","tech/containers/kubernetes","kind/debugging","tech/containers/epics-containers","domain/software"],"dg-note-properties":{"title":"Kubernetes Cluster Debug Pod","type":"note","created":"2026-06-02","updated":"2026-06-02","source":"import","tags":["kind/how-to","tech/containers/kubernetes","kind/debugging","tech/containers/epics-containers","domain/software"],"summary":"Scripts and commands for launching ephemeral debug pods in a Kubernetes cluster for EPICS IOC troubleshooting.","personal":false}}
---


Two shell scripts for spinning up debug pods inside a Diamond beamline Kubernetes cluster. Useful for diagnosing [[raw/articles/epics-containers\|epics-containers]] IOC issues at runtime. See also [[notes/debugging-iocs-in-cluster\|debugging-iocs-in-cluster]] and [[notes/python-debugging-in-cluster\|python-debugging-in-cluster]].

## Quick one-liner

Simplest approach: launch an `epics-base-developer` container directly on a named node.

```bash
kubectl run --rm -ti debug-pod \
  --overrides='{"kind":"Pod", "apiVersion":"v1", "spec": {"hostNetwork":true, "nodeName": "<node>.diamond.ac.uk"}}' \
  --namespace i24-beamline \
  --image ghcr.io/epics-containers/epics-base-developer:7.0.9ec5-beta.1
```

## `~/bin/pod-test` — launch a debug pod

Parameterised wrapper: `pod-test <beamline> [node]`

```bash
#!/bin/bash

beamline=$1
node=$2

image=ghcr.io/epics-containers/epics-base-developer:7.0.9ec5-beta.1
ns="${beamline}-beamline"

module unload ec argus pollux
module load k8s-${beamline} > /dev/null

if [[ -z $node ]] ; then
  overrides='{"spec": {"hostNetwork":true}}'
else
  overrides='{"spec": {"hostNetwork":true, "nodeName": "'$node'.diamond.ac.uk"}}'
fi

kubectl run --rm -ti debug-pod --overrides=${overrides} -n ${ns} --image ${image}
```

> **Don't forget** to set the default namespace after loading the module:
> ```bash
> kubectl config set-context --current --namespace=i24-beamline
> ```

## `~/bin/pod-debug` — attach an ephemeral debug container to a running IOC pod

Parameterised wrapper: `pod-debug <beamline> <pod> [copy]`

When `copy` is set a new pod sharing the original's process namespace is created; connect to the original console and run `iocPause` / `iocRun` to hand off the device connection.

```bash
#!/bin/bash

beamline=$1
pod=$2
docopy=$3

ns="${beamline}-beamline"
module load k8s-${beamline} > /dev/null
kubectl config set-context --current --namespace=${ns}

if [[ -n $docopy ]]; then
  # copying the pod means two IOCs are running. Connect to the original console
  # and do `iocPause` to let the debugged one take over. `iocRun` to resume.
  # note this will fail for devices that accept only a single connection
  kubectl delete pod/${pod}-deb
  copy="--copy-to ${pod}-debug"
fi

# this image has busybox and EPICS tools
image=ghcr.io/epics-containers/epics-base-developer:7.0.9ec4

# if copy is enabled this will run a new pod that shares the process space
# with the IOC - otherwise it just starts an ephemeral container in the current pod
kubectl debug ${copy} --share-processes --profile netadmin -it ${pod} --image ${image}
```

## Related notes

- [[notes/debugging-iocs-in-cluster\|debugging-iocs-in-cluster]] — broader in-cluster IOC debugging workflow
- [[notes/python-debugging-in-cluster\|python-debugging-in-cluster]] — Python-specific in-cluster debugging
- [[notes/epics-iocs-cluster-host-network-false\|epics-iocs-cluster-host-network-false]] — host-network considerations
- [[notes/tunneling-into-dls-kubectl-helm-oidc-remote-operation\|tunneling-into-dls-kubectl-helm-oidc-remote-operation]] — remote kubectl access
- [[notes/iocs-in-kubernetes-demo\|iocs-in-kubernetes-demo]] — demo context for IOCs in Kubernetes
