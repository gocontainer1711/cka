# Setting up K8s on bare server

Required Resources
- AWS account
- 3 servers with min 2GB ram& 2 CPU core [1 t2.medium , 2 t2.large]
- List of their public ip
- ssh/ssm access to these machine

# install
- install control plane processes via Kubeadm init
- install addons like Coredns
- install weave-net for Pod networking
- to install worker node use kubeadm join
  

# Accessing cluster Precedence of kubeconfig
- --kubeconfig flag  `kubectl get pod --kubeconfig /etc/kubernetes/admin.conf`
- KUBECONFIG env variable  `export KUBECONFIG=/etc/kubernetes/admin.conf`
- file located in `$HOME/.kube/config`

```bash
echo $(id -u) => 1000
echo $(id -g) => 100
sudo chown $(id -u):$(id -g) ~/.kube/config
ls -l
```

# important commands

`kubectl cluster-info`
`kubetcl get ns`
`kubectl get pod -n kube-system`
`kubectl describe pod <pod_name>` to get pod ip
`kubectl describe pod coredns-jhghgj-igu -n kube-system` to get pod ip
`kubectl get node -o wide` get internal ip of node

# Notes
  
**config maps can't be used from other namespace i.e ns must define own config map**
**node and volume not linked with namespace, live globally in cluster**
**control plane pods are inside kube-system namespace**
**every pod has unique internal ip reachable from all other pods inside the cluster via POD network managed by weakworks for example**
**Pods are necessary abstraction over containers to avoid port conflicts of multiple running containers of same service on a single node**
**Pause/Sandbox containers hold network namespace for main container inside a pod**
**Weave net , the network plugin runs as a daemon set**




