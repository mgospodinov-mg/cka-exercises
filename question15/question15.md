## Question

You have access to multiple clusters from your main terminal through kubectl contexts. Write all those context names into `/opt/course/1/contexts`.
Next write a command to display the current context into `/opt/course/1/context_default_kubectl.sh`, the command should use kubectl.
Finally write a second command doing the same thing into `/opt/course/1/context_default_no_kubectl.sh`, but without the use of kubectl.

<details>
<summary> Solution</summary>
In this example, we only have access to a single Kubernetes cluster, but the solution remains valid even with multiple clusters.

```
kubectl config get-context -o name > /opt/course/1/contexts
```
In the file `/opt/course/1/context_default_kubectl.sh` add the following command

```
kubectl get current-context
```
In the file `/opt/course/1/context_default_no_kubectl.sh` add the following command

```
cat ~/.kube/config | grep current | sed -e "s/current-context: //"
```
</details>

