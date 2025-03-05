# Kubernetes Deployment Notes

##  Concepts Learned

During this Kubernetes project, we covered:

### **1️⃣ Kubernetes Core Concepts**
- **Pods**: Smallest deployable unit in Kubernetes, running our Flask app.
- **Deployments**: Manages rolling updates and ensures multiple replicas are running.
- **Services**: Exposes our application via **NodePort** and **Port Forwarding**.
- **ConfigMaps & Secrets**: Stores environment variables and sensitive credentials.
- **Persistent Volumes & Claims**: Mounts directories for data persistence.
- **Horizontal Pod Autoscaler (HPA)**: Scales pods based on CPU/memory usage.
- **Namespaces**: Isolates resources (created `k8s-demo` namespace).
- **Kubernetes Dashboard**: Web-based UI for managing the cluster.
- **Validation Disabled Deployment**: Used `--validate=false` flag to bypass API issues.
- **Minikube Reset & Connectivity Fixes**: Used `minikube-reset-script.sh` to resolve WSL2-related networking issues.

### **2️⃣ Docker & Containerization**
- **Dockerfile**: Defines how to build our Flask application image.
- **Building Docker Images**: `docker build -t k8s-master-app:latest .`
- **Using Minikube’s Docker Daemon**: Ensured images were built inside Minikube.

### **3️⃣ Networking & Accessing the App**
- **Port Forwarding** (`kubectl port-forward` with retries).
- **NodePort** (`minikube ip` and accessing via port `30080`).
- **Service Discovery**: Used `minikube service k8s-master-app -n k8s-demo`.
- **Port Handling & Collision Avoidance**: If `8080` was in use, script auto-switched ports.
- **WSL2 Network Fixes**: Reset `wsl.conf` and DNS settings for stable connectivity.

---

## **Execution BreakDown**

### **Step 1️⃣: Running the Minikube Reset Script**
```bash
chmod +x minikube-reset-script.sh
./minikube-reset-script.sh
```
✅ **What This Did:**
- **Stopped & deleted any existing Minikube clusters** (`minikube delete --all --purge`).
- **Fixed WSL2 networking issues** (updated `/etc/wsl.conf`, applied Google DNS `8.8.8.8`).
- **Restarted Minikube with optimized settings** (`minikube start --driver=docker ...`).
- **Verified connectivity to Kubernetes API (`kubectl get nodes`)**.

📷 **MiniKube Running:** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/Minikube_Start.png)

### **Step 2️⃣: Running the Deployment Script**
```bash
chmod +x deploy-no-validate.sh
./deploy-no-validate.sh
```
✅ **What This Did:**
- Checked for dependencies (`kubectl`, `docker`, `minikube`).
- Verified if **Minikube was running** (`minikube status`).
- Configured **Docker to use Minikube’s Docker daemon** (`eval $(minikube docker-env)`).

📷 **Deployment Running:** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/Deployment_running.png)

### **Step 3️⃣: Building the Docker Image with Retries**
```bash
cd ~/k8s-master-app/app
docker build --network=host -t k8s-master-app:latest .
```
✅ **What This Did:**
- Built the **Flask application image**.
- Used **host network mode** in WSL2 for better connectivity.
- Implemented **retry logic** (up to 3 attempts if build failed).

📷 **Docker Build:** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/Docker_building.png)

### **Step 4️⃣: Deploying Kubernetes Resources (Without Validation)**
```bash
kubectl apply -f <manifest-file> --validate=false --timeout=20s
```
✅ **What This Did:**
- Created **namespace** (`k8s-demo`).
- Created **ConfigMaps & Secrets** (stores configuration & credentials).
- Created **Persistent Volumes & Claims** (mounted `/logs`, `/config`, `/data`).
- Created **Deployments & Services** (ran Flask app in Kubernetes).
- Created **HorizontalPodAutoscaler (HPA)** (enabled auto-scaling).

📷 **Kubernetes Resources:** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/Resources.png)

### **Step 5️⃣: Checking Deployment Status**
```bash
kubectl -n k8s-demo get deployments --timeout=10s
kubectl -n k8s-demo get pods --timeout=10s
```
✅ **What This Did:**
- Avoided waiting indefinitely due to potential API connectivity issues.
- Displayed **deployment and pod status** instead of blocking execution.

📷 **Deployment Status:** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/Deployment_Success.png)

### **Step 6️⃣: Setting Up Port Forwarding (With Collision Handling)**
```bash
kubectl -n k8s-demo port-forward svc/k8s-master-app 8080:80 &
```
✅ **What This Did:**
- **Checked if port `8080` was already in use** and auto-switched to `8081` if necessary.
- Started **port forwarding in the background**.
- Allowed manual override (`kubectl -n k8s-demo port-forward svc/k8s-master-app 8080:80`).

📷 **Port Forwarding:** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/port_forwarding.png)

### **Step 7️⃣: Accessing the Application**
#### **🔹 Option 1: Port Forwarding (Localhost)**
```bash
kubectl -n k8s-demo port-forward svc/k8s-master-app 8080:80
```
Opened: `http://localhost:8080`

📷 **LocalHost :** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/Localhost.png)

#### **🔹 Option 2: NodePort (Minikube IP)**
```bash
minikube ip  # Get the Minikube IP
```
Accessed at: `http://<MINIKUBE-IP>:30080`

### **Step 8️⃣: Opening the Kubernetes Dashboard**
```bash
minikube dashboard
```
📷 **Dashboard:** ![Screenshot](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%204/SRE%20TRAINING%20(DAY%2018)%20-%20KUBERNETES%20DEPLOYMENT/Kubernetes_Screenshots/Dashboard.png)

### **Step 9️⃣: Cleaning Up Deployment**
```bash
./scripts/cleanup.sh
```
✅ **What This Did:**
- Deleted **all Kubernetes resources** (pods, services, deployments, etc.).
- Stopped Minikube **if no longer needed**.


---
### **✅ Steps Taken: Summary**
1️⃣ **Reset Minikube & Fixed WSL2 Issues (`minikube-reset-script.sh`)**
2️⃣ **Ran `deploy-no-validate.sh` to set up Minikube & Kubernetes resources**
3️⃣ **Fixed API validation issues by using `--validate=false` for all manifests**
4️⃣ **Built Docker image with retry mechanism**
5️⃣ **Deployed Kubernetes resources and ensured connectivity**
6️⃣ **Verified pods, logs, and checked app accessibility**
7️⃣ **Opened Kubernetes Dashboard for monitoring**
8️⃣ **Handled port forwarding issues dynamically**
9️⃣ **Cleaned up resources when done**



