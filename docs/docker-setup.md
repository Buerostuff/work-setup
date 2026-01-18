---
layout: default
title: Docker Setup
---

# Docker Setup

[← Home](../)

---

## Installation

<details>
<summary><strong>macOS</strong></summary>

```bash
brew install --cask docker
```

Or download from [docker.com](https://www.docker.com/products/docker-desktop)
</details>

<details>
<summary><strong>Linux (Ubuntu/Debian)</strong></summary>

```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
# logout and login
```
</details>

<details>
<summary><strong>Windows</strong></summary>

```powershell
winget install Docker.DockerDesktop
```

Or download from [docker.com](https://www.docker.com/products/docker-desktop)
</details>

---

## Custom Data Path

<details>
<summary><strong>macOS</strong></summary>

Docker Desktop → Settings → Resources → Disk image location
</details>

<details>
<summary><strong>Linux</strong></summary>

Edit `/etc/docker/daemon.json`:

```json
{
  "data-root": "/path/to/docker"
}
```

```bash
sudo systemctl restart docker
```
</details>

<details>
<summary><strong>Windows</strong></summary>

Docker Desktop → Settings → Resources → Disk image location
</details>

---

## Common Commands

```bash
docker ps                     # running containers
docker ps -a                  # all containers
docker images                 # list images
docker system prune -a        # cleanup
```

---

## Compose

```bash
docker compose up -d
docker compose down
docker compose logs -f
```
