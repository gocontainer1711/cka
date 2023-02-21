- Pod is restarted auto when crashes
- When container dies , there is no default auto restart , manual restart required

https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
# Liveness Probe (Required during startup , way to tell k8s to check app inside is running)
- exec Probes
- TCP probes
- HTTP probes


# Readiness Probe (Check health or if app is ready to recieve traffic)
- without readiness probe K8s assumes app can take traffic as soon as the container within starts
    - exec Probes
    - TCP probes
    - HTTP probes


## With both these probes these options can be used:
- initialDelaySeconds
- periodSeconds

