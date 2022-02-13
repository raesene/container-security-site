# Container Breakout Vulnerabilities

A list of CVEs in the various parts of the container stack that could allow for unauthorised access to host resources (e.g. filesystem, network stack) from a container.

With Linux issues it can be a bit tricky to say if they're container escapes or not so generally looking at ones where container escape has been demonstrated.


## Linux CVEs
- [CVE-2022-0185](https://www.willsroot.io/2022/01/cve-2022-0185.html) - Local privilege escalation, needs CAP_SYS_ADMIN either at the host level or in a user namesspace
- [CVE-2021-31440](https://www.zerodayinitiative.com/blog/2021/5/26/cve-2021-31440-an-incorrect-bounds-calculation-in-the-linux-kernel-ebpf-verifier) - eBPF incorrect bounds calculation allows for privesc.
- [CVE-2017-1000112](https://capsule8.com/blog/practical-container-escape-exercise/) - memory corruption in UFO packets.
- [CVE-2016-5195](https://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2016-5195) - (a.k.a 'dirty CoW') - race condition leading to incorrect handling of Copy on Write.
- [CVE-2017-5123](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-5123) - vulnerability in the WaitID syscall.

## runc CVEs
- [CVE-2021-30465](http://blog.champtar.fr/runc-symlink-CVE-2021-30465/) - race condition when mounting volumes into a container allows for host access.
- [CVE-2019-5736](https://blog.dragonsector.pl/2019/02/cve-2019-5736-escape-from-docker-and.html) - overwrite runc binary on the host system at container start.
- [CVE-2016-9962](https://bugzilla.suse.com/show_bug.cgi?id=1012568#c2) - access to a host file descriptor allows for breakout.

## Docker CVEs
- [CVE-2021-21284](https://github.com/moby/moby/security/advisories/GHSA-7452-xqpj-6rpc) - When using user namespaces, a user with some access to the host filesystem can modify files which they should not have access to.

## Kubernetes CVES
- [CVE-2021-25741](https://groups.google.com/g/kubernetes-security-announce/c/nyfdhK24H7s) - race condition in when using hostPath volumes allows for privileged access to host filesystem
- [CVE-2021-25737](https://groups.google.com/g/kubernetes-security-announce/c/xAiN3924thY) - unauthorized access to host network stack by using endpoint slices
- [CVE-2017-1002101](https://github.com/kubernetes/kubernetes/issues/60813) - subpath volume mount handling allows arbitrary file access in host filesystem
- [CVE-2017-1002102](https://github.com/kubernetes/kubernetes/issues/60814) - Arbitrary deletion of files on the host possible when using some Kubernetes volume types


## Reference Links

- [Linux Kernel Exploitation](https://github.com/xairy/linux-kernel-exploitation/blob/master/README.md) - Extensive maintained list of links relating to Linux Kernel Exploitation
- [Hacking Kubernetes](https://hacking-kubernetes.info/) - Hacking Kubernetes book site has a set of Container Breakout CVEs