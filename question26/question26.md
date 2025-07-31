## Question

Perform some tasks on cluster certificates:
Check how long the kube-apiserver server certificate is valid using openssl or cfssl. Write the expiration date into `/opt/expiration`. Run the kubeadm command to list the expiration dates and confirm both methods show the same one. Write the kubeadm command that would renew the kube-apiserver certificate into `/opt/kubeadm-renew-certs.sh`

<details>
<summary> Solution</summary>

```
cd /etc/kubernetes/api
```

Check using openssl
```
openssl x509 -noout -text -in /etc/kubernetes/pki/apiserver.crt | grep Validity -A2 > /opt/expiration
```

Check using the kubeadm

```
kubeadm certs check-expiration | grep apiserver
```

Command to re-new the kube-apiserver certificate

```
kubeadm certs renew apiserver
```

```
#!/bin/bash
kubeadm certs renew apiserver
```
</details>

