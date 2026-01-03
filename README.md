# Homelab Kubernetes (GitOps)

This repository contains the GitOps definition of my homelab based on Kubernetes, managed by ArgoCD and deployed on Talos Linux.

The main goals of this projects are:
- Practice Kubernetes realistically
- Use good practices (GitOps, layers separation, security)
- Serve as a base for personal services (currently running mostly on Docker)
- Work in a production-like environment

---

## Tech Stack

- Kubernetes (Talos Linux)
- Cilium (CNI + Gateway API + L2 Announcements)
- ArgoCD (GitOps)
- NFS (Persistant Storage on my NAS)
- Sealed Secrets
- Pi-hole (local DNS)

---

## GitOps Workflow

The entire cluster state is defined in this repository.
- ArgoCD continuously watches this repo
- Any change is automatically reconciled into the cluster
- Manual changes using kubectl are avoided

The cluster always converges to the declared state.

---

## Access to Services

Services are exposed using Gateway API (Cilium) and local DNS:

Service	URL

ArgoCD	https://argocd.homelab.local

Plex	http://plex.homelab.local

Immich	https://immich.homelab.local

---

## Storage

- NFS backend shared from my NAS
- Dynamic provisioning via nfs-subdir-external-provisioner
- Dedicated PVCs per application (config / data / media)

--- 

## Security

- Secrets managed using Sealed Secrets
- No plaintext credentials stored in the repository
- Applications are isolated using namespaces

---

Backups automáticos (Velero)

Observabilidad (Hubble / Prometheus)

CI para validación de manifests
