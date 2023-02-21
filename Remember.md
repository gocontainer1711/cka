## switching nodes


`openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout | grep Validity -A2`


nodeSelector:
    kubernetes.io/hostname: k8s-node-01

--endpoints=https://127.0.0.1:2379


<!-- readOnly: true in volume mount of container -->

`k get nodes --sort-by .status.capacity.cpu > /tmp/nodes-capacity.txt`


CSR

`k create role ci-cd --verb=create, list,update --resource=deployments.apps,services --dry-run=client -o yaml > cicd-role.yaml`

Kubelet conf for static pods `cat /etc/kubernetes/kubelet.conf` or `cat /var/lib/kubelet/config.yaml`

Also, switches `--all-namespace` and `kubectl top pod` refer to all namespaces.

Install metrics server ??

'tail -n+1 -f
/var/log/app/demoapp.log


kubectl get deployments -A -o=custom-columns=NAME:.metadata.name,NAMESPACE:.metadata.namespace,IMAGE:..image
kubectl get deployments -A -o=custom-columns=NAME:.metadata.name,NAMESPACE:.metadata.namespace,IMAGE:.spec.template.spec.containers[0].image

kubectl get deployments -A -o=custom-columns=NAME:.metadata.name,NAMESPACE:.metadata.namespace,IMAGE:.spec.template.spec.containers[0].image

 get deployments -A -o jsonpath="{range .items[*]} {"\n"} {@.spec.template.spec.containers[0].image} {"\n"}{end}"

 kubectl get pods -A -o=jsonpath='{range .items[*]}
{..image}{"\n"}'

kubectl get pods -A -o=jsonpath='{range .items[*]}{range
.spec.containers[*]}{.image}{"\n"}{end}{end}'




 etcdctl --endpoints 10.2.0.9:2379 ??

 etcd restore update config in manifest

 kubectl run testpod --image=busybox --rm -it -- nslookup
my-web-svc.alpha > /tmp/svc-lookup.txt
kubectl run testpod --image=busybox --rm -it -- nslookup

**pod-ip-address.alpha.pod.cluster.local** > /tmp/pod-
lookup.txt


kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=cka@2020!

sys_date vs sys_time

allowPrivilegeEscalation:false


kubelet issue

SSH to node k8s-node-01
Check status of kubelet service: systemctl status kubelet
Check logs of kubelet service: journalctl -u kubelet
Fix the error as found in kubelet logs
Start the kubelet service: systemctl start kubelet


## Edit

k get pod
#Name  Ready Status                 Restarts Age 
#orange 0/1  Init:crashLoopBackOff  5        4m51s
#init container is crashing
k describe pod orange
k logs orange init-myservice # sh: sleeeep not found
k edit pod orange # wq! A copy of ur changes has been stored to /temp/x.yaml
k replace --force -f /temp/x.yaml
k get pods

## configure pod to use service account

Deploy a pod which runs nginx image for a web app. However, it needs 1200 seconds to
initialize before nginx container starts. => init containers


## 
/etc/resolv.conf
 kubectl describe svc kube-dns -n kube-system | grep "IP"


 kube-proxy uses iptables in the background to setup required rules.
These rules make it possible to route the requests to different services running in the

Kubernetes cluster. You can iptables-save command to save the rules and iptables-
restore command to restore the backup of iptables rules.

## git push the notes

`kubectl api-resources`

k get nodes -o json | jq -c 'paths' |  grep type |grep -v "metadata"

kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'

--image=alpine/curl
--rm to remove it once container is killed ?

tmux 

open  multiple terminals

--kubeconfig path


FQDN of pod (. chnaged to -)

`k exec busybox -- nslookup svc_fqdn > file`

`k edit pod orange | k replace --force -f file`

endpoint of etcd is in etcd yaml

metric server installation