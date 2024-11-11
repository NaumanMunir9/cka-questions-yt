# Question 35

Add an init container named `init-container` (which has been defined in spec file `/home/master/opt/web-pod.yaml`).

The init container should create an empty file named `/worker/conf.txt`. If `/workdir/conf.txt` is not detected, the pod should exit. Once the spec file has been updated with the init container definition, the pod should be created.

---
