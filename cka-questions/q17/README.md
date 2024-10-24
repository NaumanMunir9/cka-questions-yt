# Question 17

In *Namespace* `project-tiger`, create a *Pod* named `tigers-reunite` of image `httpd:2.4.41-alpine` with labels `pod=container` and `container=pod`. Find out on which node the Pod is scheduled. SSH into that node and find the containerd container belonging to that *Pod*.

Using command `crictl`:

1. Write the ID of the container and the `info.runtimeType` into `/tmp/opt/course/17/pod-container.txt`.
2. Write the logs of the container into `/tmp/opt/course/17/pod-container.log`.

---
