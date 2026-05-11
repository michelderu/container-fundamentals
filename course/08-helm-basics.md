# Module 8 - Helm basics on local Kubernetes

## Learning outcomes

You can explain what **Helm** manages, add and inspect chart repositories, install and upgrade a release on a local cluster, and remove that release cleanly.

---

## 1. What Helm is

**[Helm](https://helm.sh/)** is a package manager for Kubernetes. A Helm **chart** bundles Kubernetes manifests and values into a reusable install unit.

Key terms:

- **Chart**: package template for Kubernetes resources.
- **Release**: one installed instance of a chart in a cluster.
- **Values**: configuration overrides passed at install/upgrade time.

---

## 2. Why Helm after kind

After `kind` + `kubectl` are working, Helm gives you a realistic workflow for:

- Installing common apps quickly.
- Repeating installs with pinned chart versions.
- Tracking what changed between releases.

This mirrors how many teams manage dev and test workloads.

---

## 3. Prerequisites

- A running cluster (for this course, usually `kind`).
- `kubectl` configured for that cluster.
- `helm` installed ([module 9 setup](09-setup-linux-macos-windows.md#7-helm-setup-after-kind)).

Quick checks:

```bash
kubectl version --client
kubectl get nodes
helm version
```

---

## 4. Core workflow

```bash
# Add and update a chart repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Search available charts
helm search repo nginx

# Install a release
helm install web bitnami/nginx --namespace web --create-namespace

# Inspect result
helm list -A
kubectl get all -n web

# Upgrade with a value override
helm upgrade web bitnami/nginx --namespace web --set service.type=ClusterIP

# Roll back if needed
helm rollback web 1 -n web

# Remove release
helm uninstall web -n web
kubectl delete namespace web
```

---

## 5. Helm and kubectl roles

| Task | Typical command |
|------|-----------------|
| Package install/upgrade lifecycle | `helm install`, `helm upgrade`, `helm rollback` |
| Low-level object inspection/debug | `kubectl get`, `kubectl describe`, `kubectl logs` |
| Day-2 chart values management | `helm get values`, values files in Git |

Use Helm for lifecycle and repeatability, and kubectl for diagnostics.

---

## 6. Enterprise cautions

- Pin chart versions (`--version`) instead of always using latest.
- Mirror chart repos internally when policy requires.
- Review chart defaults (images, RBAC, persistence) before production-like use.

---

## 7. Related lab

- [lab-08-helm-on-kind.md](../labs/lab-08-helm-on-kind.md)

---

## 8. Check your understanding

1. What is the difference between a chart and a release?
2. Why should teams pin chart versions in regulated environments?
3. When should you use `kubectl` even if the app was installed with Helm?
