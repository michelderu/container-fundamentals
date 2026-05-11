# Lab 7 — Local Kubernetes cluster with kind

## Objective

Provision a **one-node kind cluster**, run a trivial workload with **kubectl**, then tear the cluster down. Observe that **Kubernetes nodes appear as ordinary containers** in your container engine where supported.

## Prerequisites

- **Docker** or **Podman** working on **Linux**, **macOS** (often **Docker + Colima** with `docker context use colima`), or **WSL2** with the engine installed **inside** the distro ([module 09 setup](../course/09-setup-linux-macos-windows.md)).
- **kind** and **kubectl** installed ([module 07 kind concepts](../course/07-kubernetes-kind.md)).

## Prerequisites check

```bash
docker version   # or: podman version (and use Podman-backed kind only if supported on your combo)
kubectl version --client
kind version
```

On macOS with Colima:

```bash
colima status
docker context ls
docker context use colima   # if Colima should be active
```

---

## Part A — Cluster create

```bash
kind create cluster --name cf-kind-lab

kubectl cluster-info --context kind-cf-kind-lab
kubectl get nodes -o wide
```

Expected: **one** node in `Ready` state.

---

## Part B — List “node” containers (optional introspection)

**Docker:**

```bash
docker ps --filter label=io.x-k8s.kind.role --format '{{.Names}}\t{{.Image}}'
```

**Podman:**

```bash
podman ps --filter label=io.x-k8s.kind.role --format '{{.Names}}\t{{.Image}}'
```

Teaching point: these containers are kind’s **`control-plane` / worker** stubs; workloads you schedule with kubectl run **inside** that node’s cgroup/namespace hierarchy.

---

## Part C — Deploy and hit the Service (recommended: port-forward)

Imperative `kubectl`, no manifests required:

```bash
kubectl --context kind-cf-kind-lab create deployment nginx-kind-lab --image=nginx:stable-alpine
kubectl --context kind-cf-kind-lab expose deployment nginx-kind-lab --port=80 --type=ClusterIP
kubectl --context kind-cf-kind-lab wait --for=condition=Available deployment/nginx-kind-lab --timeout=120s

kubectl --context kind-cf-kind-lab port-forward svc/nginx-kind-lab 18080:80 &
PF_PID=$!
sleep 2
curl -sS -I http://127.0.0.1:18080/ | head -n1
kill "$PF_PID"
```

Cleanup for Part C:

```bash
kubectl --context kind-cf-kind-lab delete deployment nginx-kind-lab
kubectl --context kind-cf-kind-lab delete svc nginx-kind-lab
```

**Optional (NodePort on host):** some learners want **`curl localhost:30080`**. Apply [examples/kind/nginx-nodeport-demo.yaml](../examples/kind/nginx-nodeport-demo.yaml); kind usually maps **`30080` → localhost** under Docker/Desktop/Colima, but Podman/rootless setups may differ ([kind ingress / port-mapping docs](https://kind.sigs.k8s.io/docs/user/quick-start/)).

---

## Part C-alt — Inspect a Pod versus a kind node container

```bash
kubectl --context kind-cf-kind-lab get pods -A -o wide
docker ps   # Docker engine; Podman-backed kind: podman ps
```

---

## Part D — Cleanup

```bash
kind delete cluster --name cf-kind-lab
```

---

## Deliverable

One sentence: **where** kind’s node containers appeared in `docker ps` or `podman ps` compared to application **Pods**.
