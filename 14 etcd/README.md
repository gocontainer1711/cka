https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/
# etcd
- distributed , reliable key value store
- cm, secret, depl, pod, service etc.. info stored inside etcd
- Application data is not stored inside etcd

# backing up
- install etcdctl `sudo apt install etcd-client`
- create backup with etcdctl


```
ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  member list

```
endpoints ??

## taking backup
`etcdctl version`

`ETCDCTL_API=3 etcdctl snapshot save /tmp/snapshotdb.db` will need auth

`cat /etc/kubernetes/manifests/etcd.yaml` copy `ca.crt` `server.crt` `server.key`
`sudo cat /etc/kubernetes/manifests/tcd.yaml | grep /etc/kubernetes/pki`

`sudo ETCDCTL_API=3 etcdctl snapshot save /tmp/snapshotdb.db --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key` 

`ls /tmp/`

## status of snapshot
`sud0 ETCDCTL_API=3 etcdctl --write-out=table snapshot status /tmp/snapshotdb.db`


# Restoring backup
- create resource point from backup
- We have to tell etcd new location
`cat /etc/kubernetes/manifests/etcd.yaml | grep etcd-data`
`export ETCDCTL_API=3`
`etcdctl snapshot restore --data-dir /var/lib/etcd-new-dir /tmp/snapshotdb.db`
`vim /etc/kubernetes/manifests/etcd.yaml | use /var/lib/etcd-new-dir`

- kubelet restarts static pods automatically
- Restart components like `kube-scheduler, kube-controller-manager` kubelet etc to ensure they don't rely on stale data

# Better handling etcd
- default data of etcd stored in hostPath volume 
- We can also run etcd outside cluster (on cloud better way)



