# Question 23

There was a security incident where an intruder was able to access the whole cluster from a single hacked backend *Pod*.

To prevent this create a *NetworkPolicy* called `np-backend` in *Namespace* `project-snake`. It should allow the `backend-*` *Pods* only to:

- connect to `db1-*` *Pods* on port 80
- connect to `db2-*` *Pods* on port 80

Use the `app` label of *Pods* in your policy.

After implementation, connections from `backend-*` *Pods* to `vault-*` *Pods* on port 80 should for example no longer work.

---

To complete this CKA practice question on a Kind Kubernetes cluster using Calico for network policies, you’ll need to set up the required `Namespaces`, `Pods`, and `NetworkPolicy`. Here's a step-by-step guide to creating the necessary Kubernetes resources and applying a `NetworkPolicy` to restrict access as described.

### Step 1: Set Up Your Environment

Ensure that:
- Kind Kubernetes cluster is up and running with Calico installed.
- You have `kubectl` access to the cluster.

### Step 2: Create the `project-snake` Namespace

```yaml
kubectl create namespace project-snake
```

### Step 3: Create the Required Pods with Proper Labels

1. **Create `backend-*` Pods**: These will represent the backend services.
2. **Create `db1-*` and `db2-*` Pods**: These will represent the database services.
3. **Create `vault-*` Pod**: This will simulate a restricted service to verify the NetworkPolicy.

Here is the YAML configuration for these Pods:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: backend-app
  namespace: project-snake
  labels:
    app: backend-app
spec:
  containers:
  - name: nginx
    image: nginx

---
apiVersion: v1
kind: Pod
metadata:
  name: db1-app
  namespace: project-snake
  labels:
    app: db1-app
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 1111

---
apiVersion: v1
kind: Pod
metadata:
  name: db2-app
  namespace: project-snake
  labels:
    app: db2-app
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 2222

---
apiVersion: v1
kind: Pod
metadata:
  name: vault-app
  namespace: project-snake
  labels:
    app: vault-app
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 3333
```

Apply the above configurations:

```bash
kubectl apply -f <file_name>.yaml
```

### Step 4: Verify Pods are Running

After applying the configurations, ensure all Pods are in `Running` state:

```bash
kubectl get pods -n project-snake
```

### Step 5: Create the NetworkPolicy

Now, create the NetworkPolicy `np-backend` in the `project-snake` namespace to allow the `backend-*` Pods to connect only to `db1-*` Pods on port 1111 and `db2-*` Pods on port 2222. This policy will restrict access to `vault-*` Pods and other Pods not explicitly allowed.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np-backend
  namespace: project-snake
spec:
  podSelector:
    matchLabels:
      app: backend-app
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: db1-app
    ports:
    - protocol: TCP
      port: 1111
  - to:
    - podSelector:
        matchLabels:
          app: db2-app
    ports:
    - protocol: TCP
      port: 2222
```

Apply the NetworkPolicy:

```bash
kubectl apply -f <file_name>.yaml
```

### Step 6: Verify NetworkPolicy

1. **Check Connectivity**: Try to connect from `backend-app` Pod to the allowed `db1-app` and `db2-app` Pods on the specified ports.
   
   ```bash
   kubectl exec -n project-snake backend-app -- nc -zv db1-app 1111
   kubectl exec -n project-snake backend-app -- nc -zv db2-app 2222
   ```

2. **Test Restricted Access**: Attempt to connect from `backend-app` to the `vault-app` Pod on port 3333, which should be denied:

   ```bash
   kubectl exec -n project-snake backend-app -- nc -zv vault-app 3333
   ```

### Explanation

- The NetworkPolicy `np-backend` specifies `Egress` rules for the `backend-*` Pods.
- It allows connections only to `db1-*` on port 1111 and `db2-*` on port 2222.
- Other connections, like the one to `vault-*` on port 3333, are blocked as they are not specified in the policy.

This setup should meet the requirements of the question and ensure restricted access as intended.

---
