# Container Security Standards

There are a number of sets of guidance which are provided by various bodies and can be useful in understanding how to secure container environments. Generally speaking, they fall into two categories, Compliance standards and hardening guides. The difference is that compliance standards generally seek to give precise recommendations at the level of setting specific parameters and file permissions for a specific product, where hardening guides tend to cover more ground at a higher level Whilst hardening guides may have some specific details, they don't try to comprehensively cover all settings related to security in one product.

## Complaince standards

- [CIS Benchmark for Kubernetes](https://www.cisecurity.org/benchmark/kubernetes) - There are benchmarks for a number of distributions. The main one covers Kubeadm and there are also benchmarks for EKS, AKS, GKE, OpenShift, ACK and OKE.
- [CIS Benchmark for Docker](https://www.cisecurity.org/benchmark/docker) - Worth noting that this specifically relates to Docker as a stand alone container engine, some of the recommendations will not apply when it's used as part of a Kubernetes cluster.
- [DISA STIG for Kubernetes](https://stigviewer.com/stig/kubernetes/2021-04-14/) - Doesn't specify the Kubernetes distribution that's covered, but from the settings, it's likely Kubeadm
- [DISA STIG for Docker Enterprise](https://www.stigviewer.com/stig/docker_enterprise_2.x_linuxunix/) - Whilst it's Docker's (now Mirantis') commercial product, some of the recommendations apply generally to Docker.

## Hardening Guides

- [NSA Kubernetes Hardening Guide](https://media.defense.gov/2022/Aug/29/2003066362/-1/-1/0/CTR_KUBERNETES_HARDENING_GUIDANCE_1.2_20220829.PDF) - Covers Kubernetes hardening and some general related topics like Kubernetes auditing and threat detection
- [PCI recommendations for containers and container orchestration](https://docs-prv.pcisecuritystandards.org/Guidance%20Document/Containers%20and%20Container%20Orchestration%20Tools/Guidance-for-Containers-and-Container-Ochestration-Tools-v1_0.pdf?hsCtaTracking=e1f57154-dcd8-4ddc-88bc-099110ddaec7%7C5b4d5cdd-43fb-4107-92c5-cfe752cdc807) - Covers, in a non-product specific way, container and container orchestration security. Whilst it's targeted at PCI environments, most of the guidance applies to container environments in general, there's some commentary on it [here](https://raesene.github.io/blog/2022/09/10/PCI-Guidance-for-containers-and-container-orchestration-tools/)