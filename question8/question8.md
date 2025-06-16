## Question

Create a pod named kucc8 with a single app container for each of the following images running inside (there may be between 1 and 4 images specified):
nginx + redis + memcached.

<details>
<summary>Solution</summary>

```
kubectl run kucc8 --image=nginx --dry-run=client -o yaml > pod.yaml
```
Modify the `pod.yaml` file to include redis and memcached.

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: kucc8
  name: kucc8
spec:
  containers:
  - image: nginx
    name: nginx
  - image: redis
    name: redis
  - image: memcached
    name: memcached
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Create the pod using the pod.yaml
```
kubectl create -f pod.yaml
```

</details>

<details>
<summary>Clean Up</summary>

```
kubectl delete pod kucc8
```
</details>
