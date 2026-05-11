# Lab 8 - Deploy with Helm on kind

## Objective

Use **Helm** to install, inspect, upgrade, and uninstall an application on a local **kind** cluster.

## Quick explainer: `docker run` vs `docker compose` vs `helm`

- `docker run`: run one container directly from an image.
- `docker compose`: run multiple related containers together on one Docker host.
- `helm`: install and manage packaged apps (charts/releases) on Kubernetes.

In this lab, Helm talks to the Kubernetes API on your kind cluster; it does not replace Docker, but operates one layer above it.

## Prerequisites

- `kind`, `kubectl`, and `helm` installed.
- A working cluster or permission to create one.
- Module references: [module 07 kind](../course/07-kubernetes-kind.md), [module 08 helm](../course/08-helm-basics.md), [module 09 setup](../course/09-setup-linux-macos-windows.md).

## Prerequisites check

```bash
kind version
kubectl version --client
helm version
```

---

## Part A - Create cluster (if needed)

```bash
kind create cluster --name cf-helm-lab
kubectl cluster-info --context kind-cf-helm-lab
kubectl get nodes
```

If you already have a suitable cluster, skip create and ensure your current context points to it.

---

## Part B - Add repo and inspect chart

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo bitnami/nginx
helm show chart bitnami/nginx
```

---

## Part C - Install release

```bash
helm install web bitnami/nginx --namespace web --create-namespace
helm list -n web
kubectl get all -n web
```

Wait for readiness:

```bash
kubectl wait --for=condition=Available deployment/web-nginx -n web --timeout=180s
```

---

## Part D - Validate via port-forward

```bash
kubectl -n web port-forward svc/web-nginx 18081:80 &
PF_PID=$!
sleep 2
curl -sS -I http://127.0.0.1:18081/ | sed -n '1p'
kill "$PF_PID"
```

---

## Part E - Upgrade and view history

```bash
helm upgrade web bitnami/nginx --namespace web --set service.type=ClusterIP
helm history web -n web
helm status web -n web
```

Optional rollback:

```bash
helm rollback web 1 -n web
```

---

## Part F - Cleanup

```bash
helm uninstall web -n web
kubectl delete namespace web
kind delete cluster --name cf-helm-lab
```

---

## Deliverable

Write one sentence explaining what Helm tracked as a **release** that plain `kubectl apply` does not track in the same way.
