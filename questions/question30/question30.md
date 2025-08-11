## Question
Create a Kubernetes secret as follows:
- name `super-secret`
- password: bob
Create a pod `pod-secrets-via-file` using the `redis` image, which mounts the secret named `super-secret` at `/secret`

<details>
<summary> Solution</summary>

```
kubectl create secret generic super-secret --from-literal=password=bob
```

Create a "dummy" pod, which we will modify

```
kubectl run pod-secrets-via-file --image=redis --dry-run=client -o yaml > pods-secrets-via-file.yaml
```
During the exam you may use the offical kubernetes documentation from https://kuberenets.io
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-secrets-via-file
  name: pod-secrets-via-file
spec:
  volumes:
    - name: secret-volume
      secret:
        secretName: super-secret
  containers:
  - image: redis
    name: pod-secrets-via-file
    volumeMounts:
      - name: secret-volume
        mountPath: "/secret"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```
```
kubectl apply -f pod-secrets-via-file.yaml
```
</details>
