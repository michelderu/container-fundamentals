# Lab 3 — Podman CLI (Linux), rootless, and familiarity

## Objective

Mirror Lab 2 with **Podman**; optionally compare **rootful** vs **rootless** and `podman-docker` ergonomics.

## Prerequisites

- Linux with **Podman** installed (`podman version`).
- If testing rootless explicitly: logged in as a normal user **without** `sudo podman`.

---

## Part A — Version and configuration

```bash
podman version
podman info
```

Note **rootless** true/false in `podman info` output.

---

## Part B — Run a container

```bash
podman pull alpine:latest
podman run -d --name cf-lab3-sleep alpine sleep 300
podman ps --filter name=cf-lab3-sleep
```

Inspect init PID on **host** (Podman still maps to real PIDs):

```bash
PID=$(podman inspect -f '{{.State.Pid}}' cf-lab3-sleep)
echo "Host PID: $PID"
sudo readlink /proc/$PID/ns/pid
```

---

## Part C — Rootless observation (optional)

If you have two shells (root allowed vs purely rootless):

```bash
# rootless terminal
podman unshare cat /proc/self/uid_map
```

**Teaching point:** user namespaces remap UIDs — “root inside” is **not** host root.

---

## Part D — `podman-docker` (optional)

If your distro ships the compatibility shim:

```bash
# only if docker -> podman is installed
docker run --rm hello-world 2>/dev/null || true
```

Discuss with your instructor: scripts that assume **`docker.sock`** specifics may still break.

---

## Cleanup

```bash
podman rm -f cf-lab3-sleep
```

---

## Quiz

1. Does Podman require `dockerd`? Why or why not?
2. One benefit of rootless Podman for shared laptops?
