Specifiying Kubeconfig files for each use case:
`kubectl --kubeconfig ./test-config/conf.yaml`

# We can add multiple contexts in a single config file
- Define all cluster contexts in single kubeconfig file
- One context for each cluster
- switch between these contexts (current context)


## What is a context :
- context is a combination of user and cluster
- it has a name (username@clustername)
- We can optionally add namespace to a context (default is the default namepsace of a context)
- we can update current context


Imperative commands

`kubectl config --help`
`kubectl config view`
`kubectl config get-contexts`
`kubectl config current-context`
`kubectl config use-context <name-of-context>`
