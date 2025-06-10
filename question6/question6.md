## Question

Schedule a pod as follows:
- Name nginx-kusc00401
- Image: nginx
- Node selector: disk=ssd

## Prerequisites

Before proceeding, ensure you have met the following prerequisites.

Label one of the kubernetes nodes. 
 
```
kubectl label node <node name> disk=ssd
```
<details>
<summary> Solution</summary>

```
kubectl run nginx-kusc00401 --image=nginx --dry-run=client > nginx-kusc00401.yaml
```

Add nodeSelector in nginx-kusc00401.yaml

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-kusc00401
  name: nginx-kusc00401
spec:
  containers:
  - image: nginx
    name: nginx-kusc00401
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  nodeSelector:
    disk: ssd
status: {}
```

```
kuebctl create -f nginx-kusc00401.yaml
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod nginx-kusc0041
kubectl label node <node name> disk-
```
</details>

