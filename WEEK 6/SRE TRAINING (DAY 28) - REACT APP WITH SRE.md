# SRE TRAINING (DAY 28) - REACT APP DEPLOYMENT WITH SRE

## Aim of the script:
This script automates the setup of a **React application** with **Site Reliability Engineering (SRE) concepts**. It integrates **Minikube, Kubernetes, Prometheus, and Grafana** for monitoring, ensuring a full-fledged deployment environment within **WSL**.

## Features Implemented
- **React Application Setup** with SRE best practices.
- **Minikube Configuration** for Kubernetes cluster.
- **Python 3.9+ Dependency Installation**.
- **Docker & Node.js Setup** for containerized app deployment.
- **Prometheus & Grafana Integration** for monitoring.
- **Automated Deployment to Kubernetes**.

## Script Breakdown


### 1️⃣ Project Initialization
The script creates necessary directories for the project, including locations for the React app, Kubernetes configurations, monitoring tools, and supporting scripts.

### 2️⃣ Checking WSL Environment
A validation step ensures that the script is being executed within a WSL environment. If the system is not recognized as WSL, the script exits with an error message.

### 3️⃣ Installing System Dependencies
Essential dependencies such as Docker, Python, Node.js, Kubernetes CLI tools, and Helm are installed. This ensures that the environment is properly set up for containerized deployment and orchestration.

### 4️⃣ Setting Up the React Application
The script initializes a React application and installs required dependencies like Axios for HTTP requests, React Router for navigation, and SWR for data fetching. This ensures that the application is correctly configured before deployment.

### 5️⃣ Configuring Kubernetes Deployment
The script sets up Kubernetes configurations, including namespace creation, service definitions, and deployment specifications. This allows the React app to run within a Kubernetes cluster with appropriate scaling and networking settings.

### 6️⃣ Deploying Prometheus & Grafana for Monitoring
Monitoring tools like Prometheus and Grafana are configured within the Kubernetes cluster. This ensures that logs, metrics, and performance data can be collected and visualized for better application reliability.

### 7️⃣ Deploying the React Application to Kubernetes
Once Minikube is running, the script applies the Kubernetes configuration to deploy the React application. It then waits for the application to start and verifies that the pods are running successfully. <hr>

# DEBUGGING THE SCRIPT
Below is a breakdown of each problem, its cause, and the solution applied.

## **1️⃣ Problem: WSL Not Detecting**  

### **Cause:**  
The script originally checked if it was running inside WSL using `grep -q Microsoft /proc/version`, but this failed on **WSL2** because the identifiers changed and the checking was **case-sensitive**. This led to WSL not being detectable.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/wsl_issue.png)
### **Solution:**  
Modified the detection command to check for both **"microsoft"** and **"wsl"**, ensuring compatibility with both WSL1 and WSL2 and making it case-insensitive. <br>
What the flags mean: <br>
**-q** → Quiet mode. The script only displays if WSL is detected (exit status 0 for match, 1 for no match). <br>
**-E** → Extended Regex for the | (OR) operator, matching either "microsoft" or "wsl". <br>
**-i** → Ensures matches are case-insensitive (WSL, wsl, Microsoft, MICROSOFT all match).: <br>

```sh
if ! grep -qEi "(microsoft|wsl)" /proc/version; then
```

---

## **2️⃣ Problem: React and Grafana Version Conflict**  

### **Cause:**  
The script attempted to install **React 19**, but **Grafana UI**. Since the script installs the latest version of React which is React 19 and the latest version of grafana does not support React 19, therefore causing dependency conflicts.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/react_issue.png)
### **Solution:**  
Issue can be resolved in two ways:  
1. **Downgrade React** to version 18 to match Grafana’s requirements.  
   ```sh
   npm install react@18 react-dom@18
   ```
2. Use the `--legacy-peer-deps` flag in `npm install` to bypass peer dependency conflicts.  
   ```sh
   npm install --legacy-peer-deps
   ```

---

## **3️⃣ Problem: Python `apt_pkg` Import Error (Python 3.9 vs. 3.12)**  

### **Cause:**  
The script expected **Python 3.9**, but **Python 3.12** was also installed. This caused `apt_pkg` to be installed for Python 3.12 but missing for Python 3.9, leading to package update failures.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/apt_error.png)
### **Solution:**  
1. Verified the issue by running:  
   ```sh
   python3 -c "import apt_pkg"
   ```
2. Reinstalled `python3-apt` to ensure compatibility with the correct Python version:  
   ```sh
   sudo apt install --reinstall python3-apt -y
   ```
3. Ran `sudo apt update` after fixing the module import issue:  
   ```sh
   sudo apt update
   ```

---

## **4️⃣ Problem: File Duplication Due to Existing React App**  

### **Cause:**  
Running the script multiple times caused an existing React app directory to interfere with `npx create-react-app`, resulting in conflicts.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/duplication_error.png)
### **Solution:**  
Modified the script to check if the directory exists and delete it before recreating the app:
```sh
if [ -d "$HOME/react-sre-project/sre-react-app" ]; then
  rm -rf "$HOME/react-sre-project/sre-react-app"
fi
npx create-react-app sre-react-app
```

---

## **5️⃣ Problem: `@prometheus/client` Not Found Error**  

### **Cause:**  
The script tried to install `@prometheus/client`, but this package does not exist in the **npm registry**, leading to an error.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/prom_error.png)
### **Solution:**  
Replaced `@prometheus/client` with **`prom-client`**, which is the correct package for Prometheus monitoring in Node.js:
```sh
npm install prom-client
```

---
# UNTIL HERE THE SCRIPT RUNS SUCCESSFULLY !!
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/successful_script.png)
<BR>
### Below problems were encountered when running the deploy-sre-app.sh file.
## **1️⃣ Problem: Namespace Not Getting Created (`kubectl apply -f` Error)**  

### **Cause:**  
The script attempted to apply Kubernetes configurations, but the namespace was not getting created, causing deployment failures.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/namespace_issue.png)
### **Solution:**  
1. **Applied each Kubernetes YAML file separately** to identify the issue:  
   ```sh
   kubectl apply -f namespace.yaml
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   kubectl apply -f ingress.yaml
   ```
2. Found that **Kustomize was not correctly handling namespaces**, so we manually created the namespace:  
   ```sh
   kubectl create namespace react-sre-app
   ```

---

## **2️⃣ Problem: `cwd` Error in `subprocess.run` While Installing NPM Dependencies**  

### **Cause:**  
The script originally used `cwd="$REACT_APP_DIR"`, which was not recognized as a valid argument. **"$REACT_APP_DIR"** was treated as a string in Python, not evaluated as a variable.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/cwd-error.png)
### **Solution:**  
Fixed the script by using `subprocess.run()` with the correct `cwd` parameter:
```sh
subprocess.run(["npm", "install"], cwd=REACT_APP_DIR, capture_output=True)
```
This ensured that npm dependencies were installed in the correct directory.

---

## **3️⃣ Problem: Docker building getting timed out**  

### **Cause:**  
The script execution might be consuming too many resources, causing Docker to slow down, therefore the docker building got timed out.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/docker_image_issue.png)
### **Solution:**  
Ran Docker Separately Before Running the Full Script:  
   ```sh
   docker build -t react-sre-app:latest .
   ```
Image built successfully: <br>
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/manual_docker.png)

## **4️⃣ Problem: File Duplication Due to Existing React App**  

![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/timeout.png)

**Upon running the below command, the pod status showed **ImagePullBackOff** error, indicating an issue in pulling Docker images.
```sh
   docker build -t react-sre-app:latest .
   ``` 
*Status - Yet to be fixed*
<hr>
Successfully fixed multiple issues in the script, improving its reliability, but there are still more issues to resolve for a fully stable deployment.
