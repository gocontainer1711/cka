- Request is what is guaranteed to get.
- Scheduler uses this to figure out where to run pods
- Container is only allowed to go upto a limit
- If not limited container may consume all resources of a node

https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: app
    image: images.my-company.example/app:v4
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  - name: log-aggregator
    image: images.my-company.example/log-aggregator:v6
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

## unit

**Mi => mebibyte**
**m => milicore**

# Imperative Commands

To get pods with reource limits

`k get pod -o jsonpath="{range '.items[*]'} {.metadata.name} {"\t"} {.spec.containers[*].resources} {"\n"}{end}*`