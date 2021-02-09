# Container CVE List

## runc

### CVE-2016-9962 - container escape via ptrace

- [NVD](https://nvd.nist.gov/vuln/detail/CVE-2016-9962)

### CVE-2019-5736 - Runc Privileged Escalation

- [Mitre](CVE-2019-5736)
- [Dragon Sector Blog](https://blog.dragonsector.pl/2019/02/cve-2019-5736-escape-from-docker-and.html) - This is from the people who found the issue, describing it.

### CVE-2019-16884 - Apparmor restriction bypass

- [NVD](https://nvd.nist.gov/vuln/detail/CVE-2019-16884)

## ContainerD

### CVE-2020-15157 - Containerdrip

- [Darkbit Blog Post](https://darkbit.io/blog/cve-2020-15157-containerdrip) - This is from the people who found the issue, describing it.

### CVE-2020-15257 - Abstract Shimmer

- [NCC Group Advisory](https://research.nccgroup.com/2020/11/30/technical-advisory-containerd-containerd-shim-api-exposed-to-host-network-containers-cve-2020-15257/)
- [NCC Group Technical Vulnerability Discussion](https://research.nccgroup.com/2020/12/10/abstract-shimmer-cve-2020-15257-host-networking-is-root-equivalent-again/)

## Docker

### CVE-2017-14992 - DoS via gzip bomb

- [NVD](https://nvd.nist.gov/vuln/detail/CVE-2017-14992)

### CVE-2018-15664 - Docker cp Race Condition

- [Capsule8 blog post](https://capsule8.com/blog/race-conditions-cloudy-with-a-chance-of-r-w-access/)

### CVE-2019-14271 - Docker cp code execution

- [Tenable Blog Post](https://www.tenable.com/blog/cve-2019-14271-proof-of-concept-for-docker-copy-docker-cp-vulnerability-released)

## Kubernetes

### CVE-2018-1002105 - Kubernetes Aggregated API

- [Mitre](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-1002105)
- [Kubernetes Advisory](https://discuss.kubernetes.io/t/kubernetes-security-announcement-v1-10-11-v1-11-5-v1-12-3-released-to-address-cve-2018-1002105/3700)
- [Aqua Blog Post](https://blog.aquasec.com/kubernetes-security-cve-2018-1002105)

### CVE-2019-11253 - YAML DoS

- [Github Issue](https://github.com/kubernetes/kubernetes/issues/83253)

### CVE-2019-11251 - kubectl cp file overwrite

- [Stackrox Blog Post](https://www.stackrox.com/post/2019/09/beyond-patching-fixing-kubectl-cp-cve-2019-11251/)

### CVE-2020-8558 - Localhost remote networking

- [Github Issue](https://github.com/kubernetes/kubernetes/issues/90259)

