## Question

Create an nginx pod and list the pod with different levels of verbosity.

<details>
<summary> Solution</summary>

```
kubectl run nginx --image=nginx --restart=Never
```

List pod with different level of verbosity

```
kubectl get po nginx -v=7
kubectl get po nginx -v=8
kubectl get po nginx -v=9
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod nginx
```
</details>
