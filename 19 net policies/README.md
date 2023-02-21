https://kubernetes.io/docs/concepts/services-networking/network-policies/

- By default , all communication to & from pod is allowed
- NP allows `ip` level, `port` level, `pod` level, `namespace` level restriction and rules
- Both `ingress` and `egress`
- Network Plugin (Eg weaveworks ) implement Network Policies
- A Network Policy belongs to a namespace. A NP of NS `A` can't be applied to pod of NS `B`
- All ports used in NP are containerPort and not service port

## All pod in NS
If `.spec.podSelector` is **empty then NP is applied to all PODs in that NS**


### deny all ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```
### allow-all-ingress
```yaml

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-ingress
spec:
  podSelector: {}
  ingress:
  - {}
  policyTypes:
  - Ingress

```

### default deny all egress
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-egress
spec:
  podSelector: {}
  policyTypes:
  - Egress
```
### Ingress and Egress

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - ipBlock:
            cidr: 172.17.0.0/16
            except:
              - 172.17.1.0/24
        - namespaceSelector:
            matchLabels:
              project: myproject
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978
```

## AND vs OR

- Each rule allows traffic which matches both the **from and port section**
- from and port are coupled
- to and port are coupled
- each from/to block defines a single rule. There can be multiple rules

- **AND inside single from Block**
- **OR** multiple FROM block


## namespaceSelector and podSelector
```yaml
  ingress:
  - from:
    - namespaceSelector: # ns and pod
        matchLabels:
          user: alice
      podSelector:
        matchLabels:
          role: client
```

## namespaceSelector or podSelector

```yaml
  ingress:
  - from:
    - namespaceSelector: # ns or pod
        matchLabels:
          user: alice
    - podSelector:
        matchLabels:
          role: client
```

### range of port


When writing a NetworkPolicy, you can target a range of ports instead of a single port.

This is achievable with the usage of the endPort field, as the following example:



```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: multi-port-egress
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Egress
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 32000
          endPort: 32768
```