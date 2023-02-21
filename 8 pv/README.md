## Resources
- PV (outside of namespaces, need to be present before POD is created, created by admins)
- PVC (part of namespace, created by devs, name of PVC used in pod template, PV and PVC are not coupled tightly)
- Storage Class (Provisions PV dynamically as and when PVC claims it.  Uses a provisioner to create volumes. Abstraction for Cloud storage)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0003
spec:
  capacity:
    storage: 15Gi
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
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: slow
  selector:
    matchLabels:
      release: "stable"
    matchExpressions:
      - {key: environment, operator: In, values: [dev]}
```


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim

```

## Loose Coupling (Abstraction)

- POD requests volume through PVC
- PVC tries to find apt Volume (directly or via Storage Class, PVC and storage class are linked)
- Claim must be in same NS as pod
- Claim is automatically bound to the most suitable PV
- Volume is bound 
- Volume is mounted into Pod as well as container/sidecars

---           

- pod asks PVC
- PVC asks SC for storage
- SC gives suitable PV to PVC


## Types  (USE Remote Storage in Production)



## Configure EmptyDir Volume (Local machine) - Logging use case
- no need of persistence
- initially empty
- shared across containers of pod
- data deleted permanently when pod dies or removed from node

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: registry.k8s.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
    - mountPath: /var/log
      name: log-vol
  volumes:
  - name: cache-volume
    emptyDir:
      sizeLimit: 500Mi
  - name: log-vol
    emptyDir: {}

```


## Configure HostPath Volume (Local machine)
- create hostPath PV
- create a PVC
- use PVC in Deployment

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"

```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
```

## accessModes

Once (used by one node at a time, can be multiple pods on that node)
Many (used by many nodes at a time)

- ReadWriteOnce (RWO)
- ReadWriteMany (RWX)
- ReadOnlyMany (ROX)
- ReadWriteOncePod (RWOP) [only single pod]

## reclaimPolicy

## Storage Units
- 5Gi
- 8Gi
- 10Gi

## Volume mode
- FileSystem
- Block


## PV with Node Affinity
Possible


## Configmap and secret as volume in POD ??

- CM (Local volumes, managed by K8s and not PVC)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    configMap:
      name: myconfigmap
```
- Secret (Local volumes, managed by K8s and not PVC)


## Imperative Commands

`k get pv`