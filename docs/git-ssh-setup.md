# Git SSH Setup (Port 443)

## Generate SSH Key

=== "macOS / Linux"

    ```bash
    ssh-keygen -t ed25519 -C "your_email@example.com"
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_ed25519
    ```

=== "Windows"

    ```powershell
    ssh-keygen -t ed25519 -C "your_email@example.com"
    Get-Service ssh-agent | Set-Service -StartupType Automatic
    Start-Service ssh-agent
    ssh-add $env:USERPROFILE\.ssh\id_ed25519
    ```

---

## SSH Config (Port 443)

=== "macOS / Linux"

    Edit `~/.ssh/config`:

    ```
    Host github.com
        HostName ssh.github.com
        Port 443
        User git
        IdentityFile ~/.ssh/id_ed25519
    ```

=== "Windows"

    Edit `C:\Users\<username>\.ssh\config`:

    ```
    Host github.com
        HostName ssh.github.com
        Port 443
        User git
        IdentityFile C:\Users\<username>\.ssh\id_ed25519
    ```

---

## Copy Public Key

=== "macOS"

    ```bash
    pbcopy < ~/.ssh/id_ed25519.pub
    ```

=== "Linux"

    ```bash
    cat ~/.ssh/id_ed25519.pub
    ```

=== "Windows"

    ```powershell
    Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard
    ```

---

## Test Connection

```bash
ssh -T git@github.com
```
