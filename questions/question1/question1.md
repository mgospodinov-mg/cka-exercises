## Question 
Schedule a Pod as follows:
- Name: kucc8
- Application containers: 2
- Container Name/Images:
  - nginx
  - consul

<details>
<summary> Solution</summary>

```
kubectl run kucc8 --image=nginx --dry-run=client -o yaml > kucc8-pod.yaml
```
Modify the `kucc8-pod.yaml` file by adding the Consul container, and then save the updated file. The contents of the file will resemble the following:

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
  - image: hashicorp/consul
    name: consul
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```
kubectl create -f kucc8-pod.yaml
```
</details>

<details>
<summary> Clean up</summary>

```
kubectl delete pod kucc8
```
</details>