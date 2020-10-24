# Container Terms for Security People

* **Container** - A container is just a running Linux process which has been isolated from the rest of the host, generally using standard Linux security measures like namespaces and cgroups. There are variants on this (like Windows containers and VM based solutions) but usually container == linux process

* **Container Image** - This is a tarball, which has the application that the container will run, plus any supporting software needed to run it. Many containers use Linux packages and root filesystems for their container images, so these need patched/scannned/updated similarly to standard Linux hosts

* **Container Runtime** - Usually Docker. This is essentially command execution as a service. Docker will pull down container images and then launch processes based on those images. Docker doesn't (by default) listen on the network, but can be configured to. At that point it's best thought of as "remote command execution as a service". An important point is that Docker access == root on the host, by design.

* **Container Orchestrator** - Usually Kubernetes. This is a clustering system which ties together sets of host running things like Docker, and lets developers and administrators run containers across all of the hosts in the cluster. Unlike Docker, Kubernetes is a multi-user system with Role Based Acess Control. Effectively Kubernets can be thought of as "distributed remote command execution as a service"