## Question

Create a pod with image nginx called nginx and allow traffic on port 80

<details>
<summary>Solution</summary>

```
kubectl run nginx --image=nginx --restart=Never --port=80
```
</details>

<details>
<summary> Clean up</summary>

```
kubectl delete pod nginx
```
</details>