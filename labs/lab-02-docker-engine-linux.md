# Lab 2 — Docker Engine on Linux

## Objective

Pull an OCI image, run an **ephemeral** container, inspect ** namespaces** / **cgroups** from the host (read-only introspection).

## Prerequisites

- Linux with **Docker Engine** installed (`docker version` works).
- Your user can run `docker` (either root or **docker group** — understand security implications).
- Optional: root or `sudo` for host-side `/proc` inspection.

---

## Part A — Sanity check

```bash
docker version
docker info
```

Confirm **Server** section is populated (daemon reachable).

---

## Part B — Run and introspect

```bash
docker pull alpine:latest
CID=$(docker run -d --name cf-lab2-sleep alpine sleep 300)
echo "Container ID: $CID"
docker ps --filter name=cf-lab2-sleep
```

Find the container’s **init PID** on the host:

```bash
PID=$(docker inspect -f '{{.State.Pid}}' cf-lab2-sleep)
echo "Host PID: $PID"
```

Namespace links (requires permission to read `/proc`):

```bash
sudo ls -l /proc/$PID/ns/
```

Compare to your shell:

```bash
readlink /proc/self/ns/pid
sudo readlink /proc/$PID/ns/pid
```

**Expectation:** PID namespace inode differs between host shell and container init.

Optional cgroup hint (paths vary by driver/cgroup version):

```bash
cat /proc/$PID/cgroup
```

---

## Part C — Network isolation

```bash
docker run --rm alpine ip addr show | head
ip addr show | head
```

**Teaching point:** container has its own **network namespace** (default bridge path) unless you use `--network host`.

---

## Cleanup

```bash
docker rm -f cf-lab2-sleep
```

---

## Enterprise note

If your org **blocks** daemon-based Docker on workstations, redo this lab on an **approved** Linux VM or CI runner with the same objectives.
