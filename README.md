# Demo Manifests And Application Used In DevOps Toolkit Videos

# Source: https://gist.github.com/e593ff9fa1a34d286e4b7f739bf47bc2

###################################################
# Monitoring, Logging, And Alerting In Kubernetes #
# https://youtu.be/XR_yWlOEGiA                    #
###################################################

# Additional Info:
# - Prometheus: https://prometheus.io
# - Robusta: https://robusta.dev
# - Loki: https://grafana.com/oss/loki
# - Grafana: https://grafana.com/oss/grafana
# - Kubernetes Notifications, Troubleshooting, And Automation With Robusta: https://youtu.be/2P76WVVua8w

#########
# Setup #
#########

# Create a Kubernetes cluster with Ingress

# Using Rancher Desktop for the demo, but it can be any other Kubernetes cluster with Ingress

# If not using Rancher Desktop, replace `127.0.0.1` with the base host accessible through NGINX Ingress
export INGRESS_HOST=127.0.0.1
export INGRESS_HOST=f5389e7c-01a8-47cd-9d6b-cec673caafc9.k8s.civo.com

# # Change the value if NOT using NGINX Ingress
# export INGRESS_CLASS_NAME=nginx

helm repo add prometheus \
    https://prometheus-community.github.io/helm-charts

helm repo add grafana \
    https://grafana.github.io/helm-charts

helm repo update

git clone https://github.com/vfarcic/prometheus-loki-grafana-demo

cd prometheus-loki-grafana-demo

########################################
# Metrics And Alerting With Prometheus #
########################################

# If Ingress is not set to act as default, you might need to add `--set server.ingress.ingressClassName=YOUR_INGRESS`.
helm upgrade --install \
    prometheus prometheus/prometheus \
    --namespace monitoring \
    --create-namespace \
    --values prometheus-values.yaml \
    --set "server.ingress.hosts[0]=prometheus.$INGRESS_HOST.nip.io" \
    --wait

--- k3s ---
helm upgrade --install \
    prometheus prometheus-community/prometheus \
    --namespace monitoring \
    --create-namespace \
    --values prometheus-values.yaml \
    --set "server.ingress.hosts[0]=$INGRESS_HOST/prometheus/" \
    --set "prometheus.podMonitor=false" \
    --wait

---
helm upgrade --install \
    prometheus prometheus/prometheus \
    --namespace monitoring \
    --create-namespace \
    --values prometheus-values.yaml \
    --wait

echo "http://prometheus.$INGRESS_HOST.nip.io"

# Open the output in a browser

# container_cpu_usage_seconds_total
# rate(container_cpu_usage_seconds_total[5m])

# Open https://prometheus.io/docs/prometheus/latest/querying/functions/

# rate(container_cpu_usage_seconds_total{namespace="monitoring"}[5m])
# rate(container_cpu_usage_seconds_total{namespace="monitoring", container="prometheus-node-exporter"}[5m])

# Open https://github.com/prometheus-community/helm-charts/tree/main/charts

#############################
# Logs Collection With Loki #
#############################

helm upgrade --install \
    loki grafana/loki-stack \
    --namespace monitoring \
    --create-namespace \
    --wait

###########################
# Dashboards With Grafana #
###########################

cat grafana-values.yaml

# Open https://grafana.com/grafana/dashboards in a browser

helm upgrade --install \
    grafana grafana/grafana \
    --namespace monitoring \
    --create-namespace \
    --values grafana-values.yaml \
    --set "ingress.hosts[0]=grafana.$INGRESS_HOST.nip.io" \
    --wait

kubectl --namespace monitoring \
    get secret grafana \
    --output jsonpath="{.data.admin-password}" \
    | base64 --decode ; echo

echo "http://grafana.$INGRESS_HOST.nip.io"

# User: `admin`
# Password: the output of the `echo` command

# rate(container_cpu_usage_seconds_total{namespace="monitoring", container="prometheus-node-exporter"}[5m])

###########
# Destroy #
###########

# Destroy or reset the cluster
@fpinero
 
Add heading textAdd bold text, <Cmd+b>Add italic text, <Cmd+i>
Add a quote, <Cmd+Shift+.>Add code, <Cmd+e>Add a link, <Cmd+k>
Add a bulleted list, <Cmd+Shift+8>Add a numbered list, <Cmd+Shift+7>Add a task list, <Cmd+Shift+l>
Directly mention a user or team
Reference an issue or pull request
Leave a comment
Ninguno archivo selec.
Attach files by dragging & dropping, selecting or pasting them.
Styling with Markdown is supported
Footer
© 2023 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Docs
Contact GitHub
Pricing
API
Training
Blog
About
