## Question

There was a security incident where an intruder was able to access the whole cluster from a single hacked backend Pod. To prevent this create a NetworkPolicy called `np-backend` in Namespace `project-snake`. It should allow the `backend-*` Pods only to:
- Connect to db1-* Pods on port 1111
- Connect to db2-* Pods on port 2222
Use the app Pod labels in your policy.
- For example, connections from `backend-*` Pods to `vault-*` Pods on port 3333 should no longer work

## Prerequisites

Create namespace `project-snake`

```
kubectl create ns project-snake
```

Create the following pods in the namespace `project-snake` using nginx image. By default the nginx server port is 80.

```
kubectl -n project-snake run backend-0 --labels=app=backend --image=python:3 --port=9999 --command -- python -m http.server 9999
kubectl -n project-snake run db1-0 --labels=app=db1 --image=python:3 --port=1111 --command -- python -m http.server 1111
kubectl -n project-snake run db2-0 --labels=app=db2 --image=python:3 --port=2222 --command -- python -m http.server 2222
kubectl -n project-snake run vault-0 --labels=app=vault --image=python:3 --port=3333 --command -- python -m http.server 3333
```

Check that the pods are created in the `project-snake` namespace

```
kubectl -n project-snake get pods -o wide
```

Check the conection from the `backend-0` pod to the `db1-0`, `db2-0` and `vault-0` pod

```
kubectl -n project-snake exec backend-0 -- curl <podip_db1-0>:1111
kubectl -n project-snake exec backend-0 -- curl <podip_db2-0>:2222
kubectl -n project-snake exec backend-0 -- curl <podip_vault-0>:3333
```

<details>
<summary> Solution</summary>

Create a network policy `np-backend`. You may check a network policy manifest from the kubernetes.io and the modify it.

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np-backend
  namespace: project-snake
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: db1
    ports:
    - protocol: TCP
      port: 1111
  - to:
    - podSelector:
        matchLabels:
          app: db2
    ports:
    - protocol: TCP
      port: 2222
```
</details>

```
kubectl -n project-snake get networkpolicies.networking.k8s.io
```

Test the connection from the backend pod to the other pods:

```
kubectl -n project-snake exec backend-0 -- curl <podip_db1-0>:1111
kubectl -n project-snake exec backend-0 -- curl <podip_db2-0>:2222
kubectl -n project-snake exec backend-0 -- curl <podip_vault-0>:3333
```

</details>

<details>
<summary> CleanUp</summary>

```
kubectl -n project-snake delete networkpolicies np-backend
kubectl -n project-snake delete pod backend-0 db1-0 db2-0 vault-0
kubectl delete ns project-snake
```

</details>