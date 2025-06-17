## Question

Get list of all pods in all namespaces and write it to file “/opt/pods-list.yaml”

## Prerequisites

Create some pods

```
kubectl run nginx1 --image=nginx --restart=Never
kubectl run nginx2 --image=nginx --restart=Never
kubectl run nginx3 --image=nginx --restart=Never
```

<details>
<summary> Solution</summary>

```
kubectl get pods --all-namespaces > /opt/pods-list.yaml
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod nginx1 nginx2 nginx3
```
</details>
