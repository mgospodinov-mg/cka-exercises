## Question

Get IP address of the pod - "nginx-dev"

## Prerequisites

Create a pod nginx-dev

```
kubectl run nginx-dev --image=nginx --restart=Never
```

<details>
<summary> Solution</summary>

```
kubectl get pod nginx-dev -o jsonpath '{.metadata.name}{"\t}{.status.PodIP}'
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod nginx-dev
```
</details>