# Container & Kubernetes Security Tools

## Container Attack Surface Assessment & Breakout Tools

Useful tools to run inside a container to assess the sandbox that's in use, and exploit some common breakout issues.

* [amicontained](https://github.com/genuinetools/amicontained) -  will show you information about the container runtime and rights you have
* [ConMachi](https://github.com/nccgroup/ConMachi/) - Pentester focused container attack surface assessment tool
* [deepce](https://github.com/stealthcopter/deepce) - Docker Enumeration, Escalation of Privileges and Container Escapes 
* [botb](https://github.com/brompwnie/botb) - Container breakout assessment tool. Can automatically exploit common issues like the Docker socket mount
* [keyctl-unmask](https://github.com/antitree/keyctl-unmask) - Tool that specifically focuses on grabbing kernel keyring entries from containers that allow the keyctl syscall

## Container Vulnerability Scanning Tools

* [Trivy](https://github.com/aquasecurity/trivy) - Vulnerability and IaC scanner
* [Grype](https://github.com/anchore/grype) - Container vulnerability scanner
* [clair](https://github.com/quay/clair) - Container vulnerability scanner

## IaC Scanning Tools that cover container formats

* [Trivy](https://github.com/aquasecurity/trivy) - Vulnerability and IaC scanner
* [Checkov](https://github.com/bridgecrewio/checkov) - IaC scanner
* [KICS](https://github.com/Checkmarx/kics) - IaC scanner

## Docker Security Tools

* [docker bench](https://github.com/docker/docker-bench-security) - Docker CIS Benchmark assessment tool
* [Dockle](https://github.com/goodwithtech/dockle) - Container Image Linter

## Container Runtime Security Tools

* [Tracee](https://github.com/aquasecurity/tracee). Container runtime security tooling
* [Falco](https://github.com/falcosecurity/falco). Container runtime security tooling
* [Kubearmor](https://github.com/kubearmor/KubeArmor). Container runtime security enforcement tool

## Container Registry Tools

* [reg](https://github.com/genuinetools/reg) - Tool for interacting with Container registries
* [regclient](https://github.com/regclient/regclient) - Another tool for interacting with container registries
* [go-pillage-registries](https://github.com/nccgroup/go-pillage-registries) - Tool to search the manifests and configuration for images in a registry for potentially sensitive information


## Container Orchestration Tools

### RBAC Assessment Tools

* [kubectl-who-can](https://github.com/aquasecurity/kubectl-who-can) - Tool that lets you ask "who can" do things in RBAC, e.g. who can get secrets
* [rakkess](https://github.com/corneliusweig/rakkess) - Shows the RBAC permissions available to a user as a list
* [rback](https://github.com/team-soteria/rback) - tool for graphical representation of RBAC permissions in a kubernetes cluster
* [rbac-tool](https://github.com/alcideio/rbac-tool) - RBAC Tool for Kubernetes
* [kubiScan](https://github.com/cyberark/KubiScan) - Tool to scan Kubernetes clusters for risky permissions
* [krane](https://github.com/appvia/krane) - Kubernetes RBAC static analysis & visualisation tool
* [RBAC Police](https://github.com/PaloAltoNetworks/rbac-police) - RBAC policy evaluation.

### Kubernetes Security Auditing Tools

* [kube-bench](https://github.com/aquasecurity/kube-bench) - Tool to assess compliance with the CIS benchmark for various Kubernetes distributions
* [kubescape](https://github.com/armosec/kubescape) - Kubernetes security assessment tool
* [kubeaudit](https://github.com/Shopify/kubeaudit) - Kubernetes security assessment tool focusing on workload security
* [kubesec](https://github.com/controlplaneio/kubesec) - Kubernetes security assessment tool focusing on workload security
* [kubescore](https://github.com/zegl/kube-score) - Kubernetes security and reliability assessment tool focusing on workload security.
* [eathar](https://github.com/raesene/eathar) - Kubernetes security assessment tool focusing on workload security and RBAC.
* [popeye](https://github.com/derailed/popeye) - Kubernetes cluster scanner, looking for possible mis-configurations

### Kubernetes Penetration Testing Tools

* [kube-hunter](https://github.com/aquasecurity/kube-hunter) - Tool to test and exploit standard Kubernetes Security Vulnerabilities
* [kubestrike](https://github.com/vchinnipilli/kubestrike) - Security auditing tool for Kubernetes looks at Authenticated and unauthenticated scanning
* [peirates](https://github.com/inguardians/peirates) - Kubernetes container breakout tool
* [kdigger](https://github.com/quarkslab/kdigger) - Kubernetes breakout/discovery tool
* [teisteanas](https://github.com/raesene/teisteanas) - Tool to create kubeconfig files based on the CertificateSigningRequest API.
* [tòcan](https://github.com/raesene/tocan) - Tool to create kubeconfig files based on the TokenRequest API.
* [kubestroyer](https://github.com/Rolix44/Kubestroyer) - Kubernetes pentesting tool.
* [kubestalk](https://github.com/redhuntlabs/kubestalk) - Black Box Kubernetes Pentesting Tool.
* [kubedagger](https://github.com/yasindce1998/KubeDagger) - Kubernetes offensive framework built in eBPF

### Kubernetes Post-Exploitation Tools

* [kubesploit](https://github.com/cyberark/kubesploit) - Kubesploit is a cross-platform post-exploitation HTTP/2 Command & Control server and agent written in Golang, focused on containerized environments


### Kubelet Tools

* [kubeletctl](https://github.com/cyberark/kubeletctl) - This is a good tool to automate the process of assessing a kubelet instance. If the instance is vulnerable it can also carry out some exploit tasks

### etcd Tools

* [auger](https://github.com/jpbetz/auger) - Tool for decoding information pulled directly from the etcd database

### Security Observability Tools

* [ThreatMapper](https://github.com/deepfence/ThreatMapper). Cloud + Container Security observability

### Training Tools

If you're looking to practice with some of the tools here, in a safe environment, there are projects to help with that.

* [Kube Security Lab](https://github.com/raesene/kube_security_lab) - Basic set of Kubernetes security scenarios implemented in Ansible with KinD
* [Kubernetes Simulator](https://github.com/kubernetes-simulator/simulator) - AWS based Kubernetes cluster environment with different vulnerability scenarios
* [Kubernetes Goat](https://github.com/madhuakula/kubernetes-goat) - Focuses on vulnerable deployments on top of an existing cluster. Also available on line [with Katacoda](https://katacoda.com/madhuakula/scenarios/kubernetes-goat)

### Kubernetes Honeypot projects

* [k8spot](https://github.com/Maddosaurus/k8spot) - Kubernetes honeypot.
* [Helix Honeypot](https://github.com/Zeerg/helix-honeypot) - Kubernetes API server honeypot

### Kubernetes Security Improvement Tools

* [Security Profiles Operator](https://github.com/kubernetes-sigs/security-profiles-operator) - Kubernetes operator for security profiles
* [hardeneks](https://github.com/aws-samples/hardeneks) - Tool to harden EKS clusters
