# Container Security Site

This is a site with some container security resources. It is (and probably always will be) a work in progress, but hopefully you'll find some useful information. Issues and PRs welcome [on GitHub](https://github.com/raesene/container-security-site).

## General Information

- [Container Reading List](general_information/reading_list.md)
- [Container Terms for Security people](jargon_busters/container_terms_for_security_people.md)
- [Security Terms for Container people](jargon_busters/security_terms_for_container_people.md)
- [Container CVE List](general_information/container_cve_list.md)
- [Container/Kubernetes Security Tools](general_information/tools_list.md)
- [Container Security Standards](general_information/conatiner_security_standards.md)

## Information for Attackers

Resources for pentesters/redteamers and people looking to get more information about the offensive side of container security. Methodologies for testing and some tooling information.

- [External Attacker Checklist](attackers/external_attacker_checklist.md)
- [Compromised Container Checklist](attackers/compromised_container_checklist.md)
- [Compromised User Credentials Checklist](attackers/compromised_user_credentials_checklist.md)
- [Attacker Manifests](attackers/attacker_manifests.md)
- [Container Breakout Vulnerabilities](attackers/container_breakout_vulnerabilities.md)

## Information for Defenders

- [Kubernetes Security Architecture Considerations](defenders/kubernetes_security_architecture_considerations.md)
- [Kubernetes RBAC Good Practice](https://kubernetes.io/docs/concepts/security/rbac-good-practices/) - This docs page gives guidance on avoiding common Kubernetes RBAC pitfalls.
- [Kubernetes API Server Bypass Risks](https://kubernetes.io/docs/concepts/security/api-server-bypass-risks/) - This docs page shows places where it may be possible to bypass the Kubernetes API server, an important point as many security controls are focused on the API server.

## Security Research

Content that relates to container security but doesn't neatly fit in to attacker/defender buckets

- [Node/Proxy Rights in Kubernetes](security_research/node_proxy.md)



## Questions?

you can find me on <a rel="me" href="https://infosec.exchange/@raesene">Mastodon</a>
