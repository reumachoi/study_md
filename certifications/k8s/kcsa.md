## Securing a Cluster

> https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/

1. Kubernetes API

- Use Transport Layer Security (TLS)
- API Authentication/Authorization

2. Capabilities of Workloads

- Limiting resource
- Privileges containers
- prevent from loading unwanted kernel modules
- Restrict network access
- Restrict cloud metadata access
- Select nodes

3. Protecting cluster components from compromise

- Restrict access to etcd
- Enable audit logging
- Restrict alpha or beta features
- Rotate credentials
- Review third party integrations
- Encrypt secrets
- Receiving alerts and reporting

## k8s Authorization modes

> https://kubernetes.io/docs/reference/access-authn-authz/authorization/

1. AlwaysAllow
2. AlwaysDeny
3. ABAC
4. RBAC
5. Node
   1. `system:nodes` group
6. Webhook
7.

## STRIDE

> 아래 보안위협 6가지의 앞글자를 딴 것  
> https://dev.to/pbnj/demystifying-stride-threat-models-230m

1. Spoofing: act of posing, false identity -> **authenticity**

- ex. credentials

2. Tampering: malicious modification of data -> **integrity**

- ex. Bit-flipping attack(change ciphertext), SQL injections, Trojan horse

3. Repudiation: ability of denying -> **non-repudiation**
    - ex.  denies performing a destructive action, erase or truncate log files, unable to determine

4. Information Disclosure: data leaks or data breaches -> **confidentiality**

- ex. eavesdrop, sniff, or read traffic, read data, steal x.509, read sensitive data

5. Denial of Service: to be unavailable to its intended users -> **availability**

- ex. SYN flood, storage full, is left exposed on the Internet

6. Elevation of Privileges:  gaining access that one should not have -> **authorization**

- ex. to gain root-level privileges, can elevate their privileges