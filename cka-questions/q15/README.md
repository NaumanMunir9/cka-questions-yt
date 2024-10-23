# Question 15

- *Task weight: 3%*

Use context: `kubectl config use-context k8s-c1-H`

Write a command into `/opt/course/15/cluster_events.sh` which shows the latest events in the whole cluster, ordered by time (`metadata.creationTimestamp`). Use `kubectl` for it.

Now kill the kube-proxy Pod running on node cluster2-worker1 and write the events this caused into `/opt/course/15/pod_kill.log`.

Finally kill the containerd container of the kube-proxy Pod on node cluster2-worker1 and write the event into `/opt/course/15/container_kill.log`.

Do you notice differences in the events both actions caused?

---
