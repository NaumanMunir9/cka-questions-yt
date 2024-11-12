# Question 35

In the `default` Namespace, create a Pod named `app-pod` using the `nginx:alpine` image. The Pod should have the following configuration:

1. Add an init container named `init-config` using the `busybox` image. This init container should create an empty file `/init/ready.txt` and ensure that it exists before the main container starts.

2. Configure the main container to check for the presence of `/init/ready.txt` as a mounted volume before starting. If the file is not found, the main container should fail.

3. Mount the `init-config` directory as a volume at `/init` in the main container so it can check for the `ready.txt` file created by the init container.

---

Here are the steps to create the `app-pod` with an init container in your Vagrant Kubeadm Kubernetes cluster:

### Step 1: Create the Pod Manifest

Create a YAML file for the `app-pod` in your preferred directory. Here, we'll save it as `/home/master/opt/app-pod.yaml`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
  namespace: default
spec:
  # Init container configuration
  initContainers:
  - name: init-config
    image: busybox
    command: ["sh", "-c", "touch /init/ready.txt && echo 'Init container completed'"]
    volumeMounts:
    - name: init-volume
      mountPath: /init

  # Main container configuration
  containers:
  - name: nginx-container
    image: nginx:alpine
    command: ["sh", "-c", "if [ -f /init/ready.txt ]; then echo 'File found, starting main container'; else echo 'File not found, exiting'; exit 1; fi && exec nginx -g 'daemon off;'"]
    volumeMounts:
    - name: init-volume
      mountPath: /init

  # Volume definition
  volumes:
  - name: init-volume
    emptyDir: {}
```

### Step 2: Apply the Pod Manifest

Now, apply the manifest to your Kubernetes cluster using `kubectl`.

```bash
kubectl apply -f /home/master/opt/app-pod.yaml
```

### Step 3: Verify the Pod Creation

Check the status of the Pod to confirm that the init container runs successfully before the main container starts:

```bash
kubectl get pods app-pod -o wide
```

### Step 4: Inspect Init Container Logs

To verify that the init container created the file `/init/ready.txt`, you can check the logs of the init container:

```bash
kubectl logs app-pod -c init-config
```

### Step 5: Verify Main Container Startup

Once the init container has completed, confirm that the main container started correctly by checking the logs:

```bash
kubectl logs app-pod -c nginx-container
```

### Explanation of the Manifest

- **Init Container** (`init-config`): Creates an empty file `/init/ready.txt`. The `init-volume` is mounted at `/init`, shared between the init container and main container.
- **Main Container** (`nginx-container`): Checks for the existence of `/init/ready.txt`. If the file is not found, it exits; otherwise, it starts the NGINX service.

---
