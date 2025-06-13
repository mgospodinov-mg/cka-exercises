## Question

Create a deployment as follows:
- Name: nginx-random
- Exposed via a service nginx-random
- Ensure that the service & pod are accessible via their respective DNS records
- The container(s) within any pod(s) running as a part of this deployment should use the nginx image<br>
Next, use the utility nslookup to look up the DNS records of the service & pod and write the output to /opt/KUNW00601/service.dns and /opt/KUNW00601/pod.dns
respectively.

<details>
<summary> Solution</summary>

```
kubectl create deployment nginx-random --image=nginx
kubectl expose deployment nginx-random --port=80 --target-port=80 --name=nginx-random
```
Run the busybox container, which contains many common UNIX utilities

```
kubectl run busybox --restart=Never --image=busybox -- sleep 3600
```
Get the POD IP address
```
kubectl get pod nginx-random-686cf4f66-qkjnq -o=jsonpath='{.status.podIP}'
```
Find the DNS record using the nslookup command
```
kubectl exec -it busybox -- nslookup 192.168.0.114 > /opt/KUNW00601/pod.dns
```
Get the service nginx-random ClusterIP
```
kubectl get svc nginx-random -o=jsonpath='{.spec.clusterIP}'
```
Find the DNS reconds using the nslookup command
```
kubectl exec -it busybox -- nslookup 10.96.51.107 > /opt/KUNW00601/service.dns
```
</details>

<details>
<summary> Clean Up</summary>

```
kubectl delete svc nginx-random
kubectl delete deployment nginx-random
```
</details>