# Question 5

- *Task weight: 1%*

Use context: `kubectl config use-context k8s-c1-H`

There are various Pods in all namespaces. Write a command into `/tmp/opt/course/5/find_pods.sh` which lists all Pods sorted by their AGE { `metadata.creationTimestamp` }.

Write a second command into `/tmp/opt/course/5/find_pods_uid.sh` which lists all Pods sorted by field `metadata.uid`.

Use `kubectl` sorting for both commands.

---
