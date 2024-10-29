# Question 21

Check how long the kube-apiserver server certificate is valid on `controlplane`. Do this with openssl of cfssl. Write the expiration date into `/tmp/opt/course/22/expiration`.

Also run the correct `kubeadm` command to list the expiration dates and confirm both methods show the same date.

Write the correct `kubeadm` command that would renew the apiserver server certificate into `/tmp/opt/course/22/kubeadm-renew-certs.sh`.

---

### CKA Practice Question: Check and Renew kube-apiserver Certificate on Control Plane

**Objective**: Check the expiration date of the kube-apiserver certificate using `openssl` or `cfssl`, confirm with `kubeadm`, and write the correct command to renew the certificate.

---

### Step-by-Step Instructions:

---

#### Step 1: SSH into the `controlplane`

1. **SSH into the controlplane node**:

   ```bash
   ssh controlplane
   ```

---

#### Step 2: Check kube-apiserver Certificate Expiration with `openssl`

1. **Find the location of the kube-apiserver certificate**. It's typically located at `/etc/kubernetes/pki/apiserver.crt`.

   ```bash
   ls /etc/kubernetes/pki/apiserver.crt
   ```

2. **Check the certificate expiration date using `openssl`**:

   ```bash
   sudo openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -enddate
   ```

   The output will look like:

   ```
   notAfter=YYYY-MM-DD HH:MM:SS GMT
   ```

3. **Write the expiration date into the file** `/tmp/opt/course/22/expiration`:

   ```bash
   sudo openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -enddate > /tmp/opt/course/22/expiration
   ```

---

#### Step 3: Verify Expiration Date Using `kubeadm`

1. **Run the `kubeadm` command to list the expiration dates of certificates**:

   ```bash
   sudo kubeadm certs check-expiration
   ```

   This will list all the certificates along with their expiration dates. Look for the `kube-apiserver` certificate.

2. **Confirm that the expiration date matches with the one obtained using `openssl`**.

---

#### Step 4: Write the `kubeadm` Command to Renew Certificates

If the certificate is near expiration or you need to renew it:

1. **Write the command to renew the kube-apiserver certificate** into `/tmp/opt/course/22/kubeadm-renew-certs.sh`:

   ```bash
   echo "sudo kubeadm certs renew apiserver" > /tmp/opt/course/22/kubeadm-renew-certs.sh
   ```

2. **Make the script executable** (optional):

   ```bash
   sudo chmod +x /tmp/opt/course/22/kubeadm-renew-certs.sh
   ```

---

### Summary of Commands

1. **SSH into the controlplane**:

   ```bash
   ssh controlplane
   ```

2. **Check the kube-apiserver certificate expiration date using `openssl`**:

   ```bash
   sudo openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -enddate
   ```

3. **Write the expiration date to a file**:

   ```bash
   sudo openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -enddate > /tmp/opt/course/22/expiration
   ```

4. **Run `kubeadm` to list the certificate expiration dates**:

   ```bash
   sudo kubeadm certs check-expiration
   ```

5. **Write the renewal command into a script**:

   ```bash
   echo "sudo kubeadm certs renew apiserver" > /tmp/opt/course/22/kubeadm-renew-certs.sh
   sudo chmod +x /tmp/opt/course/22/kubeadm-renew-certs.sh
   ```

By following these steps, you'll check the expiration date of the kube-apiserver certificate, verify it using `kubeadm`, and write the command to renew the certificate when needed.

---
