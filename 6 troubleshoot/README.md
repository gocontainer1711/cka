**Bash is superset of sh with More functionality**
**Some images wont have bash**
## Debugging

- Is pod running ? 
- Check pod logs ?
- Is service running ?
- Is service forwording traffic to pod ?
- Is service configured correctly ?
- Is init container failing ?
- Networking issue ?
- Network Policy ??

**Is pod running ?**
`k get pod podname`

**Is pod registered with Service ? Is service forwording traffic to pod ?**
`k get ep`
`k describe svc servicename`

**Check pod logs ?**
`k logs pod_name -c container-name`
`k logs -l app:nginx`
`k logs -l app:nginx -c container-name`

**Is service accessible**
`nc serviceip port`
`ping service_name`

**Check Pod status and recent events**
`k describe pod podname`

**Debug with temp pods busybox**
- Busybox comes with ( **no curl**)
    - nslookup
    - netstat
    - ping
    - ifconfig

- `curlimages/curl`

   

`k run testbox --image=busybox`
`k exec -it testbox -- bash`

## How to keep busybox alive
### start in interactive mode : `k run mybusybox --image=busybox -it`
`k delete pod mybusybox`
`k exec -it mybusybox --sh -c "printenv"`
`k exec -it mybusybox --sh -c "while true; do echo hello; sleep 2s; done"`
`k exec -it mybusybox --sh -c "ping nginx-svc"`
`k exec -it mybusybox --sh -c "netstat -lntp"`

### Override CMD or entrypoint

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod
  name: pod
spec:
  containers:
  - command:
    - sh
    - "-c"
    - sleep 300
    image: busybox
    name: pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod
  name: pod
spec:
  containers:
  - command: ["sh", "-c", "sleep 300"]
    image: busybox
    name: pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod
  name: pod
spec:
  containers:
  - command: ["sh"] # Entrypoint
    args: [ "-c", "sleep 300"] # CMD
    image: busybox
    name: pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

# format output

`k get pod -o wide`
`k get pod -o yaml`
`k get pod -o jsonpath='{.items[*].name}'`
`k get pod -o jsonpath='{range .items[*]}{.name}{"\t"}{.status.pod}{"\n"}{end}'`

`k get pod podname -o custom-columns=POD_NAME:metadata.name,POD_IP:status.ip`


# Troubleshoot kubelet issue 

`k get node` => worker1 not ready
`service kubelet status`
`journalctl -u kubectl` // Extended logs
`which kubelet` /usr/bin/kubelet
`edit execstart to /usr/bin/kubelet at file location in error i.e /etc/systemd/system/kubelet.service.d/10-kubeadm.conf`
`sudo systemctl daemon-reload`
`sudo systemctl restart kubelet`
`k get node` verify

# Troubleshoot kubectl issue 
`vim ~/.kube/config`   
`k cluster-info`
`k config view`
check certificate is correct , match with pki folder ?
server ip ? port ?











