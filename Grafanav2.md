https://grafana.com/docs/grafana/latest/setup-grafana/installation/helm/

```shell
helm repo add grafana https://grafana.github.io/helm-charts
```
```shell
helm repo list
```
NAME    URL
grafana https://grafana.github.io/helm-charts
```shell
helm repo update
```
```shell
kubectl create namespace monitoring
```
```shell
helm search repo grafana/grafana
```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
grafana/grafana                 8.3.6           11.1.0          The leading tool for querying and visualizing t.
```shell
helm install my-grafana grafana/grafana --namespace monitoring
```
```shell
helm list -n monitoring
```
```shell
kubectl get secret --namespace monitoring my-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
```shell
export POD_NAME=$(kubectl get pods --namespace monitoring -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=my-grafana" -o jsonpath="{.items[0].metadata.name}")
     kubectl --namespace monitoring port-forward $POD_NAME 3000
```
mYUp82wKfdzmUJNgJSmTDLEIM1tKEcjWxpc3cv1x

