# UIT-GO GitOps Kubernetes Manifests

This repository contains the Kubernetes manifests for deploying the UIT-GO microservices platform using GitOps principles. It is designed for use with ArgoCD.

## Structure

- `base/` - All Kubernetes manifests for each service and infrastructure component.
- `argo-apps/` - ArgoCD Application manifests for bootstrapping this environment.

## Usage

1. **Push this repository to your Git provider (e.g., GitHub).**
2. **Connect ArgoCD to this repository.**
3. **Apply the ArgoCD Application manifest in `argo-apps/` to your cluster.**
4. **ArgoCD will sync and deploy all services and infrastructure.**

## Namespace
All resources are deployed into the `uit-go` namespace. Make sure this namespace exists or is created by the manifests.

## Secrets
Sensitive values (passwords, etc.) are stored as Kubernetes Secrets with placeholder values. Update these before production use.

---

Generated: 2025-11-27
