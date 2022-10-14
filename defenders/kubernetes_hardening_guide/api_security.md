# API Security

Kubernetes provides a number of APIs which perform the various functions of a cluster. There are roughly speaking three sets. The control plane APIs (API Server, Controller Manager and Scheduler), the worker node APIs (Kubelet and Kube Proxy) and etcd which may sit with the control plane nodes but can also run on stand alone nodes.

An important point when reviewing or securing these APIs is that in managed Kubernetes distributions (e.g. EKS, GKE, AKS) it is not possible for cluster operators to directly change the configuration of most of the APIs unless the cloud provider makes that available. The exception is the Kubelet which runs on worker nodes which are available to the cluster operator (unless it uses a "serverless" model like EKS Fargate)

## API Visibility

An initial point when hardening Kubernetes APIs is the decision to restrict access to them at a network level. Whilt it is unwise to rely on IP address filtering as a sole security control, it can help as a layer of defence. For example consider the case where an administrator has lost a set of credentials, or where an attacker has been able to grab a service account token from a cluster. If the API Server is exposed to the Internet, the attacker can then use those stolen credentials easily, whereas restrictions on which IP addresses can connect to the API server buys time for the defenders to notice the attack and rotate the credentials.

How you restrict access will depend on the type of Kubernetes in use. For cloud managed distributions (Which mostly default to making the API server available over the Internet) a cloud provider setting can be used to restrict access. The cloud provider can often also provide some kind of VPN like access to their services which might be usuable to provide access to the API server.

For unmanaged distributions, a VPN or bastion host configuration can be used to allow access where needed.


## Kubernetes API Server

This listens on a variety of ports depending on the distribution in use. Common options are 443/TCP, 6443/TCP and 8443/TCP. In most distributions the `anonymous-auth` flag will be set to `true`. This provides access to unauthenticated users to specific paths specified in the RBAC configuration of the cluster. For example in a Kubeadm cluster the following paths are available without authentication

```yaml
rules:
- nonResourceURLs:
  - /healthz
  - /livez
  - /readyz
  - /version
  - /version/
  verbs:
  - get
```

These are generally for liveness checks, but the `/version` endpoint does provide information useful to attackers like precise version information. From a historical perspective, it's worth noting that clusters on 1.14 or earlier will likely provide additional access to unauthenticated users.

In terms of hardening recommendations for the API server the main one is

* Disable anonymous authentication where possible, where it is required, ensure that minimal paths are available to unauthenticated users.

Another area to watch out for is the "insecure API service". This has been [disabled in recent versions](https://github.com/kubernetes/kubernetes/issues/91506) of Kubernetes but may still exist in older clusters. Typically it listened on port 8080/TCP and provided `cluster-admin` access to anyone who could reach it at a network level!

* Ensure that the `--insecure-port` flag (if available) is set to `0`.

## Kubernetes Controller Manager

Access to the controller manager is generally allowed over port 10257/TCP (can vary with version and distribution). In terms of anonymous access a small number of paths can be specified as a command line flag to allow access, the default settings is as below

```
--authorization-always-allow-paths strings     Default: "/healthz,/readyz,/livez"
```

In terms of recommendations :-

* Review paths which are allowed for anonymous access to ensure that no sensitive data is accessible without authentication.

## Kubernetes Scheduler

Access to the scheduler is generally allowed over port 10259/TCP (can vary with version and distribution). In terms of access this is very similar to the controller manager, the same parameter and default exists, and the recommendation would be the same. In general for both these services there aren't a lot of good reasons for direct access so outside of health checking there shouldn't be much of a requirement for unauthenticated access.

## Kubelet

The Kubelet runs on every worker node (and possibly control plane nodes). Access is via 10250/TCP. The kubelet's configuration with regards to anonymous access is a bit odd and not the same as either the scheduler or controller manager. Anonymous access defaults to being allowed, so it's a requirement of the distribution that they disable it (either on the command line or in the kubelet's configuration file). 

Requests to the root path of the server will return 404, but requests to meaningful paths (like `/pods/`) will return 401 (if anonymous authentication is disabled) or 403 (if anonymous authentication is enabled).

* Ensure that Kubelet anonymous authentication is disabled unless explicitly required for the operation of the cluster.

Another legacy option that can still be found in Kubernetes clusters is the use of the "read-only" version of the Kubelet API. This service is not authenticated (and there's no authentication option). It listens on port 10255/TCP by default

* Ensure that the `--read-only-port` flag on the Kubelet is set to `0`.

## Kube Proxy

TBD

## etcd

Etcd, whilst not specifically part of the Kubernetes project, is a core part of most Kubernetes distributions. Generally it listens on ports 2379/TCP and 2380/TCP. In most Kubernetes distributions there's no anonymous access to it by default, client certificate authentication is used, so the recommendations are quite simple

* Ensure that etcd is configured to require authentication for all requests.