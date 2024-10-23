# Question 12

Use Namespace `project-tiger` for the following. Create a Deployment named `deploy-important` with label `id=very-important` (the `Pods` should also have this label) and 3 replicas. It should contain a container with image `nginx:1.17.6-alpine`.

There should be only ever on Pod of that Deployment running on one worker node. We have two nodes: `kind-control-plane` and `kind-worker`. Because the Deployment has three replicas the result should be that on both nodes `one` Pod is running. The third Pod won't be scheduled, unless a new worker node will be added.

In a way we kind of simulate the behavior of a DaemonSet here, but using a Deployment and a fixed number a replicas.

---
