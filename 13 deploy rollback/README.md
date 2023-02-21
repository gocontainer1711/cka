
## Deployment
- creates an application rollout
- deployment creates replicaset in background


## Replica Set
- created automatically in backround
- ensures desired no of replicas are running at any given time
- replica set manages pods
- we don't interact with replica set and pods directly
- multiple replica sets of an app exists in a cluster, only one is in action at a time
- `k get rs`

# Deployment strategies (Old replicaset remains inside cluster)
- Recreate strategy (all existing pods are killed before new ones start) - App downtime !
- Rolling update (pods are updated in rolling update fashion) - Default strategy - no Downtime (specify how many or percent of pods to update at once)
    - **maxUnavailable** (max allowed deletion)
    - **maxSurge** (extra pods above desired capacity)\
    - a new **Deployment revision** is created when Deployment manifest changes / Pod template is updated
    - `k rollout history deployment nginx`
    - to rollback to prev revision `k rollout undo deployment nginx` `k rollout status deployment nginx`


  
###  Where is update strategy configured ? How can we update strategy ?
`k get all`
`k describe deployment nginx`
`kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1`
`kubectl scale deployment/nginx-deployment --replicas=10`


