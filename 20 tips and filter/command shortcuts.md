`alias k=kubectl`
`export dry="--dry-run=client -o yaml"`
`k create service clusterip myservice --tcp=80:80 $dry > manifest.yaml`

# override container CMD in pod definition
```YAML
apiVersion: v1

kind: Pod

metadata:

  name: command-demo

  labels:

    purpose: demonstrate-command

spec:

  containers:

  - name: command-demo-container

    image: debian

    command: ["printenv"]

    args: ["HOSTNAME", "KUBERNETES_PORT"]

  restartPolicy: OnFailure

```

Example 2

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
