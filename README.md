
# Kubernetes Monitoring Stack Setup (Prometheus + Grafana + Alertmanager + Node Exporter + Kube-State-Metrics)

This repository contains Kubernetes manifests to deploy a basic monitoring stack on a MicroK8s cluster. The following components are included:

- **Prometheus** – Metrics collection
- **Grafana** – Metrics visualization
- **Alertmanager** – Alerting engine
- **Node Exporter** – Node-level metrics
- **Kube-State-Metrics** – Kubernetes object state metrics
- **ConfigMaps** – Prometheus, Alertmanager & alert rules configuration
- **Namespace** – All components are deployed under the `monitoring` namespace

---

##  Prerequisites

Before deploying this monitoring stack, ensure the following:

### Kubernetes Cluster

- A running **Kubernetes cluster** (e.g., [MicroK8s](https://microk8s.io), Minikube, k3s, or cloud-managed K8s)
- If using MicroK8s, enable required modules:

```bash
microk8s enable dns storage 
````

---

##  Directory Structure

```
prometheus-grafana/
├── alertmanager-configmap.yaml       # Alertmanager SMTP config
├── alertmanager.yaml                 # Alertmanager Deployment & Service
├── alert-rules-configmap.yaml        # Prometheus alerting rules
├── grafana.yaml                      # Grafana Deployment & Service
├── kube-state-metrics/               # Kube-State-Metrics deployment (cloned from GitHub)
├── namespace.yaml                    # Namespace for monitoring components
├── node-exporter.yaml                # Node Exporter DaemonSet
├── prometheus-configmap.yaml         # Prometheus scrape & rule config
├── prometheus.yaml                   # Prometheus Deployment & Service
├── README.md                         # This file
```

---

##  Installation Steps

1. **Clone or enter the directory:**

```bash
cd ~/prometheus-grafana
```

2. **Create the `monitoring` namespace:**

```bash
kubectl apply -f namespace.yaml
```

3. **Deploy `kube-state-metrics`:**

```bash
git clone https://github.com/kubernetes/kube-state-metrics
cd kube-state-metrics
git checkout v2.15.0
kubectl apply -k examples/standard
cd ..
```

4. **Apply all monitoring manifests:**

```bash
kubectl apply -f .
```

---

##  Accessing Services via Port Forwarding

If you are not using Ingress or a LoadBalancer:

| Component    | Local Port | Command                                                                         |
| ------------ | ---------- | ------------------------------------------------------------------------------- |
| Prometheus   | `9090`     | `kubectl port-forward -n monitoring svc/prometheus 9090:80 --address 0.0.0.0`   |
| Alertmanager | `9093`     | `kubectl port-forward -n monitoring svc/alertmanager 9093:80 --address 0.0.0.0` |
| Grafana      | `3000`     | `kubectl port-forward -n monitoring svc/grafana 3000:80 --address 0.0.0.0`      |

> Access the UIs:
>
> * Prometheus: `http://<VM_PUBLIC_IP>:9090`
> * Alertmanager: `http://<VM_PUBLIC_IP>:9093`
> * Grafana: `http://<VM_PUBLIC_IP>:3000`

---

##  Grafana Login

* **Username:** `admin`
* **Password:** `admin` (please change after first login)

---

##  Alertmanager SMTP Setup

Email alerts are configured via Gmail SMTP in `alertmanager-configmap.yaml`.

---

##  Prometheus Alerts

Prometheus alert rules are loaded from:

* `alert-rules-configmap.yaml` → mounted into Prometheus
* Example rules:

  * Pending Pods
  * Restarting containers
  * High CPU/Memory (can be added)

---

##  Prometheus Targets

Scraped targets (defined in `prometheus-configmap.yaml`):

* Prometheus (self)
* Node Exporter
* Kube-State-Metrics

You can confirm them at: `Status → Targets` in Prometheus UI.

---
Here’s a **section to add to your `README.md`** to document how to add **Prometheus as a data source in Grafana** and how to **import dashboards using Grafana Dashboard IDs**.

---

##  Add Prometheus as a Data Source in Grafana

After Grafana is deployed and accessible (e.g., via port-forward or MetalLB), follow these steps:

### Step 1: Log into Grafana

* Open your browser and go to:
  [http://localhost:3000](http://localhost:3000) *(or your VM public IP)*
* Login with default credentials:
  **Username**: `admin`
  **Password**: `admin`

>  You may be prompted to change the password after first login.

---

### Step 2: Add Prometheus Data Source

1. From the left sidebar, click **Gear (⚙️)** → **Data Sources**

2. Click **"Add data source"**

3. Choose **Prometheus**

4. Set the URL to:

   ```
   http://prometheus.monitoring.svc.cluster.local
   ```

5. Leave other settings default and click **Save & Test**

If successful, you’ll see **“Data source is working”**.

---

## Import Grafana Dashboards

Grafana provides a massive collection of community dashboards. You can import them by ID.

###  Go to Import Page

* Click the **"+"** icon on the left → **"Import"**

###  Use Dashboard IDs


| Dashboard Name                | Dashboard ID |
| ----------------------------- | ------------ |
| Node Exporter Full            | `1860`       |

### Set Data Source

* After entering a dashboard ID and clicking **"Load"**, select the Prometheus data source you added earlier.

---

### Optional: Persist Dashboards

If you want to automate dashboard provisioning via config files (JSON), you can create a `dashboards/` directory and mount it to Grafana’s container.

---


##  Cleanup

```bash
kubectl delete -f .
kubectl delete -k kube-state-metrics/examples/standard
```

---

##  Author

**Prayag Sangode**

---

```
