## setup

`k config view`

______________

5:39
# Q1

Create a deployment called secure-app in secure namespace. Make sure all
containers run with user id 10000 without configuring this setting at container level.
Also, containers should NOT be allowed privileged escalation.
Use busybox image for this deployment.


# Q2
Deploy a pod which runs nginx image for a web app. However, it needs 1200 seconds to
initialize before nginx container starts.


# Q3
Create a NetworkPolicy called allow-port which allows access ONLY to port 8080 .
Note:
1. Pods CANNOT communicate on any port other than 8080
2. Pods running in ALL other namespaces can also access port 8080

# Q4

Create deployment webapp in frontend namespace and scale it to 5 replicas. Use
nginx image.
Any change made to deployment should be recorded .


# Q5
Deploy an Ingress resource called wildcard-host-ingress with following
configuration:
Host *.video.example.com
Service video-service
Path /
Port 8080
-----------------------------------------------------------------------
Host *.web.example.com
Path /
Service web-service
Port 9090


# Q6

List all the pods running in ALL namespaces in following format and save the file to
/tmp/all-pods.txt
POD_NAME NAMESPACE NODE_NAME


# Q7

Create necessary configuration so that all pods with label app=nginx can only be
deployed on node k8s-node-01 .
Use nginx image for pods. Label the node with label env=dev
Note:
These pods should be preferred to be deployed on node k8s-node-01


# Q8

Deploy a pod called secure-pod in secure namespace with image busybox which
sleeps for 2600 seconds.

# Q9

Create a pod called front-app using nginx image. It should be accessible on local
port 80 as well as on node's port. Make sure the port should be same for all nodes in the
cluster.

# Q10

Create a namespace called admin1234

# Q11

Create 5 PersistentVolume with storage set to 8Mi, 8Gi, 1Gi, 1Mi and 10Gi. Now list
all volumes in following format and sort the volumes based on their capacity and
save the output to /tmp/pv-capacity.txt

VOLUME_NAME CAPACITY NAMESPACE

Use template provided below to create PersistentVolume . Keep any random name for
each volume.

```
1 apiVersion: v1
2 kind: PersistentVolume
3 metadata:
4 name: pv0001
5 labels:
6 type: local
7 spec:
8 storageClassName: manual
9 capacity:
10 storage: 10Gi
11 accessModes:
12 - ReadWriteOnce
13 hostPath:
14 path: "/mnt/data"
```




# Q12

Create PersistentVolume called pv-wait that can only be provisioned and mapped
when a PersistentVolumeClaim is created which is further consumed by Pod .
*Create necessary dependent resources.

# Q13

List the DNS IP address(s) used by all pods in ALL namespaces. Save the output to
/tmp/dns-ip.txt
# Q14

Describe all steps required to restore existing etcd backup stored at /tmp/etcd-pre-
snapshot.db . Use following information for restore:

endpoints https://127.0.0.1:2379
CA Cert /tmp/pki/etcd/ca.crt
Server Cert /tmp/pki/etcd/server.crt
Server Key /tmp/pki/etcd/server.key

# Q15


List name of all nodes as per below format and save the output in /tmp/node-
names.txt file.

NODE_NAME
node01
node02


# Q16

Describe all steps required to take etcd backup with below configuration and save the
backup to /tmp/etcd-backup.db
endpoints https://127.0.0.1:2379
CA Cert /tmp/pki/etcd/ca.crt
Server Cert /tmp/pki/etcd/server.crt
Server Key /tmp/pki/etcd/server.key
Check the status of the backup and write the output to /tmp/etcd-backup-status.txt
in table format.


# Q17

Create a NetworkPolicy called deny-outgoing which does NOT allow any outgoing
traffic from secure namespace

# Q18

How many nodes exist without label node-role.kubernetes.io/master ? Count the
number and write in file /tmp/node-count.txt

# Q19

List out all the iptables rules defined for ALL services running in the cluster. Save the
output to /tmp/iptables-rules.txt


# Q20

List all the endpoints in kube-system namespace and save the output to
/tmp/endpoints.txt



