# Kubernetes Security Architecture Considerations

This is an (at the moment) random list of things to think about when architecting Kubernetes based systems. They may not all still be current and if you know one's not right, PRs always welcome :)


## CVEs

- There are a number of CVEs in Kubernetes which have no patch and require manual mitigation from cluster operators.
  - [CVE-2020-8561](https://groups.google.com/g/kubernetes-security-announce/c/RV2IhwcrQsY)
  - [CVE-2021-25740](https://groups.google.com/g/kubernetes-security-announce/c/WYE9ptrhSLE)
  - [CVE-2020-8562](https://groups.google.com/g/kubernetes-security-announce/c/-MFX60_wdOY)
  - [CVE-2020-8554](https://groups.google.com/g/kubernetes-security-announce/c/iZWsF9nbKE8)

## Authentication

- None of the built-in authentication mechanisms shipped with base k8s are suitable for use by users.
  - Token authentication requires clear text tokens on disk and an API server restart to change.
  - Client certificate authentication does not support revocation (Github issue [here](https://github.com/kubernetes/kubernetes/issues/18982))
- Kubernetes does not have a user database, it relies on identity information passed from any approved authentication mechanism.
  - This means that if you have multiple valid authentication mechanisms, there is a risk of duplicate user identities. N.B. Kubernetes audit logging does record the identity of the user, but not the authentication source.


## RBAC

- There are various RBAC rights that can allow for Privilege escalation
  - GET or LIST on secrets at a cluster level (or possibly at a namespace level) will allow for privesc via service account secrets. N.B. LIST on its own will do this.
  - Access to ESCALATE, IMPERSONATE or BIND as RBAC verbs can allow privilege escalation.
- The `system:masters` group is **hard-coded** into the API server and provides cluster-admin rights to the cluster.
  - Access by a user using this group bypasses authorization webhooks (i.e. the request is never sent to the webhook)

## Networking

- Pods allowed to use host networking bypass Kubernetes Network Policies.
- Services allows to used nodePorts can't be limited by Kubernetes Network Policies.
- The pod proxy feature can be used to access arbitrary IP addresses **via** the Kubernetes API server (i.e. connections come from the API server address), which may bypass network restrictions (more details [here](https://kinvolk.io/blog/2019/02/abusing-kubernetes-api-server-proxying/))

## Pod Security Standards

- Without restriction on privileged containers, pods can be used to escalate privileges to node access
- Some capabilities will similarly allow for node access
  - CAP_SYS_ADMIN

## Distributions

- Many Kubernetes distributions provide a first user which uses a client certificate (so no revocation) bound to the system:masters group, so guaranteed cluster-admin. 
- Some distributions place sensitive information such as the API server certificate authority private key in a configmap (e.g. [RKE1](https://github.com/rancher/rke/issues/1024))

## DNS
 - At the moment it is possible to use DNS to enumerate all pods and services in a cluster, which can make leak information especially in multi-tenant clusters. (CoreDNS Issue [here](https://github.com/coredns/coredns/issues/4984)) (script to demonstrate [here](https://github.com/raesene/alpine-containertools/blob/master/scripts/k8s-dns-enum.rb))
