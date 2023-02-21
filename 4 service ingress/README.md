https://kubernetes.io/docs/concepts/services-networking/ingress/

# Service types 
- clusterip (default internal service)
- clusterip accessible only from within cluster
- NodePort (Ext service)
- LoadBalancer (Ext service)
- Ingress (Ext service)

# NodePort

- `type:NodePort` in spec
- opens nodePort on each node
- accessible through ip of any node, even when replica is running in only one node
- traffic on nodePort service gets transferred to clusterIP service inside the cluster
- nodePort also creates clusterIp unternal svc
- port range `30000 - 32767`
- `request on any node (nodeip:nodeport) -> nodePort -> clusterip -> pod`
- not ideal for Production

    ## ports :
    - nodePort (port of node eg Ec2 port)
    - port is service port like internal Cluster ip port
    - targetPort which is containerPort
    - Protocol : TCP


# Load Balancer
- `type:LoadBalancer` in spec
- outside of K8s cluster (not created inside cluster)
- load balances traffic among nodes at nodeport service
- `request on LB -> on any node -> nodePort -> clusterip -> pod`
- External ip not configured by K8s
- Cloub provider creates it
- Example : AWS CLB, AWS ALB
- Not scalable because a cluster with multiple services would need multiple Load balancers (Use Ingress instead)
- For each External service
    - a LB required that is costly
    - Domain name mapping
    - A nodeport has to be exposed
    - all configured outside cluster
    - hence not scalable approach

    ## ports : (Same as NodePort)
    - nodePort (port of node eg Ec2 port)
    - port is service port like internal Cluster ip port
    - targetPort which is containerPort
    - Protocol : TCP


# Ingress
- Deployed and available inside cluster
- We need to expose it either as `NodePort` or as `LoadBalancer`
- 1 `NodePort` or as `LoadBalancer` which is a single entry point to the cluster
- `request on LB -> Ingress Controller Pod -> Ingress -> SVC (clusterip) -> pod`
- Map Domain name to LB or node ip for ingress


## Ingress Controller
- evaluates all the rules
- manages redirections
- Entry point to cluster (Many thrird party implementation like Nginx Ingress Controller)
- For TLS ,use secret. secret must be in the same namespace as ingress
- **In production should have 1 replica of ingress controller per worker node**
- Ingress should be in same namespace as the internal service

## paths
    - path: /analytics
      backend: 
            serviceName (name of ClusterIP service)
            servicePort (is service port of internal Cluster ip)

## Ingress Config
```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wildcard-host
spec:
  rules:
  - host: "foo.bar.com"
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: service1
            port:
              number: 80
  - host: "*.foo.com"
    http:
      paths:
      - pathType: Prefix
        path: "/foo"
        backend:
          service:
            name: service2
            port:
              number: 80
```

## Host name wildcard
-------------------------------------------------------------------------------------------
|  Host	        |   Host header	      |  Match?                                            | 
------------------------------------------------------------------------------------------- 
|  *.foo.com	|  bar.foo.com	      | Matches based on shared suffix                     |
-------------------------------------------------------------------------------------------
|  *.foo.com	|  baz.bar.foo.com	  | No match, wildcard only covers a single DNS label  |  
-------------------------------------------------------------------------------------------
|  *.foo.com	|  foo.com	          | No match, wildcard only covers a single DNS label  |
-------------------------------------------------------------------------------------------

Two types of routing
    - **Simple fanout** 1 host => 1 Domain for many services (path wise)
    - **Name based virtual hosting** diff Subdomain for each internal service (use multiple host in rules , each host with diff sudomain)


# TLS
```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-example-ingress
spec:
  tls:
  - hosts:
      - https-example.foo.com
    secretName: testsecret-tls
  rules:
  - host: https-example.foo.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 80
              
```

```YAML
# secret must be in the same namespace as ingress
apiVersion: v1
kind: Secret
metadata:
  name: testsecret-tls
  namespace: default
data:
  tls.crt: base64 encoded cert (file content not path)
  tls.key: base64 encoded key (file content not path)
type: kubernetes.io/tls          
```

# Setup Ingress
- Install ingress Controller
- Setup ingress components

## Install ingress Controller
`helm repo add nginx-stable https://helm.nginx.com/stable`
`helm repo update`
`helm install my-release nginx-stable/nginx-ingress --namespace ingress-nginx --create-namespace`
`helm ls`
`k get pod`
`helm uninstall my-release`

## Setup ingress
`k create ingress my-ingress --rule=host/path=service:port --dry-run=client -o yaml > my-ingress.yaml`


`k get ValidationWebhookConfiguration` 
`k edit ValidationWebhookConfiguration` edit and update validation webhook `failurepolicy:ignore`

## Updating an Ingress

`kubectl describe ingress test`
`kubectl edit ingress test`

  