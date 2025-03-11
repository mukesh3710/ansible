# **AWX Deployment Guide (End-to-End)**

## **Prerequisites**
Before we start, ensure you have:
- A **Linux server** with internet access (Ubuntu/CentOS/RHEL)
- Basic **command-line knowledge**
- **Kubernetes installed** (We will use K3s for simplicity)
- **Git installed** (For cloning AWX repositories)

---

## **1️⃣ Install a Kubernetes Cluster (K3s)**
AWX now uses the **AWX Operator**, which runs on Kubernetes.  
For a lightweight setup, we’ll install **K3s** instead of a full Kubernetes cluster.

### **🔹 Install K3s**
Run the following command to install **K3s**:
```bash
curl -sfL https://get.k3s.io | sh -
```
- This installs **K3s** as a **single-node Kubernetes cluster**.
- Once installed, K3s will automatically start as a service.

### **🔹 Verify K3s Installation**
Check if the cluster is running:
```bash
kubectl get nodes
```
If successful, you should see your node in the **Ready** state.

---

## **2️⃣ Set Up DNS (Optional)**
If you're using **local domains**, update your DNS records so the server is resolvable.

Example (if using a local BIND DNS server):
1. Open your DNS zone file and add an **A record** for the AWX server:
   ```
   awx  IN  A  172.16.1.38
   ```
2. Update the **serial number** and restart the DNS service.

---

## **3️⃣ Install Dependencies**
Install essential tools needed for AWX deployment:
```bash
sudo apt update && sudo apt install -y git make jq
```

---

## **4️⃣ Clone the AWX Operator Repository**
The **AWX Operator** is required to deploy AWX. Clone it from GitHub:
```bash
git clone https://github.com/ansible/awx-operator.git
cd awx-operator
```

---

## **5️⃣ Set Up Kubernetes Namespace**
Define a namespace for AWX:
```bash
export NAMESPACE=awx
kubectl create namespace $NAMESPACE
```
Set the namespace as the current context:
```bash
kubectl config set-context --current --namespace=$NAMESPACE
```

---

## **6️⃣ Deploy the AWX Operator**
We will now deploy the **AWX Operator**, which manages AWX installation.

1. **Check the latest AWX Operator release:**
   ```bash
   git fetch --all --tags
   git checkout $(git tag | sort -V | tail -n1)
   ```

2. **Deploy the AWX Operator:**
   ```bash
   make deploy
   ```

3. **Verify the Operator Deployment:**
   ```bash
   kubectl get pods -n $NAMESPACE
   ```
   Look for a running pod named **awx-operator-controller-manager**.

---

## **7️⃣ Deploy AWX**
Now that the AWX Operator is running, we will create an **AWX instance**.

1. **Create a YAML file for AWX deployment:**
   ```bash
   cat <<EOF > awx-deployment.yaml
   apiVersion: awx.ansible.com/v1beta1
   kind: AWX
   metadata:
     name: awx
   spec:
     service_type: nodeport
   EOF
   ```

2. **Apply the AWX deployment:**
   ```bash
   kubectl apply -f awx-deployment.yaml
   ```

3. **Monitor the AWX Deployment:**
   ```bash
   watch kubectl get pods -n $NAMESPACE
   ```
   This will continuously display the **status** of the AWX deployment.

   - First, a **PostgreSQL database** will start.
   - Then, the **AWX application and web interface** will launch.

---

## **8️⃣ Retrieve AWX Service Details**
Once all pods are running, check which **port** AWX is using:
```bash
kubectl get svc -n $NAMESPACE
```
You’ll see an entry like:
```
awx-service    NodePort    10.43.176.53    <none>        80:32732/TCP
```
This means AWX is accessible at **port 32732** on your server.

---

## **9️⃣ Retrieve Admin Password**
AWX automatically generates an **admin password**, stored as a **Kubernetes secret**.

To retrieve the password:
```bash
kubectl get secret awx-admin-password -n $NAMESPACE -o jsonpath="{.data.password}" | base64 --decode
```
Save this password for login.

---

## **🔟 Access the AWX Web Interface**
Now, open a browser and access AWX using:
```
http://<your-server-ip>:<nodeport>
```
Example:
```
http://172.16.1.38:32732
```
Use the **username `admin`** and the **retrieved password** to log in.

---

## **✅ Conclusion**
You have successfully deployed AWX! 🎉

### **Summary of Steps:**
1. Installed **K3s** (lightweight Kubernetes).
2. Configured **DNS** (optional).
3. Installed **dependencies** (`git`, `jq`, `make`).
4. Cloned the **AWX Operator** repository.
5. Created the **AWX namespace**.
6. Deployed the **AWX Operator**.
7. Created and applied the **AWX instance YAML**.
8. Monitored **AWX pod startup**.
9. Retrieved the **AWX service details & admin password**.
10. **Logged into the AWX Web UI**.

Would you like to extend this guide with **additional configurations**, such as **SSL setup or custom port settings**? 🚀

