# CKA practice Questions - 1

set1 - 9

set 2 - 6,7,8

set 3 - 1,2,4, all

1. Deploy a pod named `nginx-pod` using the `nginx:alpine` image
2. Deploy a messaging pod using the `redis:alpine` image with the labels set to `tier=msg`
3. create namespace named `apx-x1`
4. Get the list of nodes in JSON format and store it in a file at ``/path/x.json``
5. Create a service named `messaging-service` to expose the **messaging** application `within the cluster on port 6379`
6. Create a Deployment named **hr-web-app** using the image `custom:web` with **2 replicas**
7. Create a **static pod** named  **********************************static-busy-box**********************************  on the control plane that uses the **busybox** image and the command <`sleep 1000`>
8. Create a POD in the ****************finance****************  namespace named ****************temp-bus**************** with the image ************redis:alpine************
9. A new application  ****************orange****************  is deployed . There is something wrong with it. Identify and fix the issue.
10. Expose the **hr-web-app** as service **************************************hr-web-app-service**************************************  application on **************port  30082**************  on the nodes of the cluster. The web application listens on **port 8080**.
11. Use JSON Path query to retrieve the **osImages** of all nodes and store it in a file at `/opt/outputs/nodes_os_x4.txt`. The **os Images** are under the the nodeInfo section under status of each node.
12. Create a **Persistent volume** with the given specification. **Name** : `pv-analytics`, **storage** : `100Mi`, **Access modes**: `ReadWriteMany` **Host path**: `/pv/data-analytics`

Solutions

```bash
#prep Goto https://kubernetes.io/docs/reference/kubectl/cheatsheet/

source <(kubectl completion bash) # set up autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
# fro alias to work
alias k=kubectl
complete -o default -F __start_kubectl k

#1
k run nginx-pod --image=nginx:alpine
k get pod
k describe pod nginx-pod

# 2
k run --help
kubectl run hazelcast --image=hazelcast/hazelcast --labels="hazelcasr,env=prod"

#3
k create ns apx-x1

#4

k get nodes -o json > /path/x.json

# 5
k get svc
k expose --help
k expose pod messaging --port 6379 --name messaging-service
k get svc
k describe svc messaging-service

# 6
k create deployment hr-web-app --image=custom:web --replicas=2
k get deploy
k describe deploy hr-web-app

# 7
k run static-busy-box --image=busybox --dry-run=client -o yaml --command -- sleep 1000 > static-busy.yaml

mv static-busy.yaml /etx/kubernetes/manifests/
k get pods
k describe pod static-busy-box

# 8
k run temp-bus --image=redis:alpine -n finnace
k get pods -n finance
k describe pod temp-bus -n finance

# 9
k get pod
#Name  Ready Status                 Restarts Age 
#orange 0/1  Init:crashLoopBackOff  5        4m51s
#init container is crashing
k describe pod orange
k logs orange init-myservice # sh: sleeeep not found
k edit pod orange # wq! A copy of ur changes has been stored to /temp/x.yaml
k replace --force -f /temp/x.yaml
k get pods

#10

k expose deploy hr-web-app --name=hr=hr-web-app-service ==type NodePort --port 8080
# can not pass node port here imperatively
k get svc
k describe svc hr-web-app-service
k edit svc hr-web-app-service # edit node Port and save and it should be applied
k get svc
k describe svc hr-web-app-service

#11
k get nodes -o jsonpath='.items[*].status.nodeInfo.osImage' > /opt/outputs/nodes_os_x4.txt
cat /opt/outputs/nodes_os_x4.txt

#12
# there is no imperative command for PVs
# search for PV in docs

#copy from docs to pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0003
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: slow
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    path: /tmp
    server: 172.17.0.2
# edit 

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /pv/data-analytics

k apply -f pv.yaml
# k create -f pv.yaml
k get pv
k describe pv pv-analytics

```

# CKA practice test - 2

1. Take a backup of the etcd cluster and save it to `/opt/etcd-backup.db`
2. Create a Pod called ****************************redis-storage**************************** with image `redis:alpine` with a Volume of type **emptyDir** that lasts for the life of the pod. The pod should use the mount path as `/data/redis`
3. Create a new pod called ****************************syper-user-pod**************************** with image `busybox:1.28`. Allow the pod to be able to set `system_time`. The container should sleep for 4800 seconds. `SYS_TIME` capabilities for Container?
4. A pod definition file is created at `/root/CKA/user-pv.yaml`. Make use of this manifest file and mount the persistent volume called `pv-1`. Ensure the pod is running and the PV is bound. **mountPath** : `/data`, **persistentVolumeClaim Name**: `my-pvc`
5. Create a new deployment called `nginx-deploy,` with image `nginx:1.16` and **1 replica**. Next upgrade the deployment to version **1.17** using rolling update.
6. Create a new user called **********john********** . Grant him access to the cluster. John should have permission to **create, list, get, update and delete pods** in the `development` namespace. The **private key** exists in the location : `/root/CKA/john.key` and **csr** at `/root/CKA/john.csr`. Important note: As of **K8s 1.19**, the **CertificateSigningRequest** Object expects a `signerName`.
7. Create a **nginx** pod called **nginx-resolver** using image **nginx**, **expose** it internally with a service called **nginx-resolver-service**. Test that you are able to **lookup** to service and pod names from within the cluster. Use the image `busybox:1.28` to create a pod for dns lookup. Record the results in `/root/CKA/nginx.svc` and `/root/CKA/nginx.pod` for the service name and pod name resolutions respectively.
8. Create a static pod on **node01** called **nginx-critical** with image nginx and make sure that it is **recreated/restarted automatically** in case of a failure. Use `/etc/kubernetes/manifests` as the static pod path.

6,7,8

# CKA practice Test -3

Create a new service account with the name
pvviewer . Grant this Service account access to
1ist all PersistentVolumes in the cluster by
creating an appropriate cluster role called
pvviewer-role and ClusterRoleBindint called
pvviewer-role-binding
Next, create a pod called pvviewer with the
image: redis and serviceAccount: pvviewer
in the default namespace
ServiceAccount: pvviewer
ClusterRole: pwviewer-role
ClusterRoleBinding: pwviewer-role-binding
Pod: pwviewer
Pod configured to use ServiceAccount pvviewer

List the InternalIP of all nodes of the cluster.
Save the result to a file /root/CKA/nodeips
Answer should be in the format: InternalIP of
controlplane <space> InternalIP of node01
(in a single line)
Task Completed

Create a pod called multi-pod with two
containers.
Container 1, name: alpha , image: nginx
Container 2: name: beta , image: busybox
command: sleep 4800
Environment Variables:
container 1:
name : alpha
Container 2:
name : beta
Pod Name: multi-pod
Container 1: alpha
Container 2: beta
Container beta commands set correctly?
Container 1 Environment Value Set
Container 2 Environment Value Set

Create a Pod called non-root-pod
image:
redis:alpine
runAsUser: 1000
fsGroup: 2000
Pod non-root-pod fsGroup configured
Pod non-root-pod runAsUser configured

We have deployed a new pod called np-test-
1 and a service called np-test-service
Incoming connections to this service are not
working. Troubleshoot and fix it.
Create NetworkPolicy, by the name ingress-
to-nptest that allows incoming connections to
the service over port 80
Important: Don't delete any current objects deployed
Important: Don't Alter Existing Objects!
NetworkPolicy: Applied to All sources (Incoming
traffic from all pods)?
NetWorkPolicy: Correct Port?
NetWorkPolicy: Applied to correct Pod?

Taint the worker node node01 to be
Unschedulable. Once done, create a pod called
dev-redis ,image redis:alpine,to ensure
workloads are not scheduled to this worker node
Finally, create a new pod called prod-redis and
image: redis:alpine with toleration to be
scheduled on node01
key: env type , value: production , operator: Equal
and effect: NoSchedule
Key=env_type
Value production
Effect = NoSchedule
pod 'dev-redis' (no tolerations) is not scheduled on
node01?
Create a pod 'prod-redis' to run on node01

Create a pod called hr-pod in hr namespace
belonging to the production
environment
and frontend
tier
image: redis:alpine
Use appropriate labels and create all the required objects if
it does not exist in the system already.
hr-pod labeled with
environment-production ?
hr-pod labeled with tier-frontend ?

A kubeconfig file called super. kubeconfig has
been created under /root/CKA . There is
something wrong with the configuration
Troubleshoot and fix it
Fix /root/CKA/super.kubeconfig

We have created a new deployment called nginx-
deploy . scale the deployment to 3 replicas. Has
the replica's increased? Troubleshoot the issue and
fix it.
deployment has 3 replicas

## **Course content**

## **Overview**

## **Q&AQuestions and answers**

## **Notes**

## **Announcements**

## **Reviews**

## **Learning tools**

Create a new note at 0:00

**All lectures**

**Sort by most recent**