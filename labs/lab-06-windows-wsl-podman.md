# Lab 6 — Windows: WSL2 + Podman (Docker Desktop optional)

## Objective

Run Linux containers **inside WSL2** using **Podman**, matching how many enterprises comply with desktop policy while preserving **production-like** Linux behavior.

## Prerequisites

- Windows 10/11 with **WSL2** installed and a supported Linux distro (**Ubuntu**, **Fedora Remix**, enterprise golden image).
- Virtualization enabled in BIOS/UEFI.
- **Docker Desktop not required** for this lab path.

Inside WSL2 (open your distro terminal):

---

## Part A — Confirm WSL2

```bash
uname -a
# On Windows PowerShell side (optional): wsl.exe -l -v
```

**Teaching point:** the **Linux kernel** here is Microsoft’s WSL2 kernel module + your distro userspace; containers use **namespaces/cgroups inside this Linux**.

---

## Part B — Install Podman (Ubuntu example)

Ubuntu versions differ; generic pattern:

```bash
sudo apt-get update
sudo apt-get install -y podman
podman --version
```

For **Fedora** in WSL: `sudo dnf install podman`.

Use your organization’s mirror/apt source if mandated.

---

## Part C — Run rootless hello

```bash
podman run --rm quay.io/podman/hello
```

List storage:

```bash
podman images
```

---

## Part D — Compose under WSL2

Install a Compose path your org approves (examples only):

- `podman compose` if available in your Podman build, **or**
- pinned **`podman-compose`** from internal PyPI proxy.

Reuse the `compose.yaml` from [lab 4](lab-04-compose.md).

```bash
mkdir -p ~/compose-lab && cd ~/compose-lab
# add compose.yaml
podman compose up -d
curl -sS http://127.0.0.1:8080/ | head
curl -sS http://127.0.0.1:8081/
podman compose down
```

**Port note:** `localhost` from **Windows** browsers may or may not reach WSL2 published ports depending on **Windows version** and **WSL networking mode**; `curl` from **inside** WSL2 is the reliable first check. Document your org’s pattern (e.g. `localhostForwarding`, mirrored mode, VS Code port forwarding).

---

## Optional branch — Docker Engine inside WSL2 (no Desktop)

Some teams install **Docker Engine** packages **inside WSL2** instead of Docker Desktop. If allowed:

```bash
# Example only — follow distro-specific Docker docs approved internally
sudo apt-get install -y docker.io
sudo usermod -aG docker "$USER"
```

Log out/in, then `docker run hello-world`.

**Governance:** this still grants strong privileges via the `docker` group—treat like production access.

---

## Cleanup

```bash
podman system prune -f
```

(Optional) remove test images only: `podman rmi …`

---

## Enterprise reflection

Summarize **one** Desktop-free stack your org uses on Windows: **WSL2 + Podman**, **remote Linux**, **Colima-equivalent unavailable on Win**, etc.
