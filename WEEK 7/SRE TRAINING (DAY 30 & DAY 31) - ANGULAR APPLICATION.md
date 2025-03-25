# SRE TRAINING (DAY 30 & DAY 31) - Angular Application Debugging & Deployment 
<hr>

## Aim of the activity:

This activity aims to create an angular app by putting together modular artifacts, debug and deploy.

## 1. Master Script Execution

The master script set up the base of the application. It performed the following tasks:

- Created necessary directories for different components (WSL & Minikube setup, Prometheus, Grafana, Flask API, Angular UI, Kubernetes Manifests).
- Added the setup scripts and configuration files into files for deployment (requirements.txt, DockerFiles, Angular configuration files)
- Initialized Kubernetes deployments and services.
- Creates a new **setup.sh** script that automates this whole process.
- Once the script is run, it creates a new **sre-app** app directory with files and placeholders
- Here is a tree structure of the new directory created with ```tree -d ``` command:
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/tree.png)
  
## 2. File Integration into `sre-app`

Each file had to be placed within the `sre-app` directory in the placeholders for proper execution:

- Component files were given in **angular-app.ts** file and in other separate artifacts.
- Placed all the given files and contents into their respective placeholders.
- Ran the **setup.sh** file.

## 3. Angular Dockerfile Issues and Fixes

### **Issue:**
The first issue faced after running the **setup.sh** file was the Dockerfile build failures. Below are the things i debugged in the Dockerfile:

- The `` ng build --prod ``` command fails because **angular.json** looks for tsconfig.app.json for production builds which did not get created from setup file.
- The **tsconfig files** files require a **main.ts** for the entrypoint of the app and **polyfills.ts** for browser compatibility.
- **extractCSS** and **styles.css** were not being able to be referenced because the styling is embedded inside the **app.component.ts** file.
- Missing **index.html** to reference **app-root**.

### **Fixes:**

- Added `tsconfig.app.json` and `tsconfig.json` files. Difference/photo
- Addres `main.ts` and `polyfills.ts` for the tsconfig files.
- Created **index.html** and referenced **app-root** in it.

### **Updated Dockerfile**

```dockerfile
FROM node:18 as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build --prod

FROM nginx:alpine
COPY --from=build /app/dist/sre-dashboard /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
### **tsconfig.app.json**

```tsconfig.app.json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": []
  },
  "files": ["src/main.ts", "src/polyfills.ts"],
  "include": ["src/**/*.d.ts"],
  "exclude": ["src/test.ts", "src/**/*.spec.ts"]
}
```
### **tsconfig.json**

```tsconfig.json
{
  "compileOnSave": false,
  "compilerOptions": {
    "baseUrl": "./",
    "outDir": "./dist/out-tsc",
    "sourceMap": true,
    "declaration": false,
    "module": "esnext",
    "moduleResolution": "node",
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "importHelpers": true,
    "target": "es2022",
    "typeRoots": ["node_modules/@types"],
    "lib": ["es2022", "dom"]
  }
}
```
<br>
*With this the setup file executed successfully and the angular app is created.*
<br>

## 4. Kubernetes Deployment Issues

### **Angular & Flask Pod Failure**

### **Issue:**
Angular and Flask pod failed with **ImagePullBackOff** error due to `pull access denied`.

### **Fix:**

- Retagged the docker images.
- Pushed the images to dockerhub.
- Changed the dockerfiles to pull the images from dockerhub.
- Deleted the pods and restarted them.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/docker_hub.png)

### **Prometheus Pod Failure**

### **Issue:**
Prometheus failed due to YAML configuration errors.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/CrashloopBack.png)
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/prometheus.png)

### **Fix:**

- Ran `yamllint` to validate YAML syntax before applying configurations.
- Corrected indentation and formatting errors in the `prometheus-configmap.yaml` file.
- Restarted Prometheus pod after fixing the configuration. All four pods were in **Running** state.

## 6. Port Forwarding

To access the services locally, port forwarding was set up:

```sh
kubectl port-forward svc/angular-ui-service 8080:80 -n sre-monitoring
kubectl port-forward svc/flask-api-service 5001:5000 -n sre-monitoring
kubectl port-forward svc/prometheus 9090:9090 -n sre-monitoring
kubectl port-forward svc/grafana 3000:3000 -n sre-monitoring
```

This allowed accessing:
- The Angular UI at `http://localhost:8080`
- Flask API at `http://localhost:5001/api`
- Prometheus at `http://localhost:9090`
- Grafana at `http://localhost:3000`

# Angular Dashboard retrived: 
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/dashboard.png)

# Prometheus Dashboard: 
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/prometheus_dashboard.png)

# Grafana Dashboard:
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/grafana.png)

## 7. Forcing Traffic into Flask API
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/ANGULAR%20APP%20IMAGES/traffic.png)

---
