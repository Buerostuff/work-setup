# Docker Setup

## Installation

=== "macOS"

    ```bash
    brew install --cask docker
    ```

    Or download from [docker.com](https://www.docker.com/products/docker-desktop)

=== "Linux"

    ```bash
    curl -fsSL https://get.docker.com | sh
    sudo usermod -aG docker $USER
    # logout and login
    ```

=== "Windows"

    ```powershell
    # winget
    winget install Docker.DockerDesktop

    # scoop
    scoop install docker
    ```

    Or download from [docker.com](https://www.docker.com/products/docker-desktop)

---

## Custom Install Path (Windows)

Don't double-click the installer. Use command line to specify custom paths.

=== "PowerShell"

    ```powershell
    # Run as Administrator
    cd ~\Downloads
    Start-Process -Wait -FilePath ".\Docker Desktop Installer.exe" -ArgumentList "install", "-accept-license", "--installation-dir=D:\Docker", "--wsl-default-data-root=D:\Docker\images"
    ```

=== "CMD"

    ```cmd
    :: Run as Administrator
    cd %USERPROFILE%\Downloads
    start /w "" "Docker Desktop Installer.exe" install -accept-license --installation-dir=D:\Docker --wsl-default-data-root=D:\Docker\images
    ```

**Flags explained:**

| Flag | Purpose |
|------|---------|
| `-accept-license` | Auto-accept license agreement |
| `--installation-dir=D:\Docker` | Install Docker app to D:\Docker |
| `--wsl-default-data-root=D:\Docker\images` | Store WSL data & images on D: |

!!! note
    Without `--wsl-default-data-root`, images still go to `%HOME%\AppData\Local\Docker`

---

## Custom Data Path (Post-Install)

=== "macOS"

    Docker Desktop → Settings → Resources → Disk image location

=== "Linux"

    Edit `/etc/docker/daemon.json`:

    ```json
    {
      "data-root": "/path/to/docker"
    }
    ```

    ```bash
    sudo systemctl restart docker
    ```

=== "Windows"

    Docker Desktop → Settings → Resources → Disk image location

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
