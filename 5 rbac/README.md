# API server handles auth
https://kubernetes.io/docs/reference/access-authn-authz/rbac/
https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/

## keywords with short names

- role
- rolebinding (rb)
- clusterrole ()
- clusterrolebinding ()
- action
- verbs [get, create, list, delete, update, watch, patch]
- resources
- apiGroup [apiVersion]
- resourceNames
- user (not an own K8s component)
- group
- serviceaccount (sa) [auth for application users]
- namespace
- apiGroups
- subjects

## Role

`k create role ci-cd --verb=create, list,update --resource=deployments.apps,services --dry-run=client -o yaml > cicd-role.yaml`

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
- apiGroups: [""] # "" indicates the core API group
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
  resourceNames: ["my-secret"] # permission for specific rersource
```


`kubectl create role pod-reader --verb=get --verb=list --verb=watch --resource=pods`
`kubectl create role pod-reader --verb=get --resource=pods --resource-name=readablepod --resource-name=anotherpod`
`kubectl create role foo --verb=get,list,watch --resource=replicasets.apps` with api groups

`k apply -f role.yaml`
`kubectl create sa jenkins`
`k get roles`
`k describe role dev`



## API groups
deployment -> "apps"
extensions (api group) ??

Can we use multiple api groups in array ?? yes



### Rolebinding

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
# You can specify more than one "subject"
- kind: User
  name: jane # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
- kind: group
  name: dev # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```


```bash
kubectl create rolebinding default-view \
  --clusterrole=view \
  --serviceaccount=my-namespace:default \
  --namespace=my-namespace
```


### Grant a role to all service accounts in a namespace
```bash
kubectl create rolebinding serviceaccounts-view \
  --clusterrole=view \
  --group=system:serviceaccounts:my-namespace \
  --namespace=my-namespace

```

### Crole
- manage namespaces
- manage PV
- node operations

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: secret-reader
rules:
- apiGroups: [""]
  #
  # at the HTTP level, the name of the resource for accessing Secret
  # objects is "secrets"
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```

`kubectl describe clusterrole dev-cr`

### CRB

```YAML
apiVersion: rbac.authorization.k8s.io/v1
# This cluster role binding allows anyone in the "manager" group to read secrets in any namespace.
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: Group
  name: manager # Name is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```


## External Sources for Authentication
- Static token file  `kube-apiserver --token-auth-file=/users.csv` # `token,user_1,u001,group1`
- certificates
- LDAP (OIDC)

# Auth modes (can enable more than 1 auth modes at once)
- Node
- ABAC
- RBAC
- Webhook

## where to enable auth modes?
- API server manifest files
- update --authorization-mode=Node,RBAC
- edit via sudo vim /etc/kubernetes/manifests/kube-apiserver.yaml

or  In docs use this and verify
`kube-apiserver --authorization-mode=Example,RBAC --other-options --more-options`



## Can-I , validate Auth
`k auth can-i create deployments -n dev`

Admins can test access of other users `kubectl auth can-i list secrets --namespace dev --as dave`

`kubectl auth can-i create service --as system:serviceaccount:default:jenkins -n default`


- aceess to all pods in namespace
- access to all resources in namespace
- access to pods with all actions in a NS
- aceess to all pods in all namespaces

`verbs: [*]` ??

_____________________________________________________



# certificates

Public key / root ca file=> `.crt`
Private key => `.key` or `.pem`

client certificates ?
server certificates ?

certs are stored in :
`/etc/kubernetes/pki`
`/etc/kubernetes/pki/etcd`

demo.crt
demo.key

## who signed ?
- kubeadm generated a CA for cluster
- all clients have a copy of K8s CA thats why they trust auto generated CA
`kubernetes-ca` or cluster root ca
`etcd-ca`


## Process of signing a client certificate (Goal is create a CRT file for the client)


- create key pair (Private key and CSR)
 - `openssl genrsa -out tom.key 2048` Pvt key
 - `openssl req -new -key tom.key -subj "/CN=tom" -out tom.csr` CSR
 - `cat tom.csr | base64 | tr -d "\n"`

- send CSR using K8s certificates api

 ```bash
 cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: myuser
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZVzVuWld4aE1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTByczhJTHRHdTYxakx2dHhWTTJSVlRWMDNHWlJTWWw0dWluVWo4RElaWjBOCnR2MUZtRVFSd3VoaUZsOFEzcWl0Qm0wMUFSMkNJVXBGd2ZzSjZ4MXF3ckJzVkhZbGlBNVhwRVpZM3ExcGswSDQKM3Z3aGJlK1o2MVNrVHF5SVBYUUwrTWM5T1Nsbm0xb0R2N0NtSkZNMUlMRVI3QTVGZnZKOEdFRjJ6dHBoaUlFMwpub1dtdHNZb3JuT2wzc2lHQ2ZGZzR4Zmd4eW8ybmlneFNVekl1bXNnVm9PM2ttT0x1RVF6cXpkakJ3TFJXbWlECklmMXBMWnoyalVnald4UkhCM1gyWnVVV1d1T09PZnpXM01LaE8ybHEvZi9DdS8wYk83c0x0MCt3U2ZMSU91TFcKcW90blZtRmxMMytqTy82WDNDKzBERHk5aUtwbXJjVDBnWGZLemE1dHJRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBR05WdmVIOGR4ZzNvK21VeVRkbmFjVmQ1N24zSkExdnZEU1JWREkyQTZ1eXN3ZFp1L1BVCkkwZXpZWFV0RVNnSk1IRmQycVVNMjNuNVJsSXJ3R0xuUXFISUh5VStWWHhsdnZsRnpNOVpEWllSTmU3QlJvYXgKQVlEdUI5STZXT3FYbkFvczFqRmxNUG5NbFpqdU5kSGxpT1BjTU1oNndLaTZzZFhpVStHYTJ2RUVLY01jSVUyRgpvU2djUWdMYTk0aEpacGk3ZnNMdm1OQUxoT045UHdNMGM1dVJVejV4T0dGMUtCbWRSeEgvbUNOS2JKYjFRQm1HCkkwYitEUEdaTktXTU0xMzhIQXdoV0tkNjVoVHdYOWl4V3ZHMkh4TG1WQzg0L1BHT0tWQW9FNkpsYWFHdTlQVmkKdjlOSjVaZlZrcXdCd0hKbzZXdk9xVlA3SVFjZmg3d0drWm89Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth
EOF
  ```

- K8s sign certificate for you

`kubectl get csr`
`kubectl certificate --help`

- K8s admin approves certificate
`kubectl certificate approve tom`

- get CRT file or signed Certificate
`kubectl get csr`
`kubectl get csr/tom -o yaml`

copy `certificate:` data

To get `tom.crt` file echo it and decode it

`echo 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZVzVuWld4aE1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTByczhJTHRHdTYxakx2dHhWTTJSVlRWMDNHWlJTWWw0dWluVWo4RElaWjBOCnR2MUZtRVFSd3VoaUZsOFEzcWl0Qm0wMUFSMkNJVXBGd2ZzSjZ4MXF3ckJzVkhZbGlBNVhwRVpZM3ExcGswSDQKM3Z3aGJlK1o2MVNrVHF5SVBYUUwrTWM5T1Nsbm0xb0R2N0NtSkZNMUlMRVI3QTVGZnZKOEdFRjJ6dHBoaUlFMwpub1dtdHNZb3JuT2wzc2lHQ2ZGZzR4Zmd4eW8ybmlneFNVekl1bXNnVm9PM2ttT0x1RVF6cXpkakJ3TFJXbWlECklmMXBMWnoyalVnald4UkhCM1gyWnVVV1d1T09PZnpXM01LaE8ybHEvZi9DdS8wYk83c0x0MCt3U2ZMSU91TFcKcW90blZtRmxMMytqTy82WDNDKzBERHk5aUtwbXJjVDBnWGZLemE1dHJRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBR05WdmVIOGR4ZzNvK21VeVRkbmFjVmQ1N24zSkExdnZEU1JWREkyQTZ1eXN3ZFp1L1BVCkkwZXpZWFV0RVNnSk1IRmQycVVNMjNuNVJsSXJ3R0xuUXFISUh5VStWWHhsdnZsRnpNOVpEWllSTmU3QlJvYXgKQVlEdUI5STZXT3FYbkFvczFqRmxNUG5NbFpqdU5kSGxpT1BjTU1oNndLaTZzZFhpVStHYTJ2RUVLY01jSVUyRgpvU2djUWdMYTk0aEpacGk3ZnNMdm1OQUxoT045UHdNMGM1dVJVejV4T0dGMUtCbWRSeEgvbUNOS2JKYjFRQm1HCkkwYitEUEdaTktXTU0xMzhIQXdoV0tkNjVoVHdYOWl4V3ZHMkh4TG1WQzg0L1BHT0tWQW9FNkpsYWFHdTlQVmkKdjlOSjVaZlZrcXdCd0hKbzZXdk9xVlA3SVFjZmg3d0drWm89Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=' | base64 -d > tom.crt`

`cat tom.crt` to verify


# use client CRT to connect to cluster

`kubectl cluster-info` to get server ip

`mv ~/.kube/config .` take a backup of default config because below command will override the one

`kubectl --server https://172.31.22.88:6443 --certificate-authority /etc/kubernetes/pki/ca.crt --client-certificate tom.crt --client-key tom.key get pod`  

# Share auth files to user
- client crt
- client key
- CA server crt

or share single kubeconfig file with all of them



# How to create a user ?

##  when you do -subj "/CN=tom"
`openssl req -new -key tom.key -subj "/CN=tom" -out tom.csr`

**user tom is already created**
**Essentially k8s does not manages user**


# use service account to view pods

- create SA `kubectl create serviceaccounts build-robot`
- create token ?? `kubectl get secret sa-secret -o yaml` 
- Copy token `kubectl create token build-robot`
- Decode token `echo token | base64 -d`
- `token=$(echo token | base64 -d)`
- `kubectl --server https://172.31.22.88:6443 --certificate-authority /etc/kubernetes/pki/ca.crt --token $token get pod`  
- Token can also be added in kubeconfig file
- create rolebinding
- `kubectl auth can-i create service --as system:serviceaccount:default:jenkins -n default`


# use json path and more imperative commands to create/update kubeconfig file

`kubectl get csr/myuser -o yaml`
`kubectl get csr myuser -o jsonpath='{.status.certificate}'| base64 -d > myuser.crt`
`kubectl create role developer --verb=create --verb=get --verb=list --verb=update --verb=delete --resource=pods`
`kubectl create rolebinding developer-binding-myuser --role=developer --user=myuser`
`kubectl config set-credentials myuser --client-key=myuser.key --client-certificate=myuser.crt --embed-certs=true`
`kubectl config set-context myuser --cluster=kubernetes --user=myuser`
`kubectl config use-context myuser`







