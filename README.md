<div align="center">

# 🏠 Kubernetes Homelab

### GitOps-managed Kubernetes Homelab powered by Proxmox, K3s, Talos Linux and Kubernetes

<!-- Replace with actual logos -->
![Proxmox](https://img.shields.io/badge/Proxmox-E57000?style=for-the-badge&logo=proxmox&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Talos](https://img.shields.io/badge/Talos_Linux-FF7300?style=for-the-badge&logoColor=white)
![K3s](https://img.shields.io/badge/K3s-FFC61C?style=for-the-badge&logo=k3s&logoColor=black)
![FluxCD](https://img.shields.io/badge/FluxCD-5468FF?style=for-the-badge&logo=flux&logoColor=white)

</div>

---

<img width="1920" height="1080" alt="homelab-diagram" src="https://github.com/user-attachments/assets/52bf19ec-2426-4d95-912d-d530a2dcc579" />


## 📋 Overview

The goal of this megaproject is to deploy a production-level Kubernetes cluster and migrate my existing stack of Docker services to Kubernetes.

The cluster needs to meet the following requirements to be considered feature complete:

| Requirement | Description |
|---|---|
| 💾 **Backups** | Configured for key services to local NAS and cloud storage (Backblaze / Wasabi S3) |
| 🛠️ **Ease of Management** | Simple to manage and maintain |
| 🖥️ **GPU Support** | Support GPUs for workloads that benefit from hardware acceleration (AI) |
| 🔁 **High Availability** | Key services survive one or two nodes dropping out / entering maintenance mode |
| 🗄️ **Resilient Storage** | Storage backend independent of pod lifecycle, available on worker nodes, survives a cluster crash |
| 📝 **Infrastructure as Code** | Cluster and components provisionable as code |
| 📊 **Monitoring** | Full observability stack — metrics, logs, alerts, dashboards for cluster health, resource utilisation, and GPU performance |
| 🌐 **Networking** | Tailscale, Cloudflare Tunnels, TLS/SSL certs |
| 🔐 **Security** | Encrypted secret management, network policies, image scanning |

---

## 🖥️ Infrastructure

### Underlying Hardware

| Node | OS | Role | Hardware |
|---|---|---|---|
| **Einherjar** | Cachy OS | Local Development & Testing | CPU: Ryzen 9 7950x3D (16C/32T)<br>RAM: 32GB DDR5<br>Storage: 2TB NVMe |
| **Hlidskjalf** | OPNsense 26 | Firewall/Router — DDNS, DNS, HAProxy, IDS/IPS, NTP ([details](https://www.ksefuke-labs.com/articles/enterprise-grade-firewall-router/)) | CPU: Celeron N5105 (4C/4T)<br>RAM: 16GB<br>Storage: 2 × 500GB NVMe |
| **Jotunheim** | PVE 9 | Proxmox — virtual NAS, Docker & self-hosted services ([details](https://www.ksefuke-labs.com/articles/state-of-homelab-2026/)) | CPU: i5-14500 (14C/20T)<br>RAM: 64GB DDR4<br>Storage: 2TB NVMe + 960GB SSD |
| **Huginn / Muninn** | PVE 9 | Proxmox — Talos Linux nodes | CPU: Celeron N100 (4C/4T)<br>RAM: 16GB<br>Storage: 1TB NVMe + 1.92TB SSD |

---

## ☸️ Kubernetes

My homelab consists of **3 clusters**: Development, Staging, and Production.

### 🧪 Development

Powered by **Rancher Desktop** on local desktop **Einherjar**. Applications and projects of interest are tested and deployed manually via manifest files stored in my ([Labs](https://github.com/ksefuke-labs/Labs)) Repo, which are then deployed to Staging.

### 🔬 Staging

Powered by **K3s** on a single-node Ubuntu 24.04 LTS Cloud-Init VM (4 Cores, 8GB RAM) on **Jotunheim**.

- Managed by **FluxCD**
- Listens to: `cluster/staging`, `apps/staging`, `infrastructure/staging`
- Rollback via Proxmox VM snapshots
- Less Painful to break

### 🚀 Production

Powered by **[Talos Linux](https://www.talos.dev)**, managed with **[FluxCD](https://fluxcd.io/)**.

- **3 Control Plane nodes** + **Worker Nodes** — 1 of each deployed across **Jotunheim**, **Huginn**, and **Muninn**
- Control plane load balanced via **HAProxy** on OPNsense router **Hlidskjalf**
- Will point to: `clusters/production`, `apps/production`, `infrastructure/production`

> ⚠️ Configuration is still in progress.

---

## 🧩 Core Components

| Category | Components |
|---|---|
| 📑 **Documentation | Detailed Documentation on Configuration, Storage, Networking, Security, observability and Applications |
| 📊 **Monitoring** | [Kube Prometheus Stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) |
| 🔧 **Maintenance** | [Renovate](https://github.com/renovatebot/renovate) for dependency updates |
| 🌐 **Networking** | Flannel (CNI), MetalLB, Traefik Ingress, Cloudflare Tunnels, cert-manager |
| 🔐 **Security** | [SOPS](https://github.com/mozilla/sops) + [AGE](https://github.com/FiloSottile/age) for encrypted secret management |
| 📦 **Services** | Audiobookshelf, Mealie, Linkding |
| 🗄️ **Storage** | [Longhorn](https://longhorn.io/) with NFS backups to 32TB OpenMediaVault VM on **Jotunheim** |

---

## 🗺️ Future Plans

- [ ] **🤖 AI** — Self-hosted AI leveraging iGPU acceleration (i5-14500 UHD 770 & N100 Intel UHD 24EU) across PVE servers
- [ ] **💾 Backups** — Properly configure backups for critical cluster components like ETCD
- [ ] **🗃️ Databases** — Migrate eligible services to PostgreSQL
- [ ] **🌐 Networking** — Deploy Cilium to replace Flannel and MetalLB
- [ ] **📊 Monitoring** — Additional Grafana dashboards, alerting rules, cross-server metrics
- [ ] **📦 Services** — Migrate remaining Docker services to the cluster
- [ ] **🔐 Security** — RBAC rollout, deploy Wazuh XDR/SIEM and CrowdSec, implement authentication
- [ ] **🖥️ VDI** — Deploy [Kasm Workspaces](https://www.kasmweb.com/)


