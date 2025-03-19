# SRE TRAINING (DAY 27) - Grafana Dashboard Setup and Troubleshooting Report

## Shell Script Execution

### **Step 1: Running the Shell Script**
- We executed a shell script to set up the monitoring environment.
- The script included commands to install Prometheus, Loki, and Grafana.
- It also configured Prometheus to scrape metrics and Loki to collect logs.

### **Updates and Fixes Applied**

#### **Deprecated `--short` Flag in `kubectl`**
- **Issue:** `kubectl version --short` is deprecated.
- **Fix:** Replaced with `kubectl version --output=yaml`, which provides the full version details in a structured YAML format.

#### **Werkzeug Version Error**
- **Issue:** The application encountered an error due to an incompatible `werkzeug` version, causing an import failure.
- **Fix:** Downgraded `werkzeug` to version **2.2.3**, which resolved the issue.

## **Grafana Dashboard Setup**

### **Basic SRE Dashboard**

#### **1. Request Rate Panel (Traffic)**
- **Query:**
  ```
  sum(rate(app_request_count{namespace="sre-demo"}[1m])) by (endpoint)
  ```
- **Visualization:** Time series
- **Title:** Request Rate by Endpoint
- **Unit:** requests/sec
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/req_rate.png)

#### **2. Error Rate Panel (Errors)**
- **Query:**
  ```
  sum(rate(app_request_count{namespace="sre-demo", http_status=~"5.."}[1m])) / sum(rate(app_request_count{namespace="sre-demo"}[1m])) * 100
  ```
- **Visualization:** Stat
- **Title:** Error Rate (%)
- **Thresholds:**
  - 0-1: Green
  - 1-5: Orange
  - 5-100: Red
- **Unit:** Percent (0-100)
  <br>
*Since there were no errors, this dashboard was empty*

#### **3. Latency Panel (Latency)**
- **Query:**
  ```
  histogram_quantile(0.95, sum(rate(app_request_latency_seconds_bucket{namespace="sre-demo"}[5m])) by (le, endpoint))
  ```
- **Visualization:** Time series
- **Title:** 95th Percentile Latency by Endpoint
- **Unit:** Seconds
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/latency.png)
#### **4. Active Requests Panel (Saturation)**
- **Query:**
  ```
  app_active_requests{namespace="sre-demo"}
  ```
- **Visualization:** Stat
- **Title:** Active Requests
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/active_req.png)

### **Log Analysis Dashboard**

#### **1. Log Panel**
- **Query:**
  ```
  {namespace="sre-demo"}
  ```
- **Visualization:** Logs
- **Title:** Application Logs
- **Options:** Show time enabled
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/app_logs.png)

#### **Error Logs Panel**
- **Query:**
  ```
  {namespace="sre-demo"} |= "error"
  ```
- **Visualization:** Logs
- **Title:** Error Logs <br>
*Since there were no errors, dashboard was empty*

#### **Log Volume Panel**
- **Query:**
  ```
  sum(count_over_time({namespace="sre-demo"}[1m]))
  ```
- **Visualization:** Time series
- **Title:** Log Volume
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/log_vol.png)

### **Advanced Panels Added**

#### **CPU Usage Panel**
- **Query:**
  ```
  sum(rate(container_cpu_usage_seconds_total{namespace="sre-demo"}[5m])) by (pod)
  ```
- **Visualization:** Time series
- **Title:** CPU Usage per Pod
- **Unit:** Cores
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/cpu_usage.png)
#### **Memory Usage Panel**
- **Query:**
  ```
  sum(container_memory_usage_bytes{namespace="sre-demo"}) by (pod)
  ```
- **Visualization:** Gauge
- **Title:** Memory Usage per Pod
- **Unit:** Bytes
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/memory_usage.png)
#### **API Requests Panel**
- **Query:**
  ```
  histogram_quantile(0.99, sum(rate(app_request_latency_seconds_bucket{namespace="sre-demo"}[5m])) by (le, endpoint))
  ```
- **Visualization:** Time series
- **Title:** 99th Percentile Slow API Requests
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/api_req.png)
#### **Pod Status Panel**
- **Query:**
  ```
  count by (pod, phase) (kube_pod_status_phase{namespace="sre-demo"})
  ```
- **Visualization:** Table
- **Title:** Pod Status
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/Images/IMAGES/pod_status.png)
  ```
- **Visualization:** Time series
- **Title:** 99th Percentile Slow API Requests

