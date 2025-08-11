## Question
Create a namespace called "development" and pod with image nginx called nginx on this namespace

<details>
<summary> Solution</summary>

```
kubectl create ns development
kubectl run nginx --image=nginx --restart=Never" -n development
```
</details>

<details>
<summary> Clean up</summary>

```
kubectl delete pod nginx -n development
```
</details>