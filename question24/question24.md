## Question

- Use Namespace `project-tiger` for the following. Create a DaemonSet named `ds-important` with image httpd:2-alpine and labels id=ds-important and uuid=18426a0b-5f59-4e10-923f-c0e078e82462.
- The Pods it creates should request 10 millicore cpu and 10 mebibyte memory.
- The Pods of that DaemonSet should run on all nodes, also controlplanes.

## Prerequisites

Create namespace `project-tiger`

```
kubectl create ns project-tiger
```

<details>
<summary> Solution</summary>

Create a Deployment and later modify it based on the requirements.

```
kubectl create deployment ds-important --image=httpd:2-alpine --dry-run=client -o yaml > daemonset.yaml
```

Edit the `daemonset.yaml` file

```
apiVersion: apps/v1
kind: DaemonSet # change to Daemonset
metadata:
  labels:
    id: ds-important
    uuid: 18426a0b-5f59-4e10-923f-c0e078e82462
  name: ds-important
  namespace: project-tiger
spec:
  selector:
    matchLabels:
      id: ds-important
      uuid: 18426a0b-5f59-4e10-923f-c0e078e82462
  template:
    metadata:
      labels:
        id: ds-important
        uuid: 18426a0b-5f59-4e10-923f-c0e078e82462
    spec:
      containers:
      - image: httpd:2-alpine
        name: httpd
        resources: 
          requests:
            cpu: 10m
            memory: 10Mi
      tolerations:
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
```

```
kubectl create -f daemonset.yaml
```
</details>

<details>
<summary> CleanUp</summary>

```
kubectl -n project-tiger delete ds ds-important
kubectl delete ns project-tiger
```
</details>

