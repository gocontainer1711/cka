Also Refer to section 5
https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/
## who generated certificates ?
kubeadm

`kubeadm --help`
`kubeadm certs --help`

## when does cluster certificates expire ? when does CA expire ?

`sudo kubeadm certs check-expiration`
`kubectl -n kube-system get cm kubeadm-config -o yaml`


`openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout`

`openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout | grep Validity -A2`


## Renew Certs if needed ?

- should upgrade regularly
- no need to manually do it , Kubeadm will take care of it

`sudo kubeadm certs renew --help`
`sudo kubeadm certs renew apiserver`