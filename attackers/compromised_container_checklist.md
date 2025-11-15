# Attackers - Compromised Container Checklist

A list of things you can try if you're doing a CTF/Pentest/Bug bounty and find yourself in a container.

## Confirming you're in a container.

### Docker

- `ls -al /.dockerenv` - If this file exists, it's a strong indication you're in a container
- `ps -ef` - Not a definitive tell, but if there are no hardware management processes, it's a fair bet you're in a container
- `ip addr` - Again not definitive, but `172.17.0.0/16` is the default docker network, so if all you have is network stats, this is useful
- `ping host.docker.internal` - should respond if you're in a docker container

### Tools for checking - Docker

- Run [amicontained](https://github.com/raesene/amicontained)

### Kubernetes

If you're landed in a Kubernetes cluster there are some other signs you can look for to determine that and get information about the environment.

Running `env` is very useful as Kubernetes generally injects environment variables like the ones below into every pod

```
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
```

### Tools for checking - Kubernetes

- Run [kdigger](https://github.com/quarkslab/kdigger) will automate checking `env` and a variety of other things to confirm the cluster you're in.

## Breaking out

### High level areas - Docker

- File mounts. What information can you see from the host
- Granted Capabilities. Do you have extra rights
- Kernel version. Is it a really old kernel which has known exploits.
- Namespace access, do you have access to the host's PID or network namespaces?

### High level areas - Kubernetes

In addition to the ones above which could well be present, there are some Kubernetes considerations

- Service account tokens. There's a token mounted at `/var/run/secrets/kubernetes.io/serviceaccount/token` by default which can access the Kubernetes API server. This token can be granted permissions which allow for privilege escalation
- Cloud metadata access. Kubernetes clusters are often deployed in clouds and if mis-configured might allow pods to access the metadata service for the underlying VM. If available this presents possibilities for privilege escalation.

### Tooling

[tools list here](../general_information/tools_list.md)

### Manual breakout - privileged containers

If you find out from amicontained or similar that you are in a privileged container, some ways to breakout

One for privileged containers is just to mount the underlying root filesystem. Run the `mount` command to get a list of filesystems. Usually files like `/etc/resolv.conf` are mounted off the underlying node disk, so just find that disk and mount the entire thing under something like `/host` and it'll provide edit access to the node filesystem.

Another option is to load a new kernel module as described in [this post](https://raesene.github.io/blog/2023/08/06/fun-with-privileged-container-breakout/)

### Manual breakout - Access to the Docker/containerd socket

If the tooling suggests that the Docker socket is available at `/var/run/docker.sock` then you can just get the docker CLI tool and run any docker command. To breakout use :-

- `docker run -ti --privileged --net=host --pid=host --ipc=host --volume /:/host busybox chroot /host` - From [this](https://zwischenzugs.com/2015/06/24/the-most-pointless-docker-command-ever/) post. This will drop you into a root shell on the host.

There's also a containerd socket at `/run/containerd/containerd.sock` if that's available you can follow the commands from the "Executing Binaries" section of [this post](https://raesene.github.io/blog/2025/09/12/beyond-the-surface/) to use `ctr` to run a new privileged container on the host.

## Other Avenues of attack

Avenues of attack that aren't directly related to breaking out of the container

### keyctl-unmask

As described in [this post](https://www.antitree.com/2020/07/keyctl-unmask-going-florida-on-the-state-of-containerizing-linux-keyrings/) it may be possible to get keys from the kernel keyring on a Docker host, and use those for breakouts or other access to the host or related machines.

