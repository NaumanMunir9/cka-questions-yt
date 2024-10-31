# Question 22

Node `kind-worker` has been added to the cluster using `kubeadm` and TLS bootstrapping.

Find the "issue" and "Extended Key Usage" values of the `kind-worker`:

1. kubelet **client** certificate, the one used for outgoing connections to the kube-apiserver.
2. kubelet **server** certificate, the one used for incoming connectioons from the kube-apiserver.

Write the information into file `/tmp/opt/course/23/certificate-info.txt`.

Compare the "Issuer" and "Extended Key Usage" fields of both certificates and make sense of these.

---

### CKA Practice Question: Verify Kubelet Client and Server Certificates on Node `node01`

**Objective**: Retrieve and compare the "Issuer" and "Extended Key Usage" fields of the kubelet client and server certificates on `node01` using `openssl`, and save the information for both certificates.

---

### Step-by-Step Instructions:

---

#### Step 1: SSH into `node01`

1. **Access the `node01` node**:

   ```bash
   ssh node01
   ```

---

#### Step 2: Locate the Certificates

1. **Find the kubelet client certificate**:
   - This is usually located at `/var/lib/kubelet/pki/kubelet-client-current.pem`.
   
2. **Find the kubelet server certificate**:
   - This is typically located at `/var/lib/kubelet/pki/kubelet.crt`.

---

#### Step 3: Extract Information from Kubelet Client Certificate

1. **Use `openssl` to view the Issuer and Extended Key Usage of the client certificate**:

   ```bash
   sudo openssl x509 -in /var/lib/kubelet/pki/kubelet-client-current.pem -noout -issuer -ext "extendedKeyUsage"
   ```

2. **Write the output to the specified file**:

   ```bash
   sudo openssl x509 -in /var/lib/kubelet/pki/kubelet-client-current.pem -noout -issuer -ext "extendedKeyUsage" >> /tmp/opt/course/23/certificate-info.txt
   ```

   - Optionally, add a heading for clarity:

     ```bash
     echo "Kubelet Client Certificate:" | sudo tee -a /tmp/opt/course/23/certificate-info.txt
     ```

---

#### Step 4: Extract Information from Kubelet Server Certificate

1. **Use `openssl` to view the Issuer and Extended Key Usage of the server certificate**:

   ```bash
   sudo openssl x509 -in /var/lib/kubelet/pki/kubelet.crt -noout -issuer -ext "extendedKeyUsage"
   ```

2. **Append the output to the same file**:

   ```bash
   sudo openssl x509 -in /var/lib/kubelet/pki/kubelet.crt -noout -issuer -ext "extendedKeyUsage" >> /tmp/opt/course/23/certificate-info.txt
   ```

   - Optionally, add a heading for clarity:

     ```bash
     echo "Kubelet Server Certificate:" | sudo tee -a /tmp/opt/course/23/certificate-info.txt
     ```

---

#### Step 5: Compare the Issuer and Extended Key Usage Fields

1. **Review the contents** of `/tmp/opt/course/23/certificate-info.txt` to compare the Issuer and Extended Key Usage fields.
   
   - **Kubelet Client Certificate**:
     - The Issuer will typically be the Kubernetes Certificate Authority (CA).
     - Extended Key Usage may include `Client Authentication`, indicating its use in authenticating with the kube-apiserver.
   
   - **Kubelet Server Certificate**:
     - The Issuer should also be the Kubernetes CA.
     - Extended Key Usage may include `Server Authentication`, indicating it’s used to serve requests from the kube-apiserver.

These differences reflect the purposes of each certificate: the client certificate is used for outgoing authentication requests to the kube-apiserver, while the server certificate is used for incoming requests from the kube-apiserver.

---

### Summary of Commands

1. **SSH into node01**:

   ```bash
   ssh node01
   ```

2. **Get Issuer and Extended Key Usage for the kubelet client certificate**:

   ```bash
   echo "Kubelet Client Certificate:" | sudo tee -a /tmp/opt/course/23/certificate-info.txt
   sudo openssl x509 -in /var/lib/kubelet/pki/kubelet-client-current.pem -noout -issuer -ext "extendedKeyUsage" >> /tmp/opt/course/23/certificate-info.txt
   ```

3. **Get Issuer and Extended Key Usage for the kubelet server certificate**:

   ```bash
   echo "Kubelet Server Certificate:" | sudo tee -a /tmp/opt/course/23/certificate-info.txt
   sudo openssl x509 -in /var/lib/kubelet/pki/kubelet.crt -noout -issuer -ext "extendedKeyUsage" >> /tmp/opt/course/23/certificate-info.txt
   ```

By completing these steps, you’ll retrieve, store, and understand the differences in purposes of kubelet’s client and server certificates on `node01`.

---
