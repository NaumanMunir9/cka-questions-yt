# CKA Practice Question - Ingress Resource for Multi-Path Routing

1. **Create Deployments and Services**: 

   In the `currency-space` namespace, create two Deployments named `app-euro` and `app-yen`, each with one replica of an `nginx` container. Ensure the container is listening on port 80.

   Then, create ClusterIP Services for each Deployment with the same names (`app-euro` and `app-yen`) to expose them internally on port 80.

2. **Create an Ingress Resource**: 

   The cluster has an Nginx Ingress Controller already installed.

   In the `currency-space` namespace, create an Ingress resource named `app-ingress` with the following configurations:

   - Use the domain `app.k8s.local`.
   - Define two paths:
     - `/euro` should route traffic to the `app-euro` Service on port 80.
     - `/yen` should route traffic to the `app-yen` Service on port 80.

3. **Configure Host Resolution**: 

   Add an entry in `/etc/hosts` to point `app.k8s.local` to the internal IP of the Kubernetes cluster's Node.

4. **Verify the Ingress**: 

   Use `curl` or a similar tool to confirm that the following routes are accessible and return the default Nginx welcome page:
   
   - `http://app.k8s.local/euro`
   - `http://app.k8s.local/yen`

5. **Save Configurations and Outputs**:

   - Save the output of the `curl` commands to `/tmp/currency-space-ingress-test.txt`.
   - Save the Ingress YAML to `/tmp/currency-space-ingress.yaml`.

---

### Solution Steps:

#### 1. Create the Namespace

```bash
kubectl create namespace currency-space
```

#### 2. Create Deployments and Services

```yaml
# Save as app-deployments-services.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-euro
  namespace: currency-space
spec:
  replicas: 1
  selector:
    matchLabels:
      app: euro
  template:
    metadata:
      labels:
        app: euro
    spec:
      containers:
      - name: nginx
        image: nginx:1.16-alpine
        ports:
        - containerPort: 80

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-yen
  namespace: currency-space
spec:
  replicas: 1
  selector:
    matchLabels:
      app: yen
  template:
    metadata:
      labels:
        app: yen
    spec:
      containers:
      - name: nginx
        image: nginx:1.16-alpine
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: app-euro
  namespace: currency-space
spec:
  selector:
    app: euro
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: app-yen
  namespace: currency-space
spec:
  selector:
    app: yen
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Apply the configuration:

```bash
kubectl apply -f app-deployments-services.yaml
```

#### 3. Create the Ingress Resource

```yaml
# Save as app-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  namespace: currency-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: app.k8s.local
    http:
      paths:
      - path: /euro
        pathType: Prefix
        backend:
          service:
            name: app-euro
            port:
              number: 80
      - path: /yen
        pathType: Prefix
        backend:
          service:
            name: app-yen
            port:
              number: 80
```

Apply the Ingress:

```bash
kubectl apply -f app-ingress.yaml
```

#### 4. Update `/etc/hosts` for Domain Resolution

Add an entry to `/etc/hosts`:

```bash
# Run as root or use sudo
echo "<Node IP> app.k8s.local" >> /etc/hosts
```

Replace `<Node IP>` with the internal IP of the Kubernetes Node.

#### 5. Test Ingress Routing

```bash
curl http://app.k8s.local/euro
curl http://app.k8s.local/yen
```

Save the outputs:

```bash
curl http://app.k8s.local/euro > /tmp/currency-space-ingress-test.txt
curl http://app.k8s.local/yen >> /tmp/currency-space-ingress-test.txt
```

Save the Ingress YAML:

```bash
kubectl get ingress app-ingress -n currency-space -o yaml > /tmp/currency-space-ingress.yaml
```

---
