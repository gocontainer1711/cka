## setup

`k config view`

______________

5:39
# Q1

    Create a new deployment called nginx-web with image nginx:1.16 . Change
    the nginx image to 1.17 .
    Scale the deployment to 2 replicas . Once all pods are running, rollback the
    changes back to the previous state.
`k create deployment nginx-web --image=nginx:1.6`
`k set image deployment/nginx-web nginx=nginx:1.6 --record`
`k scale --replicas=2 deployment/nginx-web --record`
`k rollout history deployment/nginx-web`
`k rollout undo deployment/nginx-web`
`kubectl rollout status deployment/nginx-web`
`k get deployment nginx-web`
`k describe deployment nginx-web`

# Q2
    Create a pod with specific volume type which only exists as long as Pod is running
    on that node.
    Additional configuration:
    Pod name : vol-test-pod
    Namespace : web
    Mount Point : /var/www/html
    Image : nginx

`k get ns`
`k create ns web`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: vol-test-pod
  namespace: web
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /var/www/html
      name: demovol
  volumes:
  - name: demovol
    emptyDir: {}
    #   sizeLimit: 500Mi
```

# Q3
    Create a service called webapp-svc listening on port 8080 . Deploy a pod with
    label app=web and image nginx as backend of the service. Deploy an Ingress
    called webapp-ingress and map it with the service webapp-svc on path /web
    * Create necessary dependent resources


`k run nginx -l=app=web --image=nginx`
`kubectl expose pod nginx --port=80 --target-port=8080 --name=webapp-svc`

```yaml
# svc port 8080 webapp-svc
# Pod Label app=web nginx
# Ingress webapp-ingress  path /web

apiVersion: v1
kind: Pod
metadata:
  name: vol-test-pod
  labels:
    app: web
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
        - containerPort: 80
---

apiVersion: v1
kind: Service
metadata:
  name: webapp-svc
spec:
  selector:
    app: web
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 80

---

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: webapp-svc
            port:
              number: 8080
```

# Q4

Deploy a pod called limit-test with resource limit of 1 CPU and 256Mi of
memory . Use image nginx .

`kubectl run limit-test --image=nginx --limits='cpu=1,memory=256Mi'`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: limit-test
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
    #   requests:
    #     cpu: 1
      limits:
       cpu: 1
       memory: "256Mi"
```

# Q5
Create a ConfigMap by name test-cm with following data. Please use
imperative commands for this task.
Key Value
env dev
log_path /var/log

`kubectl create configmap test-cm --from-literal=env=dev --from-literal=log_path=/var/log`

# Q6

    Deploy a network policy called allow-only-namespace in webapp namespace.
    Make sure it allows ALL traffic ONLY from dev namespace.
    Note:
    1. Policy should NOT allow communication between pods in same namespace
    2. Traffic ONLY from dev namespace is allowed on ALL ports
    * Create necessary dependent resources


```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-only-namespace
  namespace: webapp
spec:
  podSelector: {}
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: dev
```

# Q7

Display ONLY names of those nodes which DO NOT have label node-
role.kubernetes.io/master= configured.

There should be only one column called NAME .
`k get node ip-192-168-31-96.us-west-1.compute.internal -o json`
`k get nodes -l="alpha.eksctl.io/cluster-name=eks-from-eksctl" -o custom-columns=NODE_NAME:.metadata.name`

# Q8

Perform required steps to deploy new pods with label env=prod ONLY on k8s-
node-01 .

```yaml
apiVersion: v1
kind: Pod
metadata:
  env: prod
spec:
  nodeName: k8s-node-01 # schedule pod to specific node
  containers:
  - name: nginx
    image: nginx

---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    kubernetes.io/hostname: k8s-node-01

```

# Q9

You have to make k8s-node-01 unavailable for new deployments. Please perform
required steps without interrupting existing deployments.

`kubectl cordon k8s-node-01`

# Q10

Find expiry date of etcd CA certificate and write that to file /tmp/etcd-ca-
expiry.txt

`openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout | grep Validity -A2`

`openssl x509 -in /etc/kubernetes/pki/etcd/ca.crt --text --noout | grep -i "not after" > /tmp/etcd-ca-expiry.txt`

# Q11

Take backup of etcd and store at /tmp/etcd-snapshot.db . You can use existing
CA cert, Server cert and Server key configured for etcd.
* To view existing cert and key location, you can describe etcd pod in kube-system
namespace

```
cat /etc/kubernetes/manifests/etcd.yaml
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
  snapshot save /tmp/etcd-snapshot.db
  ```
  `ETCDCTL_API=3 etcdctl --write-out=table snapshot status /tmp/etcd-snapshot.db`

# Q12

Scale up the application called web-app in dev namespace to 5 replicas. Make
sure to record the change.

`k scale deployment web-app --replicas=5 --record -n dev`

# Q13

Create a PersistentVolume by the name my-pv with storage 10Mi. Also
configure appropriate access mode to Read multiple times.
Create a PersistentVolumeClaim by the name my-pvc and bind with the
PersistentVolume created above.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
#   labels:
#     type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Mi
  accessModes:
    - ReadOnlyMany
#   hostPath:
#     path: "/mnt/data"

---

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
#   storageClassName: manual
  accessModes:
    - ReadOnlyMany
  resources:
    requests:
      storage: 10Mi
```

# Q14

Deploy a pod called busybox with image=busybox and sleep the container for
4600 seconds.
Configure the pod to access the storage in read-only mode. Volume can be
mounted at /data mount point.
* Create necessary resources required to achieve this.

```yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
#   labels:
#     type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Mi
  accessModes:
    - ReadOnlyMany
  hostPath:
    path: "/mnt/data"

---

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
#   storageClassName: manual
  accessModes:
    - ReadOnlyMany
  resources:
    requests:
      storage: 10Mi

---
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: my-pvc
  containers:
    - name: busybox
      image: busybox
      command: ["sh -c sleep 4200"]
    #   ports:
    #     - containerPort: 80
    #       name: "http-server"
      volumeMounts:
        - mountPath: "/data"
          name: task-pv-storage
          readOnly: true


```

# Q15

List all the Server Certificates and CA Certificates used in the cluster along with
their expiry date. Save the output to /tmp/all-certs.txt
`kubeadm certs check-expiration > /tmp/all-certs.txt`



# Q16

Perform required steps to upgrade ONLY k8s-master and k8s-node-01 nodes
to version 1.19.0


# Q17

List all nodes sorted by CPU capacity and store the output in /tmp/nodes-
capacity.txt

`k get nodes`
`k get node node1 -o yaml`
`k get nodes --sort-by=.status.capacity.cpu > /tmp/nodes-capacity.txt`
`k get nodes --sort-by .status.capacity.cpu > /tmp/nodes-capacity.txt`

# Q18

You need to add new user Jim . Please create appropriate private key and
CertificateSigningRequest forJim.
Also approve the CSR generated for userJim

# Q19

A new member Ben joins your company. He is getting on-boarded and needs
access to list and view the deployments . Create a new cluster wide role
called read-only-cluster to grant necessary permissions to Ben .

`kubectl create clusterrole read-only-cluster --verb=get,list,watch --resource=deployments.apps`

`kubectl create clusterrolebinding read-only-cluster-binding --clusterrole=read-only-cluster --user=Ben` 

`kubectl auth can-i list deployments.apps --as Ben`



# Q20

Deploy a static pod called static-nginx-k8s-node-01 with nginx image.


`k run static-nginx --image=nginx --dry-run=client -o yaml > static-nginx.yaml`
`cat /etc/kubernetes/kubelet.conf` or `cat /var/lib/kubelet/config.yaml`
Copy static-nginx.yaml to node k8s-node-01 and place
under staticPodPath parameter defined in kubelet
configuration




