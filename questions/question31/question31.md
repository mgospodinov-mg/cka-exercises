## Question

- Create the ServiceAccount named `api-access` in a new namespace called `apps`.
- Create a ClusterRole with the name `api-clusterrole`, and create a ClusterRoleBinding named `api-clusterrolebinding`. Map the ServiceAccount from the
  previous step to the API resources pods with the operations `watch`, `list`, and `get`.
- Create a Pod named `operator` with the image `nginx:1.21.1` in the namespace `apps`. Expose the container port 80. Assign the ServiceAccount `api-access` to the
  Pod. Create another Pod named `disposable` with the image `nginx:1.21.1` in the namespace `rm`. Do not assign the ServiceAccount to the Pod.
- Open an interactive shell to the Pod named `operator`. Use the command-line tool curl to make an API call to list the Pods in the namespace `rm`. What response do you expect? Use the command-line tool curl to 
  make an API call to delete the Pod disposable in the namespace `rm`.

<details>
<summary> Solution</summary>

```
kubectl create ns apps
```

```
kubectl -n apps create sa api-access
```

```
kubectl create clusterrole api-clusterrole --resource=pod --verb=get,watch,list
```

```
kubectl create clusterrolebinding api-clusterrolebinding --clusterrole=api-clusterrole --serviceaccount=apps:api-access
```

Create a `dummy` pod and then we will modify the yaml file

```
kubectl run operator --image=nginx:1.21.1 --port=80 --dry-run=client > operator-pod.yaml
```

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: operator
  name: operator
  namespace: apps
spec:
  serviceAccountName: api-access
  containers:
  - image: nginx:1.21.1
    name: operator
    ports:
    - containerPort: 80
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

```
kubectl create ns rm
```

Modify the above yaml file to create the `disposable` pod

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: disposable
  name: disposable
  namespace: rm
spec:
  containers:
  - image: nginx:1.21.1
    name: disposable
    ports:
    - containerPort: 80
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

```
kubectl -n apps exec -it operator -- bash
```

```
curl -k --header "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://kubernetes.default.svc/api/v1/namespaces/rm/pods
```

```
curl -k -X DELETE --header "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://kubernetes.default.svc/api/v1/namespaces/rm/pods
```
</details>