
# Kubernetes Monitoring Stack Setup (Prometheus + Grafana + Alertmanager + Node Exporter)

This repository contains Kubernetes manifests to deploy a basic monitoring stack on a MicroK8s cluster. The following components are included:

- **Prometheus** – Metrics collection
- **Grafana** – Metrics visualization
- **Alertmanager** – Alerting engine
- **Node Exporter** – Node-level metrics
- **ConfigMaps** – Prometheus & Alertmanager configuration
- **Namespace** – All components are deployed under the `monitoring` namespace

---

## 📁 Directory Structure

```

monitoring-manifests/
├── alertmanager-configmap.yaml      # Alertmanager configuration (SMTP, routing, receivers)
├── alertmanager.yaml                # Alertmanager Deployment and Service
├── grafana.yaml                     # Grafana Deployment and Service
├── namespace.yaml                   # Namespace definition for monitoring
├── node-exporter.yaml               # Node Exporter DaemonSet
├── prometheus-configmap.yaml        # Prometheus scrape config
├── prometheus.yaml                  # Prometheus Deployment and Service

````

---

## 🚀 Installation Steps

1. **Clone the repo (or move into the directory):**

```bash
cd ~/monitoring-manifests
````

2. **Create the namespace:**

```bash
kubectl apply -f namespace.yaml
```

3. **Apply all the manifests:**

```bash
kubectl apply -f .
```

---

## 🌐 Accessing Services via Port Forwarding

Since Ingress or MetalLB is not used, you can expose services using port forwarding:

| Component    | Local Port | Command                                                                         |
| ------------ | ---------- | ------------------------------------------------------------------------------- |
| Prometheus   | `9090`     | `kubectl port-forward -n monitoring svc/prometheus 9090:80 --address 0.0.0.0`   |
| Alertmanager | `9093`     | `kubectl port-forward -n monitoring svc/alertmanager 9093:80 --address 0.0.0.0` |
| Grafana      | `3000`     | `kubectl port-forward -n monitoring svc/grafana 3000:80 --address 0.0.0.0`      |

> You can then access these UIs from your browser:
>
> * Prometheus: [http://\<VM\_PUBLIC\_IP>:9090](http://<VM_PUBLIC_IP>:9090)
> * Alertmanager: [http://\<VM\_PUBLIC\_IP>:9093](http://<VM_PUBLIC_IP>:9093)
> * Grafana: [http://\<VM\_PUBLIC\_IP>:3000](http://<VM_PUBLIC_IP>:3000)

---

## 🔐 Grafana Login

* **Username:** `admin`
* **Password:** `admin` (change after first login)

---

## 📬 Alertmanager SMTP

Alertmanager is configured to send email alerts via Gmail. Check `alertmanager-configmap.yaml` for SMTP settings.

---

## ✅ Next Steps

* Add Grafana dashboards
* Import Prometheus as a data source in Grafana
* Add alert rules
* Mount persistent volumes (if needed)

---

## 🧹 Cleanup

```bash
kubectl delete -f .
```

---

## 🧑‍💻 Author

**Prayag Sangode**

---

```
