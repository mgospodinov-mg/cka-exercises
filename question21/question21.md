## Question

- The metrics-server has been installed in the cluster. Write two bash scripts which use kubectl:
- Script node.sh should show resource usage of Nodes
- Script pod.sh should show resource usage of Pods and their containers

## Prerequisites

Check if Kubernetes metrics server is installed

<details>
<summary> Solution</summary>

node.sh

```
!#/bin/bash

kubectl top node
```

pod.sh

```
!#/bin/bash

kubectl top pods --containers=true
```
</details>



