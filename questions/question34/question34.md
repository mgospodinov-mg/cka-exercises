## Question

- In the namespace `external`, create a Deployment named `nginx` with the image `nginx` for three replicas. The container should expose 
  the port 80. Within the same namespace, create a Service of type `LoadBalancer`. The Service should route traffic to the Pods managed by the Deployment.
- From your local machine (outside the cluster), make a call to the LoadBalancer using wget or curl. Identify which Pods received the traffic 
  by looking at the logs.
- Change the Service type to `ClusterIP`. Make a call to the Service using wget or curl so that the Pods receive the traffic.
- Create an `Ingress` named `incoming` in the namespace `external`. Define the path type `Prefix` to the path / to the Service from the 
  previous step. The Ingress should be able to handle any incoming HTTP traffic.
- Make a call to the Ingress using wget or curl from your local machine. Verify that the Pods receive traffic.
- Create a new Service of type ClusterIP named echoserver in the namespace external. The selected and to-be-created Pod should use the image 
  `k8s.gcr.io/echoserver:1.10` on port 8080. Add a new rule to the existing Ingress to route traffic to the echoserver Service with the path /echo and type Exact.
- Make a call to the Service using wget or curl from your local machine so that the echoserver can be reached. 

<details>
<summary> Solution</summary>

```
kubectl create ns external
```
```
kubectl -n external create deployment nginx --image=nginx --replicas=3 --port=80
```
```
kubectl -n external create service loadbalancer nginx --tcp=80:80
```
Check the pod logs
```
kubectl -n external logs <nginx_pod>
```
```
kubectl -n external edit svc nginx
```
Change the type to "type: ClusterIP"
```
kubectl run busybox --image=busybox --restart=Never -it --rm -- wget 10.102.149.125 #This is the ClusterIP
```
Create an Ingress (use the kubernetes documentation)
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: incoming
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80
```
```
kubectl -n external apply -f ingress.yaml
```
```
kubectl -n external run echoserver --image=k8s.gcr.io/echoserver:1.10 --restart=Never --port=8080 --expose
```
Modify the ingress adding additional rule
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: incoming
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80
      - path: /echo
        pathType: Exact
        backend:
          service:
            name: echoserver
            port:
              number: 8080
```
</details>