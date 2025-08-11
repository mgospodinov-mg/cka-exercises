## Question

In namespace `project-tiger` create a Pod named `tigers-reunite` of image `httpd:2-alpine` with labels `pod=container` and `container=pod`. Find out on which node the Pod is scheduled. Ssh into that node and find the containerd container belonging to that Pod.
Using command crictl:
- Write the ID of the container and the info.runtimeType into `~/pod-container.txt`
- Write the logs of the container into `~/pod-container.log`

## Prerequisites

Create namespace `project-tiger`

```
kubectl create ns project-tiger
```

## Solution

<details>
<summary>Solution</summary>

```
kubectl -n project-tiger run tiger-reunite --image=httpd:2-alpine --labels=pod=container,container=pod
```

Check where the pod is scheduled

```
kubectl -n project-tiger get pods -o wide
``` 

ssh to the node where the pod is scheduled. Get the list of containers 

```
crictl ps
```

Get the runtimeType
```
crictl inspect --name tiger-reunite | grep runtimeType
```
Write the values in the file `pod-container.txt`

Get the container logs:
```
crictl logs <CONTAINER_ID> > pod-container.log
```
Example:
```
crictl logs e25a856732f8a > pod-container.log
```

</details>