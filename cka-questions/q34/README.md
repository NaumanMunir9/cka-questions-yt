# Question 34

Create a *ConfigMap* named `app-config` in the `default` Namespace with the following content:

```yaml
database_url: "jdbc:mysql://db-host:3306/app_db"
log_level: "debug"
```

Then, create a Pod named `configmap-pod` using the `nginx` image with the following requirements:

1. Set the `database_url` key from the `app-config` ConfigMap as an environment variable named `DB_URL` inside the container.
2. Mount the `app-config` ConfigMap as a volume to `/etc/config` in the container. Each key should become a file within this directory.

Once the Pod is created, verify that the environment variable `DB_URL` and the files in `/etc/config` contain the expected values from the ConfigMap.

---

Here's a step-by-step solution to create the `ConfigMap` and `Pod` as described. This solution is compatible with a local `kind` Kubernetes cluster.

### Step 1: Create the ConfigMap
1. Use the following YAML to create a `ConfigMap` named `app-config` in the `default` Namespace with the specified data:
   
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: app-config
     namespace: default
   data:
     database_url: "jdbc:mysql://db-host:3306/app_db"
     log_level: "debug"
   ```

2. Save this YAML content into a file called `app-config.yaml`, and then apply it:

   ```bash
   kubectl apply -f app-config.yaml
   ```

3. Confirm that the `ConfigMap` was created successfully:

   ```bash
   kubectl get configmap app-config -o yaml
   ```

### Step 2: Create the Pod
1. Use the following YAML to define a `Pod` named `configmap-pod` using the `busybox` image. It will:
   - Set the `database_url` key from the `app-config` ConfigMap as an environment variable `DB_URL`.
   - Mount the entire `app-config` ConfigMap as a volume at `/etc/config`.

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: configmap-pod
     namespace: default
   spec:
     containers:
       - name: busybox-container
         image: busybox
         command: ['sh', '-c', 'sleep 3600']
         env:
           - name: DB_URL
             valueFrom:
               configMapKeyRef:
                 name: app-config
                 key: database_url
         volumeMounts:
           - name: config-volume
             mountPath: /etc/config
     volumes:
       - name: config-volume
         configMap:
           name: app-config
   ```

2. Save this YAML content to a file named `configmap-pod.yaml`, then apply it to create the Pod:

   ```bash
   kubectl apply -f configmap-pod.yaml
   ```

3. Verify that the `Pod` was created and is running:

   ```bash
   kubectl get pod configmap-pod
   ```

### Step 3: Verify the ConfigMap Environment Variable and Mounted Volume in the Pod
1. To check the `DB_URL` environment variable inside the `Pod`:
   
   ```bash
   kubectl exec configmap-pod -- printenv DB_URL
   ```
   
   This should output:

   ```
   jdbc:mysql://db-host:3306/app_db
   ```

2. To check the contents of the mounted files in `/etc/config`:

   ```bash
   kubectl exec configmap-pod -- ls /etc/config
   ```
   
   This should list two files, `database_url` and `log_level`.

3. Finally, check the contents of each file:

   ```bash
   kubectl exec configmap-pod -- cat /etc/config/database_url
   ```
   
   Expected output:
   
   ```
   jdbc:mysql://db-host:3306/app_db
   ```

   ```bash
   kubectl exec configmap-pod -- cat /etc/config/log_level
   ```
   
   Expected output:

   ```
   debug
   ```

### Summary
This setup confirms that:
- The `DB_URL` environment variable is correctly set from the `ConfigMap`.
- The `ConfigMap` data is mounted as files under `/etc/config`.

---
