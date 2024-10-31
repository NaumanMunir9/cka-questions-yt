# Question 32

## CKA Practice Question: Insert a Sidecar Container to a Running Pod for Log Monitoring

A pod named `multi-container` is running in the cluster and is logging to a volume. You need to insert a sidecar container into the pod that will also read the logs from the volume using this command `tail -f /var/busybox/log/*.log`.

Sidecar must use an image of `busybox` and must have a name of `sidecar` with a volume path of `/var/busybox/log`.

---

**Objective**: Add a sidecar container to an existing Pod (`multi-container`) to monitor logs stored in a shared volume.

---

### Step-by-Step Instructions:

---

#### Step 1: Retrieve the Pod Definition

1. **Export the current YAML configuration of the `multi-container` Pod** to modify it by adding the sidecar container:
   
   ```bash
   kubectl get pod multi-container -o yaml > multi-container.yaml
   ```

---

#### Step 2: Edit the Pod Definition File

1. **Open the `multi-container.yaml` file** for editing:

   ```bash
   vi multi-container.yaml
   ```

2. **Modify the Pod spec** to add the new sidecar container as follows:

   - Locate the `containers` section in the YAML file.
   - Add the following sidecar container configuration beneath the existing container(s):

     ```yaml
     - name: sidecar
       image: busybox
       command: ["sh", "-c", "tail -f /var/busybox/log/*.log"]
       volumeMounts:
         - name: log-volume   # Ensure this matches the volume name used by the main container
           mountPath: /var/busybox/log
     ```

   - Ensure there’s a `volumes` section that defines `log-volume` (if not, add it). It should look like this:

     ```yaml
     volumes:
       - name: log-volume
         emptyDir: {}
     ```

---

#### Step 3: Delete the Existing Pod and Recreate It

1. **Delete the existing `multi-container` Pod** (assuming no critical data will be lost):

   ```bash
   kubectl delete pod multi-container
   ```

2. **Recreate the Pod** with the modified configuration that includes the sidecar container:

   ```bash
   kubectl apply -f multi-container.yaml
   ```

---

#### Step 4: Verify the Sidecar Container

1. **Check the status** of the `multi-container` Pod to ensure both containers are running:

   ```bash
   kubectl get pod multi-container
   ```

2. **Inspect the logs of the sidecar container** to confirm it’s monitoring the logs as expected:

   ```bash
   kubectl logs multi-container -c sidecar
   ```

   This should display the log output from `/var/busybox/log/*.log`.

---

### Summary of Commands

1. Export the Pod’s YAML configuration:

   ```bash
   kubectl get pod multi-container -o yaml > multi-container.yaml
   ```

2. Edit the `multi-container.yaml` to add the sidecar container.

3. Delete and recreate the Pod:

   ```bash
   kubectl delete pod multi-container
   kubectl apply -f multi-container.yaml
   ```

4. Confirm the sidecar container is reading logs:

   ```bash
   kubectl logs multi-container -c sidecar
   ``` 

This process will insert the `sidecar` container into the existing Pod, allowing it to monitor logs in real-time using the specified `tail` command.

---
