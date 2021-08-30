# Container CVE List

## Kubernetes

- CVE-ID
- Title
- Affected Versions
- Patched versions

|CVE-ID   |CVSS Score   |Title   |Affected Versions   | Patched Versions |
|---|---|---|---|---|
|[CVE-2021-25740](https://groups.google.com/g/kubernetes-security-announce/c/WYE9ptrhSLE)| 3.1 | Endpoint & EndpointSlice permissions allow cross-Namespace forwarding   |  All | No Patch Available (mitigations in advisory) |
|[CVE-2021-25737](https://groups.google.com/g/kubernetes-security-announce/c/xAiN3924thY)| 2.7 | Holes in EndpointSlice Validation Enable Host Network Hijack  |v1.21.0, v1.20.0 - v1.20.6, v1.19.0 - v1.19.10, v1.16.0 - v1.18.18  | v1.21.1, v1.20.7, v1.19.11, v1.18.19  |
|[CVE-2021-25736](https://groups.google.com/g/kubernetes-security-announce/c/lIoOPObO51Q)| 5.8 | Windows kube-proxy LoadBalancer contention  | v1.20.0 - v1.20.5, v1.19.0 - v1.19.9, v1.18.0 - v1.18.17  | v1.21.0, v1.20.6, v1.19.10, v1.18.18 |
|[CVE-2020-8562](https://groups.google.com/g/kubernetes-security-announce/c/-MFX60_wdOY)| 2.2 | Bypass of Kubernetes API Server proxy TOCTOU | v1.21.0, v1.20.0 - v1.20.6, v1.19.0 - v1.19.10, v1.18.0 - v1.18.18  | No Patch Available (mitigations in advisory)  |
|[CVE-2021-25735](https://groups.google.com/g/kubernetes-security-announce/c/FKAGqT4jx9Y)| 6.5 | Validating Admission Webhook does not observe some previous fields | v1.20.0 - v1.20.5, v1.19.0 - v1.19.9, Earlier than v1.18.17  | v1.21.0, v1.20.6, v1.19.10, v1.18.18 |
|[CVE-2020-8554](https://groups.google.com/g/kubernetes-security-announce/c/iZWsF9nbKE8)| 6.3 | Man in the middle using LoadBalancer or ExternalIPs  | All  | No Patch Available (mitigations in advisory) |
|[CVE-2020-8565](https://groups.google.com/g/kubernetes-security-announce/c/9d0gPe7SCM8)| 4.7  | Token Leaks in verbose logs | all v1.19 and earlier  | v1.20.0 |
|[CVE-2020-8559](https://groups.google.com/g/kubernetes-security-announce/c/JAIGG5yNROs)| 6.4  | Privilege escalation from compromised node to cluster | v1.18.0-1.18.5, v1.17.0-1.17.8, v1.16.0-1.16.12, all v1.15 and earlier  | v1.18.6, v1.17.9, v1.16.13 |
|[CVE-2020-8558](https://groups.google.com/g/kubernetes-security-announce/c/B1VegbBDMTE)| 5.4  | Kubernetes: Node setting allows for neighboring hosts to bypass localhost boundary | v1.18.0-1.18.3, v1.17.0-1.17.6, earlier than <1.16.10  | v1.18.4,v1.17.7, v1.16.11 |
|[CVE-2020-8557](https://groups.google.com/g/kubernetes-security-announce/c/cB_JUsYEKyY)| 5.5  | Node disk DOS by writing to container /etc/hosts | v1.18.0-1.18.5, v1.17.0-1.17.8, earlier than  v1.16.13  | v1.18.6, v1.17.9, v1.16.13  |
|[CVE-2020-8555](https://groups.google.com/g/kubernetes-security-announce/c/kEK27tqqs30)| 6.3 | Half-Blind SSRF in kube-controller-manager  | v1.18.0, v1.17.0 - v1.17.4, v1.16.0 - v1.16.8, earlier than < v1.15.11  | v1.18.1, v1.17.5, v1.16.9, v1.15.12  |
|[CVE-2019-11254](https://groups.google.com/g/kubernetes-security-announce/c/wuwEwZigXBc)| 6.5  | denial of service vulnerability from malicious YAML payloads  |v1.17.0-v1.17.2, v1.16.0-v1.16.6, earlier than v1.15.10  | v1.17.3, v1.16.7, v1.15.10  |
|[CVE-2020-8552](https://groups.google.com/g/kubernetes-security-announce/c/2UOlsba2g0s)| 5.3 |  |  |  |
|[CVE-2020-8551](https://groups.google.com/g/kubernetes-security-announce/c/2UOlsba2g0s)  | 4.3  |  |  |  |

- Information from [kubernetes-security-announce](https://groups.google.com/g/kubernetes-security-announce)

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

