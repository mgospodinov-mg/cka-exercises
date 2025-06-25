## Question

Scale down StatefulSet

There are two Pods named o3db-* in Namespace project-h800. The Project H800 management asked you to scale these down to one replica to save resources.

## Prerequisites

Create a namespace `project-h800`

```
kubectl create ns project-h800
```

Create statefulset using the following manifest. 

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: o3db-statefulset
spec:
  serviceName: "o3db"
  replicas: 3
  selector:
    matchLabels:
      app: o3db
  template:
    metadata:
      labels:
        app: o3db
    spec:
      containers:
      - name: o3db-container
        image: busybox
        command: ["/bin/sh", "-c", "sleep 3600"]
```

```
kubectl -n project-h800 apply -f sts.yaml
```

<details>
<summary> Solution </details>

```
kubectl -n project-h800 get sts, ds, deployment
```

```
kubectl -n project-h800 scale sts o3db-statefulset --replicas 1
```

Check number of pods

```
kubectl -n project-h800 get pods
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl -n project-h800 delete sts o3db-statefulset
```

```
kubectl delete ns project-h800
```

</details>

