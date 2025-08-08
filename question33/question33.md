## Question

-  Write a manifest for a new Pod named `ingress-controller` with a single container that uses the image `bitnami/nginx-ingress-controller:1.0.0`. 
   For  the container, set the resource request to 256Mi for memory and 1 CPU. Set the resource limits to 1024Mi for memory and 2.5 CPU.
-  Using the manifest, schedule the Pod on a cluster with 2 nodes. Once created, identify the node that runs the Pod. Write the node name 
   to the file node.txt.
-  Create the directory named manifests. Within the directory, create two files:
  `pod.yaml` and `configmap.yaml`. The `pod.yaml` file should define a Pod named nginx with the image nginx:1.21.1. 
   The `configmap.yaml` file defines a Config‐Map named logs-config with the key-value pair `dir=/etc/logs/traffic.log`.
   Create both objects with a single, declarative command.
-  Modify the ConfigMap manifest by changing the value of the key dir to `/etc/logs/traffic-log.txt`. Apply the changes. 
   Delete both objects with a single declarative command.
-  Use Kustomize to set a common namespace `t012` for the resource file `pod.yaml`. The file `pod.yaml` defines the Pod named nginx with 
   the image nginx:1.21.1 without a namespace. Run the Kustomize command that renders the transformed manifest on the console.

<details>
<summary> Solution</summary>

You may use the kubernetes documentation to write the manifest.

pod-ingress-controller.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: ingress-controller
spec:
  containers:
  - name: ingress-controller
    image: bitnami/nginx-ingress-controller:1.0.0
    resources:
      limits:
        cpu: "2.5"
        memory: "1024Mi"
      requests:
        cpu: "1"
        memory: "256Mi"
```

```
kubectl apply -f pod-ingress-controller.yaml
```

Check on which node the pod is scheduled

```
kubectl get pods -o wide
```

```
mkdir manifests
cd manifests
```

pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.21.1
```
configmap.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: logs-config
data:
  dir: "/etc/logs/traffic.log"
```
```
kubectl apply -f manifests/ -R
```
```
kubectl edit cm logs-config
```
Delete the both objects
```
kubectl delete -f manifests -R
```
Create kustomization.yaml file
```
namespace: t012
resources:
- pod.yaml
```
```
kubectl kustomize ./
```
```
kubectl kustomize ./
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: t012
spec:
  containers:
  - image: nginx:1.21.1
    name: nginx
```
</details>