## Question

- Create a Deployment named `nginx` that uses the image nginx:1.17.0. Set two replicas to begin with. 
- Scale the Deployment to seven replicas using the scale command. Ensure that the correct number of Pods exist.
- Create a Horizontal Pod Autoscaler named `nginx-hpa` for the Deployment with an average utilization of CPU to 65% and an average utilization of memory to 1Gi. Set the minimum number of replicas to 3 and the maximum
  number of replicas to 20.
- Update the pod template of the Deployment to use the image nginx:1.21.1. Make sure that the changes are recorded. Inspect the revision hisotry. How many revisions should be rendered ? Roll back to the first revision.
- Create a new Secret names `basic-auth` of type kubernetes.io/basic-auth. Assign the key-value pairs username=super, and password=my-s8cr3t. Mount the Secret as a volume with the path `/etc/secret` and read-only
  permissions to the Pods controlled by the Deployment. 

<details>
<summary> Solution</summary>

```
kubectl create deployment nginx --image=nginx:1.17.0 --replicas=2
```

```
kubectl scale deployment nginx --replicas=7
```

Create a `dummy` hpa k8s manifest, which we have to edit.

```
kubectl autoscale deployment nginx --name=nginx-hpa --min=3 --max=20 --dry-run=client -o yaml > nginx-hpa.yaml
``` 
Modify using the official kubernetes documentation
```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  maxReplicas: 20
  minReplicas: 3
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 65
  - type: Resource
    resource:
      name: memory
      target:
        type: AverageValue
        averageValue: 1Gi
```
```
kubectl apply -f nginx-hpa.yaml
```
Update the image 
```
kubectl set image deployments nginx nginx=nginx:1.21.1 
```
```
kubectl rollout history deployment nginx
```
```
kubectl rollout undo deployment nginx --to-revision=1
```
```
kubectl create secret generic basic-auth --type=kubernetes.io/basic-auth --from-literal=username=super --from-literal=password=my-s8cr3t
```

Edit the deployment and mount the secret as volume

```
kubectl edit deployment nginx
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "5"
  creationTimestamp: "2025-08-07T11:44:35Z"
  generation: 6
  labels:
    app: nginx
  name: nginx
  namespace: default
  resourceVersion: "44109"
  uid: ac62d23d-f2a7-4c22-9300-4acaea7e68aa
spec:
  progressDeadlineSeconds: 600
  replicas: 7
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.17.0
        imagePullPolicy: IfNotPresent
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:              #Add
        - mountPath: /etc/secret   #Add
          name: auth-secret        #Add
          readOnly: true           #Add
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:                     #Add
      - name: auth-secret          #Add
        secret:                    #Add
          defaultMode: 420 
          secretName: basic-auth   #Add
status:
  availableReplicas: 7
  conditions:
  - lastTransitionTime: "2025-08-07T11:44:52Z"
    lastUpdateTime: "2025-08-07T11:44:52Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2025-08-07T11:44:35Z"
    lastUpdateTime: "2025-08-07T12:14:14Z"
    message: ReplicaSet "nginx-64f6978d84" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 6
  readyReplicas: 7
  replicas: 7
  updatedReplicas: 7
```
</details>