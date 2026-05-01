# Lab 4 — Compose: Docker Compose vs Podman Compose

## Objective

Run a **minimal two-service stack** (`web` + `api` style) using the **same** `compose.yaml` with **`docker compose`** and **`podman compose`** on separate passes (choose the engine your policy allows).

## Prerequisites

- **Docker:** `docker compose version` reports a plugin version  
  **or Podman:** `podman compose version` works on your Podman build.
- No other service bound to ports **8080** and **8081** (adjust in file if occupied).

---

## Part A — `compose.yaml`

Either copy **`examples/compose-lab/compose.yaml`** from this repo into `compose-lab/`, or create `compose-lab/compose.yaml` with this **minimal** stack (two published ports, no custom Nginx config):

```yaml
services:
  web:
    image: nginx:stable-alpine
    ports:
      - "8080:80"

  api:
    image: traefik/whoami:latest
    ports:
      - "8081:80"
```

**Teaching point:** Compose is wiring **services** and **ports**; inter-service HTTP routing would need extra config (Nginx `proxy_pass`, Traefik labels, etc.)—out of scope for this lab.

---

## Part B — Docker path

```bash
cd compose-lab
docker compose up -d
docker compose ps
curl -sS http://127.0.0.1:8080/ | head
curl -sS http://127.0.0.1:8081/   # whoami variant
docker compose logs --tail=20
docker compose down
```

---

## Part C — Podman path (same file)

```bash
podman compose up -d
podman compose ps
curl -sS http://127.0.0.1:8080/ | head
curl -sS http://127.0.0.1:8081/
podman compose down
```

If `podman compose` is unavailable, try your org’s pinned **`podman-compose`** invocation and record differences in your runbook.

---

## Part D — Compare with legacy `docker-compose` (optional)

If V1 is installed:

```bash
docker-compose --version
docker-compose up -d
docker-compose down
```

Document: **V1 vs V2** flag differences your team hit in real services.

---

## Deliverable

One paragraph: **which** compose entrypoint your org standardizes on and **one** pitfall you discovered (ports, SELinux labels, build cache, etc.).
