## Processes in control Plane
- API server (Entrypoint | Gateway, auth, cluster CIDR )
- Controller Manager (current state == Desired state ? no_action : call_scheduler)
- Scheduler (decides on which node a pod should run, kubelet then starts the pod)
- ETCD (cluster_brain, updates status for each K8s resource in manifests)


## Processes in Worker node
- Container runtime
- Kubelet (interface with CRI, kubelet starts pods, )
- kube proxy (forwards traffic, Intelligent FWD logic) 
- Kubeproxy uses Daemon set to run in every node
  

## Main K8s Components
- Pod
- Deployment
- Service
- Secret
- configMap
- Deployment
- statefulset
- DaemonSet
- Ingress
- Volume (PV, PVC, StorageClass)
- Network Policy
- Namespace


## CKA Passing Score 66% (10/15 or 14/20)

## YAML samples


### sample yaml
```yaml
people:
    - name: andy
      gender: male
    - name: mandy
      gender: female
```

### sample yaml => JSON
```JSON
{
    "people": [{"name": "andy", "gender":"male"},{"name":"mandy", "gender":"female"}]
}
```

### key value

```yaml
app: nginx
port: 8080
text: "kgvj mgjhgjh mgkjg /n bkj @ kiguy" # use double quotes for special characters
```

### comment

```yaml
app: nginx
# this is a comment
port: 8080 
```


### Objects
```yaml
people:
    name: andy
    gender: male
```

### List
```yaml
people:
    - name: andy
      gender: male
    - name: mandy
      gender: female
commands: ["sh", "-c", "sleep 3000"]
args: 
    - sh
    - -c
    - sleep 300
```

### Boolean
```yaml
people:
    - name: andy
      is_male: yes
    - name: mandy
      is_male: on
    - name: sandy
      is_male: true
    - name: lita
      is_male: no
    - name: rita
      is_male: off
    - name: mita
      is_male: false

```

### Multiline strings

```yaml
text: | 
    Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.
config: |
    password : adminpwd
    uname : alpha

    {
        // add more config here
    }
# | is interpreted as it appears in file


```yaml
text: >
    this is a single line string.
    Just for readbility in yaml , I have spread it across lines

```


### Place holders

```yaml
people:
    - name: andy
      is_male: {{.Values.andy.is_male}}
    - name: mandy
      is_male: {{.Values.mandy.is_male}}
```


### env


```yaml
config:
    - name: env
      value: message $secret
```

### multiple yaml in same file

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
---
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
  restartPolicy: Never

```
