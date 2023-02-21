# Sidecar
- SIDE CAR CONTAINERS CAN TALK TO EACH OTHER USING LOCALHOST
- CAN SHARE DATA
- `k logs <POD_NAME> -c <container_name>`


```YAML
apiVersion: v1

kind: Pod

metadata:

  name: bb

  labels:

    purpose: test

spec:

  containers:
  - name : sidecar
    image: busybox
    command: ["/bin/sh"]
    args: ["-c", "while true; do echo hello; sleep 10;done"]

  - name: bb

    image: busybox

    command: ['sh', '-c', 'echo "$var"']

    # args: ["HOSTNAME", "KUBERNETES_PORT"]

  restartPolicy: OnFailure

```



# INIT CONTAINERS
- run once in the begining and exits
- main container starts afterwards
- main container wont start if init keeps failing
- can be seen when run `k get pod`
- while init containers are running its logs can be found without `-c` flag using `k logs <pod_name>`

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done"]
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup mydb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done"]

```

# Exposing POD info into containers  via env or via volume mounts (downwards API)
- POD SA
- POD IP
- POD NAMESPACE

via ENV
```YAML
apiVersion: v1

kind: Pod

metadata:

  name: dapi-envars-fieldref

spec:

  containers:

    - name: test-container

      image: registry.k8s.io/busybox

      command: [ "sh", "-c"]

      args:

      - while true; do

          echo -en '\n';

          printenv MY_NODE_NAME MY_POD_NAME MY_POD_NAMESPACE;

          printenv MY_POD_IP MY_POD_SERVICE_ACCOUNT;

          sleep 10;

        done;

      env:

        - name: MY_NODE_NAME

          valueFrom:

            fieldRef:

              fieldPath: spec.nodeName

        - name: MY_POD_NAME

          valueFrom:

            fieldRef:

              fieldPath: metadata.name

        - name: MY_POD_NAMESPACE

          valueFrom:

            fieldRef:

              fieldPath: metadata.namespace

        - name: MY_POD_IP

          valueFrom:

            fieldRef:

              fieldPath: status.podIP

        - name: MY_POD_SERVICE_ACCOUNT

          valueFrom:

            fieldRef:

              fieldPath: spec.serviceAccountName

  restartPolicy: Never

```

