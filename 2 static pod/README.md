# static pods
- Master node pods are called static pods
- save manifest files i here `/etc/kubernetes/manifests/manifest.yaml`
- kubelet watches `/etc/kubernetes/manifests` for changes
- kubelet restarts static pods automatically
- scheduler decides which node, kubelest schedules pods in actual


# Why static pods ?
- kubelet not control manager watches sttaic pods and restarts if it fails
- pod names are suffixed with node host names