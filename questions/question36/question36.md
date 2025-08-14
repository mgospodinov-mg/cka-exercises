## Question

- You are supposed to implement cluster-level logging with a sidecar container. Create a multi-container Pod named `multi`. The main application container named `nginx` should use the image `nginx:1.21.6`. 
  The sidecar container named streaming uses the image `busybox:1.35.0` and the arguments `/bin/sh`, `-c` and `tail -n+1 -f /var/log/nginx/access.log`.
- Define a volume of type `emptyDir` for the Pod and mount it to the path `/var/log/nginx` for both containers.
- Access the endpoint of the nginx service a couple of times using a wget or curl command. Inspect the logs of the sidecar container. 
- Create two Pods named `stress-1` and `stress-2`. Define a container that uses the image `polinux/stress:1.0.4` with the command `stress` and the arguments `/bin/sh -c` and 
  `stress --vm 1 --vm-bytes $(shuf -i 20-200 -n1)M --vm-hang 1`. Set the container memory resource limits and requests to 250Mi.
- Use the data available through the metrics server to identify which of the Pods, `stress-1` and `stress-2`, consumes the most memory. Write the name of the Pod to the file `max-memory.txt`.

<details>
<summary> Soluton</summary>

```
 kubectl run multi --image=nginx:1.21.6 --restart=Never --dry-run=client -o yaml > multi.yaml
```

 Edit the multi.yaml file
 ```
 apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: multi
  name: multi
spec:
  containers:
  - image: nginx:1.21.6
    name: nginx
  - image: busybox:1.35.0
    name: streaming
    command: ['bin/sh', '-c', 'tail -n+1 -f /var/log/nginx/access.log']
 ```

 ```
 kubectl apply -f multi.yaml
 ```
 Edit the multi.yaml file
 ```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: multi
  name: multi
spec:
  containers:
  - image: nginx:1.21.6
    name: nginx
    volumeMounts:
    - name: emptydir-volume
      mountPath: /var/log/nginx
  - image: busybox:1.35.0
    name: streaming
    command: ['bin/sh', '-c', 'tail -n+1 -f /var/log/nginx/access.log']
    volumeMounts:
    - name: emptydir-volume
      mountPath: /var/log/nginx
  volumes:
  - name: emptydir-volume
    emptyDir: {}
```
```
kubctl apply -f multi.yaml
```      
```
kubectl run tmp --image=busybox --restart=Never -it --rm wget 172.17.1.2   # This is the POD IP
```
```
kubectl logs multi -c streaming
172.17.1.3 - - [14/Aug/2025:06:51:44 +0000] "GET / HTTP/1.1" 200 615 "-" "Wget" "-"
```
```
kubectl run stress-1 --image=polinux/stress:1.0.4 --restart=Never --dry-run=client > stress-1.yaml
```
Edit the `stress-1.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: stress-1
  name: stress-1
spec:
  containers:
  - image: polinux/stress:1.0.4
    name: stress-1
    command: ['/bin/sh', '-c','stress --vm 1 --vm-bytes $(shuf -i 20-200 -n1)M --vm-hang 1']
    resources:
      requests:
        memory: 250Mi
      limits:
        memory: 250Mi
  restartPolicy: Never
```
Create 'stress-2.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: stress-2
  name: stress-2
spec:
  containers:
  - image: polinux/stress:1.0.4
    name: stress-1
    command: ['/bin/sh', '-c','stress --vm 1 --vm-bytes $(shuf -i 20-200 -n1)M --vm-hang 1']
    resources:
      requests:
        memory: 250Mi
      limits:
        memory: 250Mi
  restartPolicy: Never
```
```
kubectl apply -f stress-2.yaml
```
```
kubectl top pods
```
 </details>