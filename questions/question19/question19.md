## Question

Check all available Pods in the namespace project-c13 and find the names of those that would probably be terminated first if the nodes run out of resources (cpu or memory).
Write the Pod names into /opt/course/4/pods-terminated-first.txt.

## Prerequisites

Create namespace `project-c13`

```
kubectl create ns project-c13
```

Create two pods within namespace `project-c13` specifying cpu and memory limits using the manifests

```
apiVersion: v1
kind: Pod
metadata:
  name: pod1
spec:
  containers:
    - name: container1
      image: nginx:latest
      resources:
        limits:
          memory: "128Mi"
          cpu: "250m"
        requests:
          memory: "64Mi"
          cpu: "125m"
```

```
apiVersion: v1
kind: Pod
metadata:
  name: pod2
spec:
  containers:
    - name: container2
      image: nginx:latest
      resources:
        limits:
          memory: "128Mi"
          cpu: "250m"
        requests:
          memory: "64Mi"
          cpu: "125m"
```         

Create two pods within namespace `project-c13` without specifying cpu and memory limits using the manifests

```
apiVersion: v1
kind: Pod
metadata:
  name: pod3
spec:
  containers:
    - name: container3
      image: nginx:latest
```

```
apiVersion: v1
kind: Pod
metadata:
  name: pod4
spec:
  containers:
    - name: container4
      image: nginx:latest
```

<details>
<summary> Solution </summary>

Pods without resource request/limits will be evicted first

```
kubectl get pods -o jsonpath="{range .items[*]}{.metadata.name}{'\t'}[{.spec.containers[*].name},{.spec.containers[*].resources}]{'\n'}{end}"
```

Write `pod3` and `pod4` into the file `/opt/course/4/pods-terminated-first.txt`.
</details>

<details>
<summary> Clean Up </summary>

```
kubectl -n project-c13 delete pod pod1 pod2 pod3 pod4
```

</details>

