## Question
Create and configure the service "front-end-service" so it's accessible through NodePort and routes the existing pod named "front-end".

## Prerequisites

Create a pod named "front-end" that utilizes port 80

```
kubectl run front-end --image=nginx --restart=Never --port=80
````

<details>
<summary> Solution</summary>
1. Check which port uses the container "front-end"

```
kubectl get pod front-end -o yaml
```
2. Create the service "front-end-service"

```
kubectl expose pod front-end --port=80 --target-port=80 --type=NodePort --name=front-end-service
```

--port=80 exposes the service on port 80 <br>
--target-port=80 forwards traffic to port 80 in the pod <br>
--type NodePort makes the service accessible externally on a port from the NodePort range

</details>

<details>
<summary> Clean up</summary>

```
kubectl delete svc front-end-serivce
kubectl delete pod front-end
```
</details>