# Container Breakout Vulnerabilities

A list of CVEs in the various parts of the container stack that could allow for unauthorised access to host resources (e.g. filesystem, network stack) from a container.

With Linux issues it can be a bit tricky to say if they're container escapes or not so generally looking at ones where container escape has been demonstrated.


## Linux CVEs

- [CVE-2022-0847](https://dirtypipe.cm4all.com/) - a.k.a DirtyPipe. Vulnerability allows for overwrite of files that should be read-only. Basic container information [here](https://blog.aquasec.com/cve-2022-0847-dirty-pipe-linux-vulnerability), full container breakout PoC writeup [here](https://www.datadoghq.com/blog/engineering/dirty-pipe-container-escape-poc/) and code [here](https://github.com/DataDog/dirtypipe-container-breakout-poc)
- [CVE-2022-0492](https://access.redhat.com/security/cve/cve-2022-0492). Vulnerability in cgroup handling can allow for container breakout depending on isolation layers in place. Container breakout details [here](https://unit42.paloaltonetworks.com/cve-2022-0492-cgroups/)
- [CVE-2022-0185](https://www.willsroot.io/2022/01/cve-2022-0185.html) - Local privilege escalation, needs CAP_SYS_ADMIN either at the host level or in a user namespace
- [CVE-2021-3490[(https://www.crowdstrike.com/blog/exploiting-cve-2021-3490-for-container-escapes/) - Vulnerability in the eBPF subsystem allows for container breakout if the container has CAP_BPF (see also [proof of concept](https://github.com/chompie1337/Linux_LPE_eBPF_CVE-2021-3490)
- [CVE-2021-31440](https://www.zerodayinitiative.com/blog/2021/5/26/cve-2021-31440-an-incorrect-bounds-calculation-in-the-linux-kernel-ebpf-verifier) - eBPF incorrect bounds calculation allows for privesc.
- [CVE-2021-22555](https://google.github.io/security-research/pocs/linux/cve-2021-22555/writeup.html) - Linux LPE used to break out of Kubernetes pod by the researcher
- [CVE-2017-1000112](https://capsule8.com/blog/practical-container-escape-exercise/) - memory corruption in UFO packets.
- [CVE-2016-5195](https://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2016-5195) - (a.k.a 'dirty CoW') - race condition leading to incorrect handling of Copy on Write.
- [CVE-2017-5123](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-5123) - vulnerability in the WaitID syscall.

## runc CVEs

- [CVE-2024-21626](https://snyk.io/blog/leaky-vessels-docker-runc-container-breakout-vulnerabilities/) - a.k.a. Leaky Vessels, allows for container escape if running a malicious image, or building a malicious Dockerfile, directly, or indirectly (i.e. through a `FROM` instruction)
- [CVE-2021-30465](http://blog.champtar.fr/runc-symlink-CVE-2021-30465/) - race condition when mounting volumes into a container allows for host access.
- [CVE-2019-5736](https://blog.dragonsector.pl/2019/02/cve-2019-5736-escape-from-docker-and.html) - overwrite runc binary on the host system at container start, see also [explanation](https://unit42.paloaltonetworks.com/breaking-docker-via-runc-explaining-cve-2019-5736/)
- [CVE-2016-9962](https://bugzilla.suse.com/show_bug.cgi?id=1012568#c2) - access to a host file descriptor allows for breakout.

## Containerd CVEs
- [CVE-2022-23648](https://bugs.chromium.org/p/project-zero/issues/detail?id=2244) - Vuln in volume mounting allows for arbitrary file read from the underlying host, leading to likely indirect container breakout. PoC exploit [here](https://github.com/raesene/CVE-2022-23648-POC)

## CRI-O CVEs
- [CVE-2022-0811](https://www.crowdstrike.com/blog/cr8escape-new-vulnerability-discovered-in-cri-o-container-engine-cve-2022-0811/) - Vulnerability in setting sysctls in k8s/OpenShift manifests allows for container breakout. Linked post has full PoC details.

## Docker CVEs

- [CVE-2024-23653](https://snyk.io/blog/cve-2024-23653-buildkit-grpc-securitymode-privilege-check/) - missing privilege check in Docker BuildKit allowing for container escape when building an image using a malicious Dockerfile or upstream image (i.e. when using FROM)
- [CVE-2024-23651](https://snyk.io/blog/cve-2024-23651-docker-buildkit-mount-cache-race/) - race condition in Docker BuildKit allowing for container escape when building an image using a malicious Dockerfile or upstream image (i.e. when using FROM)
- [CVE-2021-21284](https://github.com/moby/moby/security/advisories/GHSA-7452-xqpj-6rpc) - When using user namespaces, a user with some access to the host filesystem can modify files which they should not have access to.
- [CVE-2019-14271](https://unit42.paloaltonetworks.com/docker-patched-the-most-severe-copy-vulnerability-to-date-with-cve-2019-14271/) - An issue in the implementation of the Docker "cp" command can lead to full container escape when exploited by an attacker

## Kubernetes CVES
- [CVE-2021-25741](https://groups.google.com/g/kubernetes-security-announce/c/nyfdhK24H7s) - race condition in when using hostPath volumes allows for privileged access to host filesystem
- [CVE-2021-25737](https://groups.google.com/g/kubernetes-security-announce/c/xAiN3924thY) - unauthorized access to host network stack by using endpoint slices
- [CVE-2017-1002101](https://github.com/kubernetes/kubernetes/issues/60813) - subpath volume mount handling allows arbitrary file access in host filesystem
- [CVE-2017-1002102](https://github.com/kubernetes/kubernetes/issues/60814) - Arbitrary deletion of files on the host possible when using some Kubernetes volume types

## Cloud provider tooling

- [CVE-2021-3100, CVE-2021-3101, CVE-2022-0070, CVE-2022-0071](https://unit42.paloaltonetworks.com/aws-log4shell-hot-patch-vulnerabilities/) AWS' hot patch package for log4shell allowed for container escape, if a container contains a malicious "java" executable which will be run uncontainerized.

## Reference Links

- [Linux Kernel Exploitation](https://github.com/xairy/linux-kernel-exploitation/blob/master/README.md) - Extensive maintained list of links relating to Linux Kernel Exploitation
- [Hacking Kubernetes](https://hacking-kubernetes.info/) - Hacking Kubernetes book site has a set of Container Breakout CVEs
