## Question

- Create a PersistentVolume named `logs-pv` that maps to the hostPath `/tmp/logs`.The access mode should be `ReadWriteOnce` and `ReadOnlyMany`. Provision a storage capacity of 2Gi. Assign the reclaim policy `Delete` and an empty string as the storage class. Ensure that the status of the PersistentVolume shows Available.
- Create a PersistentVolumeClaim named `logs-pvc`. The access it uses is `ReadWriteOnce`. Request a capacity of 1Gi. Ensure that the status of the PersistentVolume shows Bound.
- Mount the PersistentVolumeClaim in a Pod running the image nginx at the mount path `/var/log/nginx`.
- Open an interactive shell to the container and create a new file named `mynginx` log in /var/log/nginx. Exit out of the Pod.
- Delete the Pod and PersistentVolumeClaim. What happens to the PersistentVolume?
- List the available storage classes and identify the default storage class. Note the provisioner.
- Create a new storage class named `custom` using the provisioner of the default storage class.
- Create a PersistentVolumeClaim named `custom-pvc`. Request a capacity of 500Mi and declare the access mode `ReadWriteOnce`.
  Assign the storage class name `custom`.
- The PersistentVolume should have been provisioned dynamically. Find out the name and write it to the file named `pv-name.txt`.
- Delete the PersistentVolumeClaim. What happens to the PersistentVolume?

<details>
<summary> Solution</summary>
Use the kubernetes.io documentation to create a PV. 

logs-pv.yaml

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: logs-pv
  labels:
    type: local
spec:
  storageClassName: ""
  persistentVolumeReclaimPolicy: Delete
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
    - ReadWOnlyMany
  hostPath:
    path: "/tmp/logs/"
```

```
kubectl apply -f logs-pv.yaml
```
Check if the status is "Available"

```
kubectl get pv
```
logs-pvc.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: logs-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```
```
kubectl apply -f logs-pvc.yaml
```
Check that the status of PV shows "Bound"
```
kubectl get pvc
```
Create a `dummy` pod

```
kubectl run nginx-pod --image=nginx --restart=Never --dry-run=client -o yaml > nginx-pod.yaml
```
Modify the `nginx-pod.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx-pod
  name: nginx-pod
spec:
  containers:
  - image: nginx
    name: nginx-pod
    volumeMounts:
    - mountPath: "/var/log/nginx"
      name: my-volume
  volumes:
   - name: my-volume
     persistentVolumeClaim:
       claimName: logs-pvc
  dnsPolicy: ClusterFirst
  restartPolicy: Never
```
```
kubectl apply -f nginx-pod.yaml
```
```
kubectl exec -it nginx-pod -- bash
```
```
cd /var/log/nginx
touch mynginx
```
```
kubectl delete pod nginx-pod 
```
```
kubectl delete pvc logs-pvc
```
```
kubectl get storageclasses.storage.k8s.io
```
Create a new storageclass `custom.yaml`
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: custom
provisioner: ebs.csi.aws.com
```
```
kubectl apply -f custom.yaml
```
Create a PVC using the manifest below 
custom-pvc.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: custom-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  storageClassName: "custom"
  resources:
    requests:
      storage: 500Mi
```
```
kubectl apply -f custom-pvc.yaml
```
```
kubectl get pv
```
```
kubectl delete pvc custom-pvc
```
</details>