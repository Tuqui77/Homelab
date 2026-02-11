
# Kubernetes Homelab (GitOps)

![Kubernetes](https://img.shields.io/badge/kubernetes-v1.34.1-326CE5?logo=kubernetes&logoColor=white) ![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?logo=argo&logoColor=white) ![Talos](https://img.shields.io/badge/OS-Talos_Linux-FF6C2C?logo=linux&logoColor=white)

This repository contains the GitOps definition of my homelab based on Kubernetes, managed by ArgoCD and deployed on Talos Linux.

## Goals

- Practice Kubernetes realistically
- Use good practices (GitOps, layers separation, security)
- Serve as a base for personal services
- Work in a production-like environment

---

## Architecture

### Infrastructure

- **Hypervisor**: Proxmox VE
- **Host Hardware**: Ryzen 5 5600G, 32GB DDR4, 480GB SSD
- **Kubernetes Cluster**:
    - 1x Control Plane (8GB RAM)
    - 2x Worker Nodes (8GB RAM each)
- **OS**: Talos Linux

### Network & Storage

- **NFS**: Persistent storage backed by NAS
- **DNS**: Pi-hole (primary) + AdGuard Home (backup)
- **Domain**: tomasmerino.com (Cloudflare DNS)
- **TLS**: Let's Encrypt via cert-manager

Currently everything (cluster, NAS and my desktop PC) is working directly from the ISP router's switch. Lately it's been struggling a little with big file transfers, even crashing the internet connection. I have to get a dedicated switch as soon as possible.

---

## Applications

- **ArgoCD** - GitOps management
- **Immich** - Photo management
- **Plex** - Media server

    Problem: when using an HTTPRoute to access the service through the gateway, Plex detected the connection as remote access instead of local network traffic. This caused the streaming to require Plex Pass.

    Solution: modified the Deployment to use hostNetwork, allowing Plex to see the real client IP and treat traffic as local. The downside is that the namespace has to be privileged to allow it.
  
- **n8n** - Workflow automation
- **Paperless-ngx** - Document management
- **Homepage** - Dashboard
- **Linkding** - Bookmark manager
- **LubeLogger** - Vehicle maintenance tracker
- **Booklore** - Book management
- **AdGuard Home** - Backup DNS server

---

## Tech Stack

- **Kubernetes**: v1.34.1 (Talos Linux)
- **CNI**: Cilium (with Gateway API + L2 Announcements)
- **GitOps**: ArgoCD
- **Storage**: NFS (nfs-subdir-external-provisioner)
- **Secrets**: Sealed Secrets
- **Ingress**: Gateway API (Cilium) + HTTPRoutes
- **DNS**:
    - **Pi-Hole**: Primary DNS server, running on my NAS
    - **AdGuard Home**: Secondary DNS server running on Kubernetes

---

## GitOps Workflow

The entire cluster state is defined in this repository.

- ArgoCD continuously watches this repo
- Any change is automatically reconciled into the cluster
- Manual changes using `kubectl` are avoided

**The cluster always converges to the declared state.**

---

## Access to Services

## Access & Networking

**Internal Access**:
- Services exposed via **Gateway API** (Cilium) with **L2 announcements**
- Public DNS via **Cloudflare** (tomasmerino.com)
- TLS certificates automatically provisioned via **cert-manager**

**Remote Access**:
- **Tailscale** for secure remote connectivity (Subnet router + exit node running in Proxmox host since I had a lot of troubles with the connector in Kubernetes)

All exposed services use HTTPS with Let's Encrypt certificates.  

---

## Storage

- NFS backend shared from NAS
- Dynamic provisioning via `nfs-subdir-external-provisioner`
- Dedicated PVCs per application (config / data / media)

---

## Security

- Secrets managed using **Sealed Secrets**
- No plaintext credentials stored in the repository
- Applications isolated using **namespaces**
- TLS certificates automatically provisioned and renewed

