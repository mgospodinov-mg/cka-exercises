## Question

- Create a new PersistentVolume named `safari-pv`. It should have a capacity of 2Gi, accessMode ReadWriteOnce, hostPath /Volumes/Data and no storageClassName defined.
- Next create a new PersistentVolumeClaim in namespace `project-t230` named `safari-pvc` . It should request 2Gi storage, accessMode ReadWriteOnce and should not define a storageClassName. The PVC should bound to the PV correctly.
- Finally create a new Deployment safari in namespace `project-t230` which mounts that volume at /tmp/safari-data. The Pods of that Deployment should be of image httpd:2-alpine.

## Prerequsites

Create namespace `project-t230`

```
kubectl create ns project-t230
```

<details>
<summary> Solution</summary>
During the exam, you are allowed to refer to the Kubernetes documentation. Locate an example of a PersistentVolume in the documentation and modify it.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: safari-pv
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/Volumes/Data"
```

Locate an example of a PersistentVolumeClaim in the documentation and modify it

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: safari-pvc
  namespace: project-t230
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

Create a deployment and modify it based on the requirements. 

```
kubectl create deployment safari --image=httpd:2-alpine --dry-run=client -o yaml > deployment-safari.yaml 
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: safari
  name: safari
  namespace: project-t230
spec:
  replicas: 1
  selector:
    matchLabels:
      app: safari
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: safari
    spec:
      containers:
      - image: httpd:2-alpine
        name: httpd
        resources: {}
status: {}
```

Modified version

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: safari
  name: safari
  namespace: project-t230
spec:
  replicas: 1
  selector:
    matchLabels:
      app: safari
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: safari
    spec:
      containers:
      - image: httpd:2-alpine
        name: httpd
        resources: {}
        volumeMounts:
        - mountPath: /tmp/safari-data
          name: data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: safari-pvc
```  
</details>

<details>
<summary> Clean Up</summary>

```
kubectl -n project-t230 delete deployment safari
kubectl -n project-t230 delete pvc safari-pvc
kubectl delete pv safari-pv
```