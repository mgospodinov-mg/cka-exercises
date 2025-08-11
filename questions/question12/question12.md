## Question

List the nginx pod with custom columns POD_NAME and POD_STATUS

## Prerequisites

Create 3 nginx pods

```
kubectl run nginx1 --image=nginx --restart=Never
kubectl run nginx2 --image=nginx --restart=Never
kubectl run nginx3 --image=nginx --restart=Never
```

<details>
<summary> Solution</summary>

```
kubectl get pods -o=custom-columns='POD_NAME:.metadata.name,POD_STATUS:.status.phase'
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod ngnix1 nginx2 ngnix3
```
</details>