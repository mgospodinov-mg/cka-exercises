## Question

- There is ServiceAccount secret-reader in Namespace `project-swan`. Create a Pod of image nginx:1-alpine named api-contact which uses this ServiceAccount.
- Exec into the Pod and use curl to manually query all Secrets from the Kubernetes Api.
- Write the result into file /opt/course/9/result.json.

## Prerequisites

Create namespace `project-swan`

```
kubectl create ns project-swan
```

Create a service account `secret-reader` in namespace `project-swan`

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: secret-reader
  namespace: project-swan
```

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader-role
rules:
  - apiGroups: [""]
    resources: ["secrets"]
    verbs: ["get", "list", "watch"]
```

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: secret-reader-binding
  namespace: project-swan
subjects:
  - kind: ServiceAccount
    name: secret-reader
    namespace: project-swan
roleRef:
  kind: ClusterRole
  name: secret-reader-role
  apiGroup: rbac.authorization.k8s.io
```

<details>
<summary> Solution</summary>

```
kubectl -n project-swan run api-contact --image=nginx:1-alpine --restart=Never --dry-run=client -o yaml > api-contact.yaml
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: api-contact
  name: api-contact
  namespace: project-swan
spec:
  serviceAccount: secret-reader #Add
  containers:
  - image: nginx:1-alpine
    name: api-contact
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```
kubectl -n project-swan exec -it api-contact  -- sh
```

```
curl -k https://kubernetes.default/api/v1/secrets
```

Get the token from `/var/run/secrets/kubernetes.io/serviceaccount/token`

```
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)

curl -k https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"
```

Save the json in file `/opt/course/9/result.json`

</details>

<details>
<summary>Clean Up</summary>

```
kubectl delete clusterrolebindings.rbac.authorization.k8s.io secret-reader-binding
kubectl delete clusterrole secret-reader-role
kubectl -n project-swan delete sa secret-reader
kubectl -n project-swan delete pods api-contact
kubectl delete ns project-swan
```