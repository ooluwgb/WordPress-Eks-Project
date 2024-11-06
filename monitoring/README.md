
# Prometheus and Grafana Setup on EKS with LoadBalancer Services

## Step 1: Add the Helm Repository

Add the Prometheus Community Helm repository:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

## Step 2: Install Prometheus and Grafana with LoadBalancer Services

Install the `kube-prometheus-stack` Helm chart, setting Grafana and Prometheus services to type `LoadBalancer`:

```bash
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring --create-namespace \
  --set grafana.service.type=LoadBalancer \
  --set prometheus.server.service.type=LoadBalancer
```

This command:

- Installs the Prometheus and Grafana stack in a `monitoring` namespace.
- Sets both Grafana and Prometheus services to be of type `LoadBalancer`.

## Step 3: Retrieve External IPs for Grafana and Prometheus

Get the external IPs of the services using the following command:

```bash
kubectl get svc -n monitoring
```

You should see output similar to this:

```plaintext
NAME                          TYPE           CLUSTER-IP       EXTERNAL-IP       PORT(S)          AGE
prometheus-grafana            LoadBalancer   xx.xx.xx.xx      <external-ip>     80:3000/TCP      ...
prometheus-kube-prometheus    LoadBalancer   xx.xx.xx.xx      <external-ip>     9090:9090/TCP    ...
```

- **Grafana** will be accessible via `<external-ip>:80`.
- **Prometheus** will be accessible via `<external-ip>:9090`.

## Step 4: Access Grafana and Prometheus

### Access the Monitoring Interfaces:

Open a browser and navigate to the external IP for Grafana to access it.

Use the default Grafana credentials if you haven’t set custom ones:

- **Username**: `admin`
- **Password**: `prom-operator` (or as defined in the `values.yaml` file)

Similarly, navigate to the external IP for Prometheus to view the Prometheus dashboard.
