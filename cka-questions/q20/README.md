# Question 20

Create a `Static Pod` named `my-static-pod` in Namespace `default` on `controlplane`. It should be of image `nginx:1.16-alpine` and have resource request for `10m` CPU and `20Mi` memory.

Then create a NodePort *Service* named `static-pod-service` which exposes that static *Pod* on port 80 and check if it has *Endpoints* and if its reachable through the `controlplane` internal IP address. You can connect to the internal node IPs from your main terminal.

---
