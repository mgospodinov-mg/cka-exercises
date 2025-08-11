## Question

Create a new NetworkPolicy named `allow-port-from-namespace` in the existing namespace `fubar`.
Ensure that the new NetworkPolicy allows Pods in namespace `internal` to connect to port 9000 of Pods in namespace `fubar`.
Further ensure that the new NetworkPolicy:
- does not allow access to Pods, which don't listen on port 9000
- does not allow access from Pods, which are not in namespace internal

## Prerequisites

Create namespaces `fubar` and `internal`

```
kubectl create ns fubar
kubectl create ns internal
```

In the `fubar` namespace create two pods like this

```
kubectl -n fubar run http-server-9000 --image=python:3 --port=9000 --command -- python -m http.server 9000
kubectl -n fubar run http-server-9001 --image=python:3 --port=9001 --command -- python -m http.server 9001
```

Create a BusyBox pod in the internal namespace for testing purposes. 

```
kubectl -n internal run busybox --image=busybox --restart=Never -- sleep 3600
```

<details>
<summary> Solution</summary>

Create the required NetworkPolicy. Remember, during the exam you’re allowed to refer to the Kubernetes documentation. 

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: fubar
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: internal
    ports:
      - protocol: TCP
        port: 9000

```

```
kubectl apply -f network-policy.yaml
```

Time to test the solution

```
kubectl -n internal exec -it busybox -- telnet <POD IP http-server-9000> 9000
```
Must be to able to connect.

```
kubectl -n internal exec -it busybox -- telnet <POD IP http-server-9001> 9001
```

Unable to connect.
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod http-server-9000 -n fubar
kubectl delete pod http-server-9001 -n fubar
kubectl delete pod busybox -n internal
```