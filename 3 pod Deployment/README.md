# Pod , Deployment, Service
- Pods are ephemeral , don't rely on its ip. Use service name instead
- service load balances traffic
- service load balances using labels [label selector]
- service is not a node process but virtual ip available across cluster
- kube proxy forwards the requests i.e **Request -> service -> kube-proxy -> pod**

# Labels
- connects services to pods
- links service to pods
- optional but best practice
- some labels are auto added by k8s `k get pod --show-labels -n kube-system`
- two services with same name in same ns is not allowed
- two unique pods of same Deployment will have same labels

`k get deployment --show-labels`
`k get svc --show-labels`
`k get svc -l app=nginx` label filter
`k get pod --show-labels`
`k get pod -l app=nginx` label filter
`k logs -l app-nginx` show logs of all pod replicas
`k get node --show-labels`

# Ports

containerPort [in Deployment] = targetPort [in Service]
port [in service is the service port]

# Service types 
- clusterip (default internal service)

# Endpoints [ep]
`k get ep` list endpoints of all services
Service maintains node ips
Kubeproxy maintains service ips

# Kubeproxy
- kube proxy forwards the requests i.e **Request -> service -> kube-proxy -> pod**
- Responsible for maintaining list of service IPS

# Scale
`k scale --help`
`k scale --replicas=3 rs/foo`
`k scale --replicas=3 deployment/mysql`
`k scale --replicas=3 statefulset/mysql`

`k scale deployment mysql --replicas=3`
`k scale deployment mysql --replicas=3 --record` **scale with record**
`k rollout history deployment mysql` # validate scaling
`k scale deployment mysql --replicas=3 --record=true`

# Rollout

`k rollout --help`
`k rollout history --help`
`k rollout history deployment mysql` # validate scaling

# Test nginx app accessibility

nginx-svc clusterIp service is alreday running

`k run testpod --image=busybox`
`k run testnginx --image=nginx`
`k get svc nginx-svc`
`k exec -it testpod -- bash `
curl nginx-svc:80 # may not work because coreDNS has issues
curl http://10.12.233.23:80

# DNS IP $ FQDN
Domain names managed by ICANN (Internet Corporation for Assigned names and numbers)

root domain             =>      `.`                    ^     Root     => cluster.local
top level domain        => `com`, `edu`, `org`         |    Type     => svc
web domain(hostname)    =>   `facebook`                |    NS       => namespace
subdomain               =>    `api`                    |    service  => nginx-svc, service_name
FQDN =                      `api.facebook.com.`        |               `nginx-svc.test-ns.svc.cluster.local`

DNS server ip (that has a map for all service to ip) => `/etc/resolv.conf` Inside the pod
Inside the pod `cat /etc/resolv.conf` => svc.cluster.local test-ns.svc.cluster.local cluster.local 
Same NS only name is sufficient

- Prior to K8s v1.12 the DNS server was kube-dns which then got replaced by CoreDNS
- `kubectl get pod -n kube-system | grep coredns`
- Name resolution problems => CoreDNS issue => check coreDNS pods are running and accessible ?
- `kubectl get pod -n kube-system --show-labels`
- `kubectl get pod -n kube-system -l k8s-app=kube-dns`
- `kubectl logs -n kube-system -l k8s-app=kube-dns`
- Name server ip :  `cat /etc/resolv.conf | grep nameserver` <= name server ip is nothing but ip of kube dns service 
- `k get svc -n kube-system | grep dns`
- kubelet creates `/etc/resolv.conf` for each pod
- `cat /var/lib/kubelet/config.yaml | grep clusterdns -A2`

# Service types 
- clusterip (default internal service)
- clusterip accessible only from within cluster


# IP range
- IP address range is defined by kube-apiserver config
- `cat /etc/kuberenetes/manifests/kube-apiserver.yaml | grep ip-range -A10`
- To update cluster CIDR `sudo vim /etc/kuberenetes/manifests/kube-apiserver.yaml` kubelet periodically scans for changes
- new CIDR blocks is applied to only newly created services

# Imperative Commands

`k get pod test`
`k delete pod test`
`k get deployment`
`k apply -f pod.yaml`
`k get svc` will give port details and ip of service
`k describe svc nginx-svc` it list endpoints , that are nothing but list of ip addresses of pods
`k get all`
`k edit svc nginx-svc`
`k describe deployment nginx-dep`
`k get deployment --show-labels`
`k get svc --show-labels`
`k get svc -l app=nginx` label filter
`k get pod -l app=nginx` label filter
`k logs -l app-nginx` show logs of all pod replicas

`k create svc clusterip test-svc --tcp=80:80 --dry-run=client -o yaml > test-svc.yaml`
`k run nginxpod --image=nginx --labels="app=proxy,env=prod" --dry-run=client -o yaml > nginxpod.yaml`

`k create deployment test-dep --replicas=3 --port=80 --image=nginx --dry-run=client -o yaml > test-dep.yaml`
`k delete deployment test-dep`