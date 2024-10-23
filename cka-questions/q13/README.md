# Question 13

**Objective**: Create a Pod named `multi-container-pod` in the `default` namespace that includes three containers: `c1`, `c2`, and `c3`. The Pod should have a volume attached, which will be mounted into each container, but this volume should not be persisted or shared with other Pods.

### Requirements:

1. **Container c1**:
   - **Image**: Use `nginx:1.17.6-alpine`.
   - **Environment Variable**: Set the environment variable `MY_NODE_NAME` to the name of the node on which the Pod is running.

2. **Container c2**:
   - **Image**: Use `busybox:1.31.1`.
   - **Functionality**: This container should execute a command that writes the output of the `date` command into a file named `date.log` every second within the mounted volume. You can use the following command:
     ```
     while true; do date >> /your/vol/path/date.log; sleep 1; done
     ```

3. **Container c3**:
   - **Image**: Use `busybox:1.31.1`.
   - **Functionality**: This container should continuously output the contents of the `date.log` file from the mounted volume to stdout. You can use the command:
     ```
     tail -f /your/vol/path/date.log
     ```

### Verification:
After creating the Pod, check the logs of container `c3` to confirm that it is correctly sending the contents of the `date.log` file to stdout.

---
