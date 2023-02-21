`k create --help`
`k run mypod --image=nginx`
`k create deployment nginx-d --image=nginx --replicas=2`
`k expose deployment nginx-d --type=NodePort --name=nginx-service`
`export dry="--dry-run=client -o yaml"`
`k create service clusterip myservice --tcp=80:80 $dry > manifest.yaml`
`k scale --replicas=3 deployment/mysql`

`k top pod --help`
`k top pod podname --containers` # display resource usage pods or nodes



# While Editing Existing Pods
- Can't add or remove contaibers
- Can't add volumes

# while creating service 
- can't specify node port when creating NodePort Service


# root user
`sudo -i`

# switch nodes
`ssh -i ~/.ssh/id_rsa ubuntu@158.10.19.1`
`ssh -i ~/.ssh/id_rsa root@158.10.19.1`

# Temp files
`k apply -f temp/tempfile.yaml`

