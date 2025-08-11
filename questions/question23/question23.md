## Question

- Create a new ServiceAccount processor in Namespace project-hamster.
- Create a Role and RoleBinding, both named processor as well.
- These should allow the new SA to only create Secrets and ConfigMaps in that Namespace.

## Prerequisites

```
kubectl create ns project-hamster
```

<details>
<summary> Solution</summary>

```
kubectl -n project-hamster create sa processor
```

```
kubectl create role processor --verb=create --resource=secret --resource=configmap
```

```
kubectl create rolebinding processor --role=processor --serviceaccount=project-hamster:processor
```

Test that everything works

```
kubectl auth can-i create secret --as=system:serviceaccount:project-hamster:processor
kubectl auth can-i create configmap --as=system:serviceaccount:project-hamster:processor
```
</details>

<details>
<summary> CleanUp</summary>

```
kubectl delete rolebinding processor
kubectl delete role processor
kubectl -n project-hamster delete sa processor
kubectl delete ns project-hamster
```