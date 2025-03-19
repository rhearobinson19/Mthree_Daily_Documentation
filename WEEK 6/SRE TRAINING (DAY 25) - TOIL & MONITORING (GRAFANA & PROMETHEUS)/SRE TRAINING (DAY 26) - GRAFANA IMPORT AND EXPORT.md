# **SRE TRAINING (DAY 26) - GRAFANA DASHBOARD**

## **1. Starting Minikube and Accessing Grafana**
1. Ensure Minikube is running:
   ```sh
   minikube start
   ```
2. Verify that the Grafana pod is running in the `monitoring` namespace:
   ```sh
   kubectl get pods -n monitoring
   ```
3. If Grafana is running, use port forwarding to access it locally:
   ```sh
   kubectl port-forward svc/grafana -n monitoring 3000:80
   ```
4. Open Grafana in a browser at:
   ```
   http://localhost:3000
   ```

## **2. Exporting Dashboards**
1. Navigate to the dashboard in Grafana UI.
2. Click on the **Dashboard settings** (gear icon).
3. Select **JSON Model** and click **Download JSON** to export the dashboard.
4. The JSON file will be downloaded to your local machine.

## **3. Importing Dashboards Back into Grafana**
1. Open Grafana and go to the **Dashboards** section.
2. Click **Import** and upload the previously exported JSON file.
3. Change the UID if required and click **Create**.
4. The dashboard should now be visible in the list of available dashboards.

## **4. Locating Dashboards Inside Grafana Pod**
To find where Grafana stores dashboards inside the pod:
1. Get the running Grafana pod name:
   ```sh
   kubectl get pod -n monitoring -l app.kubernetes.io/name=grafana -o jsonpath="{.items[0].metadata.name}"
   ```
2. Access the Grafana pod:
   ```sh
   kubectl exec -it <grafana-pod-name> -n monitoring -- /bin/sh
   ```
3. Check for dashboard JSON files:
   ```sh
   find /var/lib/grafana -name "*.json"
   ```

## **5. Copying Dashboards from Grafana Pod to Local Machine**
Use `kubectl cp` to transfer the dashboard JSON files:
```sh
kubectl cp monitoring/<grafana-pod-name>:/var/lib/grafana/dashboards/default/dashboard.json ./dashboard.json
```

## **6. Verifying Import & Access**
- After importing, verify the dashboard appears correctly in Grafana.
- Check for any missing panels or data sources and update configurations if necessary.

**DASHBOARDS**
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/SRE%20TRAINING%20(DAY%2025)%20-%20TOIL%20%26%20MONITORING%20(GRAFANA%20%26%20PROMETHEUS)/IMAGES/WhatsApp%20Image%202025-03-18%20at%2010.48.02.jpeg)
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/SRE%20TRAINING%20(DAY%2025)%20-%20TOIL%20%26%20MONITORING%20(GRAFANA%20%26%20PROMETHEUS)/IMAGES/WhatsApp%20Image%202025-03-18%20at%2010.48.02%20(2).jpeg)
![](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%206/SRE%20TRAINING%20(DAY%2025)%20-%20TOIL%20%26%20MONITORING%20(GRAFANA%20%26%20PROMETHEUS)/IMAGES/WhatsApp%20Image%202025-03-18%20at%2010.48.02%20(1).jpeg)
