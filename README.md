# Container fundamentals — course overview

This repository contains **course modules** and **hands-on labs** that explain how containers work on **Linux**, **macOS**, and **Windows**, and how common tools map to those platforms.

## Who this is for

- Engineers who need a clear mental model of **where** containers run on each OS.
- Teams in **regulated or policy-constrained enterprises** where **Docker Desktop** is not approved or licensed.
- Anyone comparing **Docker Engine**, **Podman**, **Colima**, **Compose** variants, and desktop products.

## How to use this material

1. Read the course modules in order (`course/01` → `07`); use [course/07-setup-linux-macos-windows.md](course/07-setup-linux-macos-windows.md) when you are ready to install tools.
2. Complete labs on a machine that matches the lab’s platform notes (Linux labs on Linux, and so on).
3. Adapt commands to your organization’s approved tooling; the enterprise module lists common patterns.

## Contents

| Path | Topic |
|------|--------|
| [course/01-core-concepts.md](course/01-core-concepts.md) | Namespaces, cgroups, images, OCI — what “a container” actually is |
| [course/02-linux-runtimes.md](course/02-linux-runtimes.md) | Docker Engine, Podman CLI, rootless, sockets |
| [course/03-macos-windows.md](course/03-macos-windows.md) | Why a VM (or WSL2) is required; Docker Desktop vs alternatives |
| [course/04-compose.md](course/04-compose.md) | `docker compose`, `docker-compose`, Podman Compose |
| [course/05-desktop-products.md](course/05-desktop-products.md) | Docker Desktop vs Podman Desktop vs Colima (roles, not slogans) |
| [course/06-enterprise-constraints.md](course/06-enterprise-constraints.md) | Licensing, policy, air-gapped, practical stack choices |
| [course/07-setup-linux-macos-windows.md](course/07-setup-linux-macos-windows.md) | Concrete install/setup for Linux, macOS, and Windows |

## Lab index

| Lab | Focus |
|-----|--------|
| [labs/lab-01-linux-namespaces.md](labs/lab-01-linux-namespaces.md) | Observe isolation on Linux (no Docker required) |
| [labs/lab-02-docker-engine-linux.md](labs/lab-02-docker-engine-linux.md) | Engine, images, containers on Linux |
| [labs/lab-03-podman-linux.md](labs/lab-03-podman-linux.md) | Podman CLI, rootless, `podman-docker` familiarity |
| [labs/lab-04-compose.md](labs/lab-04-compose.md) | Same Compose file with Docker and Podman ([example YAML](examples/compose-lab/compose.yaml)) |
| [labs/lab-05-mac-colima.md](labs/lab-05-mac-colima.md) | Colima as a Desktop-free path on macOS |
| [labs/lab-06-windows-wsl-podman.md](labs/lab-06-windows-wsl-podman.md) | WSL2 + Podman or Engine patterns on Windows |

## Conventions

- **Copy-paste blocks** are shell commands unless marked otherwise.
- **“Linux container”** means a workload using the **Linux kernel** userland ABI; macOS and Windows hosts run those workloads **inside** a Linux environment (VM, WSL2, etc.), not on the native host kernel.

---

*These materials are educational; validate all choices against your organization’s security, licensing, and support policies.*
