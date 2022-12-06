# PCI Container Orchestration Guidance for Kubernetes

In September 2022 the PCI council released [Guidance for Containers and Container Orchestration Tools](https://blog.pcisecuritystandards.org/new-information-supplement-guidance-for-containers-and-container-orchestration-tools) which is intended to help organizations who use tools like Docker and Kubernetes in payment systems, do so in a secure fashion. The guidance should also be useful as a general guide to Kubernetes security hardening.

One of the key parts of the document is a table of risks and best practices which span 16 areas.

The guidance is fairly generic, so to help apply this specifically to Kubernetes I've been writing a series of blogs to look at each of these areas. This page provides a handy index for those posts. As this guidance will be used by assessors as well, I made some notes on the [challenges of doing security assessments on Kubernetes](https://raesene.github.io/blog/2022/09/20/Assessing-Kubernetes-Clusters-for-PCI-Compliance/).

- [Authentication](https://raesene.github.io/blog/2022/10/01/PCI-Kubernetes-Section1-Authentication/)
- [Authorization](https://raesene.github.io/blog/2022/10/08/PCI-Kubernetes-Section2-Authorization/)
- [Workload Security](https://raesene.github.io/blog/2022/10/15/PCI-Kubernetes-Section3-workload-security/)
- [Network Security](https://raesene.github.io/blog/2022/10/23/PCI-Kubernetes-Section4-network-security/)   
- [PKI](https://raesene.github.io/blog/2022/10/29/PCI-Kubernetes-Section5-PKI/)
- [Secrets Management](https://raesene.github.io/blog/2022/11/06/PCI-Kubernetes-Section6-Secrets-Management/)
- [Container Orchestration Tool Auditing](https://raesene.github.io/blog/2022/11/12/PCI-Kubernetes-Section7-Auditing/)
- [Container Monitoring](https://raesene.github.io/blog/2022/11/19/PCI-Kubernetes-Section8-monitoring/)
- [Runtime Security](https://raesene.github.io/blog/2022/11/27/PCI-Kubernetes-Section9-Runtime-Security/)
- [Patching](https://raesene.github.io/blog/2022/12/03/PCI-Kubernetes-Section10-Patching/)