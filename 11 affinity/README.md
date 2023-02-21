# Scheduling Constraints (Do not over use)
- nodeName
- nodeSelector
- nodeAffinity
- podAffinity (Interpod)
- pod AntiAffinity (Interpod)
- taint
- tolerations

## OPerators

- Exists
- DoesNotExists
- In
- NotIn
- Gt
- Lt

## nodeName
https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  nodeName: kube-01
```

`k get pod nginx -o wide`
Some of the limitations of using nodeName to select nodes are:

If the named node does not exist, the Pod will not run, and in some cases may be automatically deleted.
If the named node does not have the resources to accommodate the Pod, the Pod will fail and its reason will indicate why, for example OutOfmemory or OutOfcpu.
Node names in cloud environments are not always predictable or stable.

## nodeSelector
https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes/
`kubectl label nodes <your-node-name> disktype=ssd`
`kubectl get nodes --show-labels`


```yaml
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
    disktype: ssd
```

## nodeAffinity
- more flexible than node name
- if not enough resource , pod wont be scheduled
- more flex operators and expressions

https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: topology.kubernetes.io/zone
            operator: In
            values:
            - antarctica-east1
            - antarctica-west1
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
  containers:
  - name: with-node-affinity
    image: registry.k8s.io/pause:2.0
```

## Taint and Tolerations
https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/

```yaml
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
  tolerations:
  - key: "example-key"
    operator: "Exists"
    effect: "NoSchedule"
```

`kubectl taint nodes node1 key1=value1:NoSchedule`
`kubectl taint nodes node1 key1=value1:NoExecute`
`kubectl taint nodes node1 key2=value2:NoSchedule`


```yaml
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoExecute"
```

In this case, the pod will not be able to schedule onto the node, because there is no toleration matching the third taint. But it will be able to continue running if it is already running on the node when the taint is added, because the third taint is the only one of the three that is not tolerated by the pod.

The default value for operator is Equal.

A toleration "matches" a taint if the keys are the same and the effects are the same, and:

the operator is Exists (in which case no value should be specified), or
the operator is Equal and the values are equal.

### Effect

- **NoSchedule** 
- **PreferNoSchedule** This is a "preference" or "soft" version of NoSchedule -- the system will try to avoid placing a pod that does not tolerate the taint on the node, but it is not required. 
- **NoExecute** Normally, if a taint with effect NoExecute is added to a node, then any pods that do not tolerate the taint will be evicted immediately, and pods that do tolerate the taint will never be evicted. However, a toleration with NoExecute effect can specify an optional tolerationSeconds field that dictates how long the pod will stay bound to the node after the taint is added.

```yaml
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoExecute"
  tolerationSeconds: 3600
```

## Inter Pod Affinity / Anti affinity
https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/

- Dynamic Infrastructure : Nodes get added and removed with time and traffic
- Assume there are 5 master nodes , we want to collect logs of each master node
- With Node selector, 1 master node may have more than 1 pod scheduled to collect logs
- we want 1 replica per node
- anti Affinity (Inter pod) means that don't run a replica of pod A when its **already running** Pod B (A repels B)

- Add pod Affinity with etcd node 
- Add inter pod Anti Affinity to itself

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
spec:
  selector:
    matchLabels:
      app: web-store
  replicas: 3
  template:
    metadata:
      labels:
        app: web-store
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web-store
            topologyKey: "kubernetes.io/hostname"
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - store
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: web-app
        image: nginx:1.16-alpine
```

Note: Pod anti-affinity requires nodes to be consistently labelled, in other words, every node in the cluster must have an appropriate label matching **topologyKey**. If some or all nodes are missing the specified topologyKey label, it can lead to unintended behavior.


# Remeber

Node Affinity => Node Labels
Inter pod Affinity => Pod Labels

## Weight range
- 1 to 100
- sum of weights is the score of a node
- highest score node wins





