## Question

Implement the following in namespace `project-tiger`:
- Create a Deployment named `deploy-important` with 3 replicas
- The Deployment and its Pods should have label `id=very-important`
- First container named container1 with image `nginx:1-alpine`
- Second container named container2 with image `google/pause`
There should only ever be one Pod of that Deployment running on one worker node, use `topologyKey: kubernetes.io/hostname` for this.

## Prerequisites
Create namespace `project-tiger`

```
kubectl create ns project-tiger
```

<details>
<summary> Solution</summary>
Generate a deployment yaml file , which later we will modify

```
kubectl create deployment deploy-important --image=nginx:1-alpine --dry-run=client -o yaml > deployment.yaml
```
Modify the deployment.yaml file like below:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    id: very-important
  name: deploy-important
spec:
  replicas: 3
  selector:
    matchLabels:
      id: very-important
  template:
    metadata:
      labels:
        id: very-important
    spec:
      containers:
      - image: nginx:1-alpine
        name: container1
      - image: google/pause
        name: container2
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: id
                operator: In
                values:
                - very-important
            topologyKey: kubernetes.io/hostname
```

```
kubectl create -f deployment.yaml -n project-tiger
```

```
kubectl get pods -n project-tiger -o wide
```

</details>

<details>
<summary> CleanUp</summary>

```
kubectl -n project-tiger delete deployment deploy-important
kubectl delete ns project-tiger
```

</details>
