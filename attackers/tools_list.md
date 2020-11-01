# Tools for Attackers

## Container Attack Surface Assessment

Useful tools to run inside a container to assess the sandbox that's in use.

* [amicontained](https://github.com/genuinetools/amicontained) -  will show you information about the container runtime and rights you have.
* [ConMachi](https://github.com/nccgroup/ConMachi/) - Pentester focused container attack surface assessment tool.

## Container Breakout Tools

* [botb](https://github.com/brompwnie/botb) - Container breakout assessment tool. Can automatically exploit common issues like the Docker socket mount.

## Container Orchestration Tools

### RBAC Assessment Tooling

* [kubectl-who-can](https://github.com/aquasecurity/kubectl-who-can) - Tool that lets you ask "who can" do things in RBAC, e.g. who can get secrets.
* [rakkess](https://github.com/corneliusweig/rakkess) - Shows the RBAC permissions available to a user as a list
* [rback](https://github.com/team-soteria/rback) - tool for graphical representation of RBAC permissions in a kubernetes cluster.

### Kubernetes Security Auditing Tools

* [kube-bench](https://github.com/aquasecurity/kube-bench) - Tool to assess compliance with the CIS benchmark for various Kubernetes distributions.

### Kubernetes Penetration Testing Tool

* [kube-hunter](https://github.com/aquasecurity/kube-hunter) - Tool to test and exploit standard Kubernetes Security Vulnerabilities.

### Kubelet Tooling

* [kubeletctl](https://github.com/cyberark/kubeletctl) - This is a good tool to automate the process of assessing a kubelet instance. If the instance is vulnerable it can also carry out some exploit tasks.

### etcd Tooling

* [auger](https://github.com/jpbetz/auger) - tool for decoding information pulled directly from the etcd database.

### Container Registry Tooling

* [reg](https://github.com/genuinetools/reg) - Tool for interacting with Container registries
* [go-pillage-registries](https://github.com/nccgroup/go-pillage-registries) - Tool to search the manifests and configuration for images in a registry for potentially sensitive information.