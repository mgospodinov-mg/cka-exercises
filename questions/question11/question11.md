## Question

Scale the deployment presentation to 6 pods.

## Prerequisites

Create a deployment "presentation"

```
kubectl create deployment presentation --image=nginx
```

<details>
<summary> Solution</summary>

```
kubectl get deployments
kubectl scale deployment/presentation --replicas=6
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete deployment presentation
```
</details>

