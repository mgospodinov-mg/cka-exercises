## Question

Perform the following tasks:
- Add an init container to hungry-bear (which has been defined in spec file `/opt/KUCC00108/pod-spec-KUCC00108.yaml`)
- The init container should create an empty file named `/workdir/calm.txt`.
- If `/workdir/calm.txt` is not detected, the pod should exit.
- Once the spec file has been updated with the init container definition, the pod should be created.

## Prerequisites

Create the `/opt/KUCC00108/pod-spec-KUCC00108.yaml` file using the below yaml file

```
apiVersion: v1
kind: Pod
metadata:
  name: hungry-bear
spec:
  containers:
   - name: checker
     image: alpine
     command: ["/bin/sh", "-c", "if [ -f /workdir/calm.txt ];
               then sleep 100000; else exit 1; fi" ]
     volumeMounts:
       - name: workdir
         mountPath: /workdir
  volumes:
    - name: workdir
      emptyDir:
```

<details>
<summary> Solution</summary>

Open the `/opt/KUCC00108/pod-spec-KUCC00108.yaml` file and add the init container

```
apiVersion: v1
kind: Pod
metadata:
  name: hungry-bear
spec:
  containers:
   - name: checker
     image: alpine
     command: ["/bin/sh", "-c", "if [ -f /workdir/calm.txt ];
               then sleep 100000; else exit 1; fi" ]
     volumeMounts:
       - name: workdir
         mountPath: /workdir
   - name: create
     image: alpine
     command: ["/bin/sh", "-c", "touch /workdir/calm.txt"]
     volumeMounts:
       - name: workdir
         mountPath: /workdir
  volumes:
    - name: workdir
      emptyDir:

```

```
kubectl create -f /opt/KUCC00108/pod-spec-KUCC00108.yaml
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete pod hungry-bear
```
</details>
