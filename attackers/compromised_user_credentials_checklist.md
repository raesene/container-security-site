# Compromised User Credentials

This could apply where you have access to a container with a service token mounted, or have got access to a set of user credentials for a cluster.

## What rights do you have?

The first place to start is to see what access you've got.  You can do this with just `kubectl` . `kubectl auth can-i --list` will list all the permissions the currently configured user has, you can also use `rakkess` to achieve the same result

## Escalating privileges

Depending on what rights your user has, there are a number of possible routes for privilege escalation :-

### Create Daemonset (and exec into container)

If you have create daemonset rights (and there aren't any Pod Security Policies or similar in place) and also pod/exec rights, getting cluster-admin is pretty easy in an unmanaged cluster, and possible in a managed cluster depending on the configuration. Create an instance of the [root daemonset](attacker_manifests.md) and then for each created pod you can exec into a root shell on the node. Once you find a shell on a control plane node, the easiest way to get cluster admin is either to get a pre-created cluster-admin certificate (in `kubeadm` clusters this is located at `/etc/kubernetes/admin.conf`) or to mint a new certificate with a group membership of `system:masters`. Assuming the the certificate authority files are held in `/etc/kubernetes/pki` the following commands can be used to create a user certificate :-

* `openssl genrsa -out user1.key 2048`
* `openssl req -new -key user1.key -out user1.csr -subj "/CN=user1/O=system:masters"`
* `openssl x509 -req -in user1.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out user1.crt`

In a managed cluster, the above procedure won't work, as you don't have shell access to the control plane nodes. For this, one approach would be to get your daemonset, exec a root shell into each node, and then use CRI level commands (so `docker` where it's in use or `crictl` for other environments) to examine each container running on the node and take copies of the service account files looking for a high privileged token to use.


### Create Pod (and exec into container)

The approach here is relatively similar to the daemonset, except that you'll need to target your pod at a control plane node and ensure you tolerate the `NoSchedule` taint that's likely to be set there. 

1. Use `kubectl get nodes` to get a list of nodes (you need list on node resources for this)
2. Modify the [root pod manifest](./attacker_manifests.md) with a `NodeName` field in the spec as described [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodename)
3. create the pod and then exec into it.