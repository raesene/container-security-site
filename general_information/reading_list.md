# Reading List

This is a list of more in-depth articles than can be worth reading to get a better understanding of some of the underlying technologies used by Containers and how things like Kubernetes work.

# Container History

- [Brief Container History](https://blog.aquasec.com/a-brief-history-of-containers-from-1970s-chroot-to-docker-2016)
- Series of posts from 2013 on the history of container engines leading up to Docker
  - [part one](https://web.archive.org/web/20191011153644/http://www.cybera.ca/news-and-events/tech-radar/contain-your-enthusiasm-part-one-a-history-of-operating-system-containers/)
  - [part two](https://web.archive.org/web/20191011151348/https://www.cybera.ca/news-and-events/tech-radar/contain-your-enthusiasm-part-two-jails-zones-openvz-and-lxc/)
  - [part three](https://web.archive.org/web/20191011153243/http://www.cybera.ca/news-and-events/tech-radar/contain-your-enthusiasm-part-three-docker/ )

# Docker Networking

- [Post on Linux Bridging](https://www.thegeekstuff.com/2017/06/brctl-bridge/)
- Posts on Docker MACVLAN
  - [part one](https://web.archive.org/web/20190217173244/https://hicu.be/docker-networking-macvlan-vlan-configuration) 
  - [part two](https://web.archive.org/web/20190130130707/hicu.be/bridge-vs-macvlan)

# Container Storage and Volumes

- [Post detailing how Docker union Filesystems and storage drivers work](https://integratedcode.us/2016/08/30/storage-drivers-in-docker-a-deep-dive/)
- [Post on union file systems](https://www.terriblecode.com/blog/how-docker-images-work-union-file-systems-for-dummies/)
- [Post with some opinions on the available Docker storage graphdrivers](https://blog.jessfraz.com/post/the-brutally-honest-guide-to-docker-graphdrivers/)

# Container Fundamentals

- [Understanding and Hardening Linux Containers](https://research.nccgroup.com/wp-content/uploads/2020/07/ncc_group_understanding_hardening_linux_containers-1-1.pdf) - Whitepaper that goes into detail about container fundamental security.

- Series of posts from Ian Lewis on Container runtimes
  - [part one](https://www.ianlewis.org/en/container-runtimes-part-1-introduction-container-r)
  - [part two](https://www.ianlewis.org/en/container-runtimes-part-2-anatomy-low-level-contai)
  - [part three](https://www.ianlewis.org/en/container-runtimes-part-3-high-level-runtimes)
  - [part four](https://www.ianlewis.org/en/container-runtimes-part-4-kubernetes-container-run)

- [Start of a good series of posts on LWN about Namespaces](https://lwn.net/Articles/531114/)

- Good Series of Articles on Namespaces
  - [part one](http://ifeanyi.co/posts/linux-namespaces-part-1/)
  - [part two](http://ifeanyi.co/posts/linux-namespaces-part-2/)
  - [part three](http://ifeanyi.co/posts/linux-namespaces-part-3/)
  - [part four](http://ifeanyi.co/posts/linux-namespaces-part-4/)

- [Post specifically focusing on the PID namespace and it's relation to containers](https://hackernoon.com/the-curious-case-of-pid-namespaces-1ce86b6bc900)

- [Post from Jessie Frazelle about non-namespaces resources in Linux](https://blog.jessfraz.com/post/two-objects-not-namespaced-linux-kernel/)

- Good series of posts on capabilities from siphos
  - [part one](http://blog.siphos.be/2013/05/capabilities-a-short-intro/)
  - [part two](http://blog.siphos.be/2013/05/restricting-and-granting-capabilities/)
  - [part three](http://blog.siphos.be/2013/05/overview-of-linux-capabilities-part-1/)
  - [part four](http://blog.siphos.be/2013/05/overview-of-linux-capabilities-part-2/)
  - [part five](http://blog.siphos.be/2013/05/overview-of-linux-capabilities-part-3/)

- [This post goes into the slightly obscure topic of "ambient capabilities"](https://s3hh.wordpress.com/2015/07/25/ambient-capabilities/)
- [Post from spender of grsecurity.  Talks about privesc possibilities from certain capabilities](https://forums.grsecurity.net/viewtopic.php?f=7&t=2522&sid=c6fbcf62fd5d3472562540a7e608ce4e#p10271)
- [Post which isn't just on capabilities.  It goes into the idea of building your own containers, and as part of that, covers capabilities](https://blog.lizzie.io/linux-containers-in-500-loc.html)
- [Post about LXC and capabilities](https://blog.iwakd.de/lxc-cap_sys_admin-jessie)
- [Post detailing how Docker uses cgroups](https://shekhargulati.com/2019/01/03/how-docker-uses-cgroups-to-set-resource-limits/)
- [Abusing Privileged and Unprivileged Linux Containers](https://www.nccgroup.com/globalassets/our-research/us/whitepapers/2016/june/abusing-privileged-and-unprivileged-linux-containers.pdf) whitepaper on container breakout techniques, including focus on NET_RAW.

# Docker Security

- [Post on Docker Authorization plugins](https://blog.aquasec.com/docker-1.10-security-features-part-2-authorization-plug-in)


## Container Registry Security 

 - [Post about how registries can be used/abused](https://www.antitree.com/2021/10/abusing-registries-for-exfil-and-droppers/)
 - [ContainerDrip write-up with good details on how registries work](https://darkbit.io/blog/cve-2020-15157-containerdrip)


# Docker Swarm

- [Docker Swarm Certificate management](https://docs.docker.com/engine/swarm/how-swarm-mode-works/pki/)

# runc

- [Post on how to use runc directly](https://danishpraka.sh/2020/07/24/introduction-to-runc.html)

# Kubernetes - General

- [Post that goes into a lot of detail of how Kubernetes works to create a deployment](https://github.com/jamiehannaford/what-happens-when-k8s)
- [Post on What a Kubernetes Pod is](https://www.ianlewis.org/en/what-are-kubernetes-pods-anyway)
- [Post on the pause container](https://www.ianlewis.org/en/almighty-pause-container)

# Kubernetes Networking

- [Website that goes into detail on the Kubernetes networking model](https://k8s.networkop.co.uk/)

- Good set of posts on Container networking setup
  - [part one](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-1-d1ede3322727)
  - [part two](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-2-13fdc6c4e24c)

- [Post on implementing your own CNI plugin in Bash](https://www.altoros.com/blog/kubernetes-networking-writing-your-own-simple-cni-plug-in-with-bash/)

- [Post on how to implement a replacement for kube-proxy](https://arthurchiao.art/blog/cracking-k8s-node-proxy/)
- [Post on how Kubernetes service traffic direction is done](https://dustinspecker.com/posts/iptables-how-kubernetes-services-direct-traffic-to-pods/)

# Kubernetes Security

- [Playlist of Kubernetes pentesting/security videos](https://www.youtube.com/playlist?list=PLKDRii1YwXnLmd8ngltnf9Kzvbja3DJWx)
- [Post on creating reverse shells from Docker and Kubernetes](https://raesene.github.io/blog/2019/08/09/docker-reverse-shells/)
- [Post on getting reverse shells on every node in a cluster](https://raesene.github.io/blog/2019/08/10/making-it-rain-shells-in-Kubernetes/)


# Books 

## Security Books
- [Free Container Security book from Aqua](https://info.aquasec.com/container-security-book)
- [Free Kubernetes Security book from Aqua](https://info.aquasec.com/kubernetes-security)
- [Hacking Kubernetes](https://www.oreilly.com/library/view/hacking-kubernetes/9781492081722/)
- [Cloud Native Security](https://www.amazon.co.uk/Cloud-Native-Security-Chris-Binnie-ebook/dp/B097NHC3BS)

## General Books
- [Free Kubernetes Up and Running from VMWare](https://k8s.vmware.com/kubernetes-up-and-running/)
- [Docker in Practice](https://www.manning.com/books/docker-in-practice-second-edition)
- [Kubernetes in Action](https://www.manning.com/books/kubernetes-in-action-second-edition)