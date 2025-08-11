## Question

The CoreDNS configuration in the cluster needs to be updated:
- Make a backup of the existing configuration Yaml and store it at `~/coredns_backup.yaml`. You should be able to fast recover from the backup.
- Update the CoreDNS configuration in the cluster so that DNS resolution for `SERVICE.NAMESPACE.custom-domain` will work exactly like and in addition to `SERVICE.NAMESPACE.cluster.local`
- Test your configuration for example from a Pod with `busybox:1` image. These commands should result in an IP address:
  nslookup kubernetes.default.svc.cluster.local 
  nslookup kubernetes.default.svc.custom-domain

<details>
<summary> Solution</summary>

Check how the CoreDNS is deployed on the cluster

```
kubectl get pods, deployments -n kube-system
```

The CoreDNS configuration is in a configmap

```
kubectl get cm -n kube-system
```

BackUp the CoreDNS configuration

```
kubectl -n kube-system get cm coredns -o yaml > coredns_backup.yaml
```
Edit the configmap

```
kubectl -n kube-system edit cm coredns
```

In the following section

```
kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
```

add the `custom-domain`

```
kubernetes custom-domain cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
```

Restart the CoreDNS

```
kubectl -n kube-system rollout restart deployment coredns
```

Run a Pod with image `busybox:1` and test

```
kubectl run bb --image=busybox:1 -- sh -c "sleep 3600s"
```

```
kubectl exec -it bb -- sh
```

```
nslookup kubernetes.default.svc.cluster.local 
nslookup kubernetes.default.svc.custom-domain
```

</details>