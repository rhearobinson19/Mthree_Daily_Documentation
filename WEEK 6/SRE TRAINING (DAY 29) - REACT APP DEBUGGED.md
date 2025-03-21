# SRE TRAINING (DAY 29) - REACT APP DEBUGGING

## Identifying and debugging some more...
Last point we left the script was when kubernetes pods were stuck in a **ImagePullBackOff** state, and we continued to identify the causes as listed:


### **1️⃣ Port Configuration in Kubernetes Service**
- **Previous Script:** Exposed **port 80**, causing a mismatch with the React app.
- **Corrected Script:** Exposed **port 4000** in `service.yaml`.

 **Fix:** Updated `service.yaml` to ensure consistency:
```yaml
spec:
  ports:
    - port: 4000
      targetPort: 4000
      protocol: TCP
      name: http
```

### **2️⃣ Dockerfile Port Exposure Issue**
- **Previous Script:** Used `EXPOSE 80`, conflicting with the Kubernetes service.
- **Corrected Script:** Used `EXPOSE 4000`.
 **Fix:** Updated the Dockerfile to:
```dockerfile
EXPOSE 4000
```

### **3️⃣ Readiness & Liveness Probe Issues**
- **Previous Script:** Used the incorrect port, leading to Kubernetes pods failing readiness checks.
- **Corrected Script:** Configured health checks for `/health` on **port 4000**.

 **Fix:** Corrected probes in `deployment.yaml`:
```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 4000
livenessProbe:
  httpGet:
    path: /health
    port: 4000
```


### **4️⃣ `patchesStrategicMerge` Deprecated in Kustomization**
**Error:**
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/patches_error.png)
**Fix:** Replaced `patchesStrategicMerge` with `patches` in `kustomization.yaml`:
```yaml
patches:
  - path: replica-count.yaml
  - path: resource-limits.yaml
```

### **5️⃣ Incorrect YAML format in `service.yaml`**
**Error:** <br>
**Used **yamllint** package to identify the yaml syntax issues** <br>
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/yaml.png)
**Fix:** Corrected indentation and spacing in `service.yaml`:
```yaml
ports:
  - port: 4000
    targetPort: 4000
    protocol: TCP
    name: http
```

After applying these fixes, the app was deployed **successfully**. 

---

## Grafana Observations
### **1️⃣ Successfully Created Panel for HTTP Requests**
- After fixing the deployment, the panel for **HTTP request metrics** was successfully created in Grafana as to be done by the script.
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/react-app.png)
- **However, no data appeared in the panel yet since we have made any requests.**

### **2️⃣ Queries to Check Prometheus Health**
To verify if Prometheus was running correctly, we executed few more of following queries:

#### **Prometheus Server Health** 
```yaml
rate(prometheus_target_scrapes_sample_out_of_order_total[5m])
```
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/grafana2.png)
#### **Prometheus Query Execution Time**
```yaml
rate(prometheus_engine_query_duration_seconds_sum[5m])
```
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/REACT%20APP%20IMAGES/grafana1.png)
