# securityContext Under Sandboxed Container Runtimes

How do Kubernetes `securityContext` settings behave when a pod runs under a sandboxed runtime such as Kata Containers or gVisor? This page describes an experiment that ran the same set of pods under runc and five sandboxed runtime configurations, and examined each running container to see what each setting actually changed.

## Summary

- Sandboxed runtimes apply securityContext **inside the sandbox** (the guest VM for Kata, the user-space kernel for gVisor). Nothing the pod requests reaches beyond that boundary.
- **Capabilities, user/group IDs, `allowPrivilegeEscalation` and `readOnlyRootFilesystem` work as they do under runc.**
- **`seccompProfile` and `appArmorProfile` are silently ignored by every sandboxed runtime tested** (in their default configurations). Policies that require `RuntimeDefault` seccomp are satisfied on paper, but no syscall filter is applied.
- **`hostNetwork`, `hostPID` and `hostIPC` never expose the node.** hostNetwork pods end up with no network at all, and hostPID only shares the pod's own processes.
- **`privileged: true` grants full control of the sandbox, not the node.**
- **Kubernetes user namespaces (`hostUsers: false`) aren't supported**: the kubelet refuses to start the pod.
- The Kata variants (QEMU with the Rust or Go runtime, and Firecracker) behaved identically to each other, and so did gVisor's two platforms (systrap and KVM).

## Test Environment

| Component | Version / configuration |
|---|---|
| Kubernetes | v1.37.1 (kubeadm), 1 control plane + 2 workers |
| Container runtime | containerd 2.2.1 |
| Node OS / kernel | Ubuntu 24.04, 6.8.0 (AppArmor enabled) |
| Kata Containers | 4.2.0, installed with the kata-deploy Helm chart, default configuration |
| gVisor | runsc release-20261005.0, default configuration |
| CNI | Calico v3.33.0 |

The nodes were VMware Workstation VMs with nested virtualization enabled, so Kata VMs and gVisor's KVM platform ran on nested KVM.

Six runtime configurations were tested:

| Label | RuntimeClass handler | Description |
|---|---|---|
| runc | (default) | Standard runc containers; the baseline for comparison |
| kata-rs | `kata-qemu-runtime-rs` | Kata with QEMU, Rust runtime (the Kata 4.x default) |
| kata-go | `kata-qemu` | Kata with QEMU, legacy Go runtime |
| kata-fc | `kata-fc` | Kata with Firecracker (Go runtime, devmapper snapshotter) |
| gvisor | `runsc` | gVisor, systrap platform (the default) |
| gvisor-kvm | `runsc-kvm` | gVisor, KVM platform |

## Method

Seventeen test cases were defined, each a different securityContext or pod host-namespace setting. Every case was run as an otherwise identical busybox pod under each of the six runtimes (102 pods in total).

Each pod ran a probe script that recorded:

- **Identity and privileges:** UID/GID/groups, capability sets (effective and bounding), `NoNewPrivs`, seccomp mode, AppArmor/LSM label, user namespace mapping
- **Environment:** kernel version, namespaces, PID 1 and visible processes, network interfaces and connectivity, IPC objects, mount options for the root filesystem, `/proc/sys`, `/sys` and cgroupfs, masked paths, device nodes, sysctl values
- **Actions:** roughly 25 operations that a security setting should permit or deny, for example writing the root filesystem, `chown`, `mknod`, mounting a tmpfs, creating namespaces, raw sockets (ping), network administration, binding port 80, writing `/proc/sys`, reading the kernel log, entering PID 1's namespaces, opening `/dev/kvm`, and reading block devices or `/dev/mem`

The key comparison is **each case against the same runtime's baseline pod** (no securityContext at all). This answers "did setting this field change anything under this runtime?", rather than just "what does a privileged pod look like under Kata?".

A node-side view was also captured for every pod, showing how the host sees the workload: the container process for runc, the QEMU or Firecracker process for Kata, and the `runsc` sandbox and gofer processes for gVisor.

To make `hostIPC` observable, a SysV shared memory segment and a `/dev/shm` file were created on each node before the tests.

## Results

The table summarises each setting under runc, Kata (all three variants behaved the same) and gVisor (both platforms behaved the same).

| Setting | runc | Kata (QEMU rs / QEMU go / Firecracker) | gVisor (systrap / KVM) |
|---|---|---|---|
| `capabilities.add` / `drop` | Applied | **Works** as under runc | **Works**, but NET_RAW is removed from every set by default |
| `runAsUser` / `runAsGroup` / `fsGroup` / `supplementalGroups` | Applied | **Works** | **Works** |
| `allowPrivilegeEscalation: false` | `NoNewPrivs` set | **Works** | **Works** |
| `readOnlyRootFilesystem` | Root filesystem read-only | **Works** | **Works** |
| `sysctls` (safe sysctls) | Applied | **Works** | **Not observable**: the sysctls tested don't exist in gVisor's emulated `/proc/sys` (the pod still starts) |
| `seccompProfile: RuntimeDefault` | Filter applied (seccomp mode 2) | **Ignored**: no filter in the container | **Ignored**: no filter in the container |
| `appArmorProfile` | containerd's default profile enforced; `Unconfined` removes it | **Ignored**: guest kernel has no LSM | **Ignored**: no LSM interface |
| `hostIPC` | Node IPC objects visible | **Ignored** | **Ignored** |
| `hostPID` | Node processes visible, PID 1 is the node's init | PID 1 is the pod's `pause` process: pod-level sharing only | PID 1 is the pod's `pause` process: pod-level sharing only |
| `hostNetwork` | Node interfaces, node hostname | **No network**: loopback only, the VM gets no network device | **No network**: no interfaces, sandbox stays in its own network namespace |
| `privileged: true` | All capabilities, AppArmor removed, node devices accessible (disk, `/dev/mem`, `/dev/kvm`), node kernel log readable | All capabilities and writable `/sys`, cgroupfs and `/proc/sys` **inside the guest VM**; no node devices | All capabilities except NET_RAW; node device nodes **appear** but cannot be used |
| `hostUsers: false` | User namespace applied | **Rejected**: pod never starts | **Rejected**: pod never starts |
| `procMount: Unmasked` | Applied (requires `hostUsers: false`) | Rejected | Rejected |
| privileged + hostPID + hostNetwork + hostIPC | Full access to the node | Guest-only privilege, no network, no node access | Sandbox-only privilege, no network, no node access |

## Notable Findings

### Seccomp and AppArmor profiles provide no protection under sandboxed runtimes

In their default configurations, none of the sandboxed runtimes applied the pod's seccomp profile:

- **Kata** ships with `disable_guest_seccomp = true`, and its guest kernel has no LSM, so AppArmor profiles have nothing to act on.
- **gVisor** only applies the OCI seccomp profile when runsc is started with `--oci-seccomp`. There is no AppArmor interface inside the sandbox.

The pods start normally, and the API shows the requested profile, but no filtering happens inside the container. Admission policies that require `RuntimeDefault` seccomp (such as the Pod Security Standards "restricted" profile) are therefore satisfied without any syscall filtering taking place; the sandbox itself is the only boundary.

AppArmor's absence also changes what added capabilities can do. Under runc on Ubuntu, a container with `SYS_ADMIN` added still couldn't mount a filesystem, because containerd's default AppArmor profile blocks mounts. Under every sandboxed runtime, the same container could.

### `privileged` means control of the sandbox, not the node

- **Kata:** a privileged container gets every capability and writable `/sys`, cgroupfs and `/proc/sys`, but all of that applies to the guest VM. It received none of the node's devices, because kata-deploy configures containerd with `privileged_without_host_devices = true` for the Kata runtimes. With those capabilities the container has full control of its own VM, including that VM's root filesystem.
- **gVisor:** the `privileged_without_host_devices` setting wasn't applied to the runsc handler, so containerd passed the node's device list to gVisor. The device nodes (`/dev/sda`, `/dev/kvm`, `/dev/mem` and others, over 200 entries) appeared in the container, but gVisor didn't pass any of them through: reading the disk returned no data, and opening `/dev/kvm` or `/dev/mem` failed with "No such device or address".
- **runc**, for comparison, gave the privileged container raw access to the node's disk, `/dev/mem` and `/dev/kvm`, plus the node's kernel log.

### Default Kata containers can read their own VM's disk

A Kata container with **no** securityContext could create a device node for the guest VM's root disk and read it. That works because default containers hold `CAP_MKNOD`, and the guest doesn't restrict device access for ordinary containers the way the runc device cgroup does on the node. This was observed with QEMU and Firecracker alike.

The exposure is limited to the pod's own VM, which holds the Kata guest image rather than node data. Dropping `MKNOD` (or all capabilities) or running as a non-root user prevents it. Write access wasn't tested. gVisor refused `mknod` of device nodes entirely, even for root with `CAP_MKNOD`.

### `hostNetwork` pods have no network

Neither runtime family can attach a sandbox to the node's network namespace:

- **Kata** starts the VM without any network device, so the container sees only loopback.
- **gVisor**'s sandbox stays in a private network namespace with no interfaces.

In both cases the pod couldn't reach the Kubernetes API server. This fails safe, but workloads that depend on `hostNetwork` (node agents, some CNI or ingress components) won't work under these runtimes.

### `hostPID` and `hostIPC` don't reach the host

With `hostPID: true`, sandboxed containers shared only the pod's own PID namespace: PID 1 was the `pause` container, and no node processes were visible. `hostIPC: true` produced no observable change, and the node's IPC objects weren't visible.

### User namespaces are not supported

Pods with `hostUsers: false` never started under any sandboxed runtime. The kubelet reported, for example:

```
Failed to create pod sandbox: can't set `spec.hostUsers: false`, RuntimeClass handler "kata-qemu-runtime-rs" does not support user namespaces
```

The same message appeared for `kata-fc` and `runsc`. Because `procMount: Unmasked` requires `hostUsers: false`, it can't be used either.

### gVisor-specific behaviour

- **NET_RAW:** removed from all capability sets by default (it can be re-enabled with runsc's `--net-raw`), so `ping` fails even in a default pod.
- **Emulated kernel:** gVisor presents its own kernel (`uname -r` reports `4.19.0-gvisor`). Its `/proc` and `/sys` are minimal: `/proc/partitions`, `/sys/class/net` and many sysctls don't exist, so some checks return "absent" rather than a comparable value.
- **`/proc/sys`:** root inside the sandbox can write values such as `net/ipv4/ip_forward`, which affect only the sandbox's own network stack. runc forbids this even for root.

### Host-side footprint

How the sandbox itself runs on the node never changed with the pod's securityContext, and it differed sharply between runtimes:

| | Kata QEMU | Kata Firecracker | gVisor (sandbox / gofer) |
|---|---|---|---|
| User | root | root | root, inside a private user namespace |
| Capabilities | all | all | about 5 |
| Seccomp | none | filter (Firecracker's built-in) | filter |
| NoNewPrivs | 0 | 1 | 1 |
| Namespaces | host PID and user, pod network, private mount | host PID and user, pod network, private mount | private PID, network and user |

With Kata, the isolation boundary is the hypervisor. With gVisor, the sandbox process is also heavily confined on the host.

### Baseline differences unrelated to securityContext

Even without any securityContext, sandboxed containers differ from runc containers:

- **Kernel log:** the test nodes set `dmesg_restrict=1`, so a default runc container couldn't read the kernel log. Kata guests use `dmesg_restrict=0`, so default Kata containers could read the *guest* kernel log. gVisor returns a synthetic log.
- **Masked paths:** runc masks paths such as `/proc/kcore`, `/proc/keys`, `/proc/acpi` and `/sys/firmware`. Kata masks `/proc/keys` and `/proc/timer_list`, while `/sys/firmware` (and `/proc/acpi` under QEMU) is visible inside the guest. Most of these paths don't exist under gVisor.

## Caveats

- **Default configurations:** all results are for default Kata (kata-deploy) and gVisor configurations. Several findings come from configurable settings (`disable_guest_seccomp`, `privileged_without_host_devices`, `--oci-seccomp`, `--net-raw`), so other deployments may behave differently.
- **Scope:** one run per runtime, with a busybox probe image.
- **Not covered:** SELinux (the nodes ran Ubuntu with AppArmor), unsafe sysctls, `Localhost` seccomp and AppArmor profiles, volume types, and Windows-specific settings.

## Possible Follow-ups

- Re-run with guest seccomp enabled in Kata (`disable_guest_seccomp = false`) and `--oci-seccomp` for gVisor, to check whether profiles are then enforced.
- Set `privileged_without_host_devices = true` for the runsc handler, and confirm the node device nodes no longer appear.
- Compare what the Pod Security Standards "restricted" profile permits against what each sandbox actually enforces.
