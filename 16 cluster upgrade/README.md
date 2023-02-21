# upgrade K8s cluster from K8s v1.24 to K8s 1.25
https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

## Upgrade control plane
- no app downtime
- mangement funcationality is not available
- crashed pods won't be rescheduled
- upgrade each master node one by one

## what components get upgraded (Recommended upgrade to same version)
- kube-apiserver (must have a higher/later version compared to others)
- kube-scheduler (1 version early)
- controller-manager (1 version early)
- kube-proxy (2 version early)
- kubelet (2 version early)
- kubectl (same, 1 high, 1 low than api server)


`apt-get install -y --allow-change-held-packages kubeadm=1.26.0-00`

```bash
sudo -i
# 1. Determine which version to upgrade to
apt update
apt-cache madison kubeadm
# find the latest 1.26 version in the list
# it should look like 1.26.x-00, where x is the latest patch


# 2. kubeadm upgrade
 # replace x in 1.26.x-00 with the latest patch version
 apt-mark unhold kubeadm && \
 apt-get update && apt-get install -y kubeadm=1.26.x-00 && \
 apt-mark hold kubeadm

 kubeadm version

 kubeadm upgrade plan

# replace x with the patch version you picked for this upgrade
sudo kubeadm upgrade apply v1.26.x # use apply in first node only
sudo kubeadm upgrade node # for other nodes, instead of apply

# 3. Drain node
kubectl drain mater --ignore-daemonsets



# 4. upgrade kubelet and kubectl
# replace x in 1.26.x-00 with the latest patch version
apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.26.x-00 kubectl=1.26.x-00 && \
apt-mark hold kubelet kubectl

# 5. Restart the kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# 6. Uncordon the node 
kubectl uncordon <node-to-uncordon>


# [upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.26.x". Enjoy!

# [upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.


```



## Upgrade Data plane | worker nodes

```bash
#1. kubeadm upgrade

# replace x in 1.26.x-00 with the latest patch version
apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.26.x-00 && \
apt-mark hold kubeadm

sudo kubeadm upgrade node


#2. Drain the node
kubectl drain <node-to-drain> --ignore-daemonsets


#3.Upgrade kubelet and kubectl
# replace x in 1.26.x-00 with the latest patch version
apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.26.x-00 kubectl=1.26.x-00 && \
apt-mark hold kubelet kubectl

# 4.Restart the kubelet
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# 5. Uncordon the node
# replace <node-to-uncordon> with the name of your node
kubectl uncordon <node-to-uncordon>

```

# verify cluster status

`kubectl get nodes`
