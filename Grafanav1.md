Grafana is not built-in for Kubernetes; you need to include and deploy Grafana separately, typically using an existing Helm chart. The Grafana community provides an official Helm chart that you can use to deploy Grafana in your Kubernetes cluster.

Here’s how you can include and deploy Grafana using the official Helm chart:

### Steps to Deploy Grafana using Helm Chart

1. **Add the Grafana Helm Repository:**

   First, add the Grafana Helm repository to your Helm client:

   ```sh
   helm repo add grafana https://grafana.github.io/helm-charts
   helm repo update
   ```

2. **Create a Custom Values File:**

   Create a custom `values.yaml` file to configure your Grafana deployment. This file allows you to customize various aspects of the Grafana setup, such as dashboards, datasources, and other settings.

   Here is an example `values.yaml`:

   ```yaml
   persistence:
     enabled: true
     storageClassName: "standard"
     accessModes:
       - ReadWriteOnce
     size: 10Gi

   adminUser: admin
   adminPassword: admin

   datasources:
     datasources.yaml:
       apiVersion: 1
       datasources:
         - name: Prometheus
           type: prometheus
           access: proxy
           url: http://prometheus-server
           isDefault: true

   dashboards:
     default:
       my-dashboard:
         json: |
           {
             "annotations": {
               "list": [
                 {
                   "builtIn": 1,
                   "datasource": "-- Grafana --",
                   "enable": true,
                   "hide": true,
                   "iconColor": "rgba(0, 211, 255, 1)",
                   "name": "Annotations & Alerts",
                   "type": "dashboard"
                 }
               ]
             },
             "editable": true,
             "gnetId": null,
             "graphTooltip": 0,
             "id": null,
             "iteration": 1603477003621,
             "links": [],
             "panels": [
               {
                 "datasource": "Prometheus",
                 "fieldConfig": {
                   "defaults": {
                     "color": {
                       "mode": "thresholds"
                     },
                     "thresholds": {
                       "mode": "absolute",
                       "steps": [
                         {
                           "color": "green",
                           "value": null
                         },
                         {
                           "color": "red",
                           "value": 80
                         }
                       ]
                     }
                   }
                 },
                 "gridPos": {
                   "h": 8,
                   "w": 12,
                   "x": 0,
                   "y": 0
                 },
                 "id": 2,
                 "options": {
                   "orientation": "auto"
                 },
                 "pluginVersion": "7.3.1",
                 "targets": [
                   {
                     "expr": "up",
                     "interval": "",
                     "legendFormat": "",
                     "refId": "A"
                   }
                 ],
                 "title": "Panel Title",
                 "type": "timeseries"
               }
             ],
             "refresh": "",
             "schemaVersion": 27,
             "style": "dark",
             "tags": [],
             "templating": {
               "list": []
             },
             "time": {
               "from": "now-6h",
               "to": "now"
             },
             "timepicker": {},
             "timezone": "",
             "title": "New dashboard",
             "uid": "kGVmfPaMk",
             "version": 1
           }
   ```

3. **Install Grafana Using Helm:**

   Use the Helm CLI to install Grafana with the customized `values.yaml` file:

   ```sh
   helm uninstall grafana grafana/grafana -f values.yaml
   ```

4. **Access Grafana:**

   After the installation, you can access the Grafana UI. To get the login credentials and URL:

   ```sh
   kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
   kubectl get svc --namespace default -l app.kubernetes.io/name=grafana -o jsonpath="{.items[0].status.loadBalancer.ingress[0].hostname}"
   ```

   Alternatively, if using a NodePort:

   ```sh
   kubectl get svc --namespace default -l app.kubernetes.io/name=grafana
   ```

### Summary

Grafana is not natively built into Kubernetes, but it can be easily deployed using the official Helm chart. The above steps guide you through adding the Grafana Helm repository, creating a custom `values.yaml` file, installing Grafana, and accessing the Grafana dashboard. This approach allows you to configure Grafana to meet your specific requirements within your Kubernetes environment.


tX5aAMV2uuptA3bZF1XG0RECJxUxfJrEhY00Mos2


