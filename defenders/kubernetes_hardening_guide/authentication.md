# Authentication

Obviously this is a key security control in most environments and something which cluster need to consider. It's also a slightly tricky topic in Kubernetes, as the authentication options provided by the base open source project aren't generally considered suitable for production use, leaving distribution makers and cluster operators with the task of ensuring that secure authentication is in place on their clusters.

## Lack of User Objects

An initial point to be aware of when hardening or operating a Kubernetes cluster is that Kubernetes does not have the concept of a User, instead it relies on the authentication provider(s) to supply the user identity which is then used by Kubernetes authorization system. This has a number of practical consequences that should be considered when hardening a cluster

- There's nothing to stop multiple credentials existing for the same user, either from the same authentication source or multiple sources. This means that non-repudiation in Kubernetes is very difficult without very strict control of the issuance of credentials in every supported authentication mechanism.
- It's not generally practicable to definitively state the number of users who have access to the cluster as there's no record held in the cluster and some of the authentication options don't support enumeration of users (e.g. client certificate).

## API Authentication

Requiring authentication for API access is a pretty obvious first control and there's a couple of ways in which this requirement applies to Kubernetes, the APIs provided by Kubernetes itself and then supporting service APIs. As this fits into the area of API security you can find the details about this in the [API Security](./api_security.md) section.

## User Authentication

Kubernetes provides [a number of authentication options](https://kubernetes.io/docs/reference/access-authn-authz/authentication/) with the open source project. There's essentially two groups here, options that can be used with just the Kubernetes project and options which require external software to be operational.

what's unfortunate is that in the first group, none of the options are suitable for production use (for reasons described below) which means that secure authentication with Kubernetes requires the use of (sometimes quite complex) 3rd party software. This problem is largely mitigated for the main managed distributions as they leverage their IAM systems to fill the gap.

### Static Token File Authentication

This is a relatively basic method of authentication where a file is held on each control plane node in the cluster with a set of tokens used for authentication and then for each token the user and group(s) that they belong to.

From a security standpoint this isn't a great option as it stores the tokens in the clear on disk and any user who gets rights to modify that file can trivially become `cluster-admin` by adding new tokens, or modifying the identity of an existing token.

This authentication method also has problems from a usuability perspective, in that the API server process has to be restarted for any changes to be applied.

### Client Certificate Authentication

Kubernetes makes extensive use of client certificate authentication for component-to-component auth. and also for user authentication. There are a number of security concerns with this approach (as implemented by Kubernetes)

- The private keys are typically embedded in kubeconfig files and do not have passwords set on them, as a result any leakage of a file allows for unauthorized access to the cluster.
- There is [no support for certificate revocation](https://github.com/kubernetes/kubernetes/issues/18982), which means that a lost credential can be abused for its lifetime, unless the cluster certifcate authority rotates *all* keys in the cluster. The official position of the Kubernetes project is that this is best mitigated by the use of short-lived certificates, however in practice the default lifetime for certificates us 1+ years.

### Service Account Tokens

Kubernetes allows for the creation of JWT tokens which can be used to authenticate to the API server. They are primarily intended for use by workloads running inside the cluster, but can in theory be used by external services or indeed users to authenticate to the API server.

There's two essential forms of these tokens. Secret based service account tokens were the default until Kubernetes 1.24 and [bound service account token volumes](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume) are the default since then.

Secrets based services account do not expire and can only be revoked if the service account is deleted (and the `--service-account-lookup` flag is set on the API server).

For bound volume tokens, they're set to expire by default, but longer lasting tokens are possible. Again the token itself can't be revoked in its lifetime without deleting the object that it's bound to.

Hardening recommendations

* Where possible move from secret based service account tokens to bound service account token volumes

* Restrict access to the TokenRequest API provided by Kubernetes which can be used to issue new service account tokens.

### Bootstrap Tokens

Another specialized form of authentication, this time designed to be used by nodes joining the cluster, it is possible, but unadvisable to use bootstrap tokens for general authentication.

From a security standpoint the problems are similar to service account tokens. Essentially bootstrap tokens are just secrets with a specific form and property. Unlike service account tokens it is possible to specify an expiry in the object which would actually be a security benefit!

A deficiency (for general authentication) specific to bootstrap tokens is that they need to follow specific naming conventions in order to operate, reducing the flexibility of user and group naming.

* Avoid using Bootstrap tokens for general authentication

* You can review a cluster for the presence of bootstrap tokens by reviewing secrets in the `kube-system` namespace with a `type` of `bootstrap.kubernetes.io/token`

### OIDC authentication

This is an external authentication mechanism and, for most production clusters, probably the best way to go for user authentication. For on-premises clusters, or other unmanaged distributiopns, it requires an external User store, for example Active Directory and some kind of OIDC provider. Some of the more commonly used OIDC providers are :-

- [Dex](https://dexidp.io/)
- [Keycloak](https://www.keycloak.org/)
- [Open Unison](https://www.tremolosecurity.com/products/openunison)

For managed distributions, typically the best approach is to integrate with your Cloud IAM service.  