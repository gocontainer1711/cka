# kube api rest
`kubectl config view`
`kubectl proxy --port=8080 &`
`curl localhost:8080/api/v1...`

- default path of kubeconfig ?
- `mv ~/.kube/config .`

`kubectl cluster-info` to get server ip


- where are control plane manifest files ?
  - for api server
  - for etcd


`openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout` used for ?

`kubectl create rolebinding bob-admin-binding --clusterrole=admin --user=bob --namespace=acme` is it valid ?


# How to create a user ?

##  when you do -subj "/CN=tom"
`openssl req -new -key tom.key -subj "/CN=tom" -out tom.csr`

            **user tom is already created**
            **Essentially k8s does not manages user**

# Token associated with service account ? User service account to view pods?

# how to use specific kube config file ?

# Signer ??

# kubeadm certs renew --csr-only |  What all certs to renew ??


`install kubeadm etcdctl`

- Restart components like `kube-scheduler, kube-controller-manager` kubelet etc to ensure they don't rely on stale data

How to switch nodes, master in exam ?

`apt-get install -y --allow-change-held-packages kubeadm=1.26.0-00` ??

Difference between cordon and drain ?


# Why static pods ?
- kubelet not control manager watches sttaic pods and restarts if it fails
- pod names are suffixed with node host names
- scheduler decides which node, kubelest schedules pods in actual

DNS IP ?

Get containers of a pod ?

/etc/hosts ?

name node ?


Where is kubelet conf ?
`cat /var/lib/kubelet/config.yaml | grep clusterdns -A2`

Internal IP range ?

Port number in ingress and deployment

`nc serviceip port`
- nslookup
- netstat
- ping
- ifconfig
- netstat -lntp

`service kubelet status`
`journalctl -u kubectl` // Extended logs
`which kubelet`

`sudo systemctl daemon-reload`
`sudo systemctl restart kubelet`


update maxsurge ?


======


Validate Documenttaion

**Can this be used ??**
https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#expose



`nc -v <ip> port`