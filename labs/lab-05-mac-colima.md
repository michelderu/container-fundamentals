# Lab 5 — macOS without Docker Desktop: Colima

## Objective

Run **Docker CLI** workloads using **Colima’s** Linux VM as the backing engine—useful when **Docker Desktop is not permitted** but `docker`-compatible tooling is desired.

## Prerequisites

- **macOS** with [Homebrew](https://brew.sh/) or another approved install path.
- Hardware virtualization available (Intel VT-x / Apple Silicon Hypervisor.framework).
- **Docker CLI** (`docker`) installed locally — Colima exposes a socket/context the CLI consumes.

Install examples (adjust to corp mirror policies):

```bash
brew install colima docker
```

Some teams install **Docker Buildx** separately; follow your baseline image.

---

## Part A — Start Colima

```bash
colima version
colima start
```

Inspect runtime:

```bash
docker context ls
docker info
```

**Expectation:** `docker info` reports a Linux kernel in the backend; daemon is reachable via Colima’s forwarded endpoint (implementation detail may vary by Colima release).

---

## Part B — Run a container

```bash
docker run --rm hello-world
docker run -d --name cf-lab5-sleep alpine sleep 600
docker ps --filter name=cf-lab5-sleep
```

---

## Part C — Compose (optional)

Reuse [lab-04 `compose.yaml`](lab-04-compose.md) inside a temp directory:

```bash
mkdir -p ~/tmp/compose-lab && cd ~/tmp/compose-lab
# paste compose.yaml from lab 4
docker compose up -d
curl -sS http://127.0.0.1:8080/ | head
curl -sS http://127.0.0.1:8081/
docker compose down
```

---

## Part D — File sharing friction (discussion)

Bind-mount a project from `~/Projects/...` into a container and run `ls -la`.

**Teaching point:** cross-VM mounts can exhibit **latency** and **permission** quirks; compare with cloning the repo **inside** the Linux VM tooling if your platform team documents that pattern.

---

## Cleanup

```bash
docker rm -f cf-lab5-sleep
colima stop
```

Optional: `colima delete` to reclaim disk (destructive).

---

## Alternative cue

Teams standardized on **Podman** rather than Docker CLI should try **Podman Machine** / **Podman Desktop** alongside this lab—not necessarily Colima’s default profile.
