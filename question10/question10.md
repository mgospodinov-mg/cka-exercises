## Question

Create a pod as follows:
- Name: mongo
- Using Image: mongo
- In a new Kubernetes namespace named: my-website

<details>
<summary> Solution</summary>

```
kuebctl create ns my-website
kubectl run mongo --image=mongo --restart=Never -n my-website
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod mongo -n my-website
```
</details>