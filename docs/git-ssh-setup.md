# Git SSH Setup

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

## Add SSH Key to GitHub

1. Go to [GitHub SSH Settings](https://github.com/settings/keys)
2. Click **New SSH key**
3. Give it a title (e.g., "Work Laptop")
4. Paste your public key
5. Click **Add SSH key**

> **_NOTE:_** See GitHub's official guide: [Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

---

## SSH Config (Port 443)

### Why Port 443?

Many corporate firewalls block port 22 (default SSH port). GitHub allows SSH connections over port 443 (HTTPS port) which is usually open.

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

## Test Connection

```bash
ssh -T git@github.com
```

Expected output:

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Multiple Git Profiles

If you have multiple GitHub accounts (e.g., personal and work), you can configure separate SSH keys and profiles.

### Step 1: Generate Separate Keys

```bash
# Personal account
ssh-keygen -t ed25519 -C "personal@email.com" -f ~/.ssh/id_ed25519_personal

# Work account
ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work
```

### Step 2: Add Keys to SSH Agent

=== "macOS / Linux"

    ```bash
    ssh-add ~/.ssh/id_ed25519_personal
    ssh-add ~/.ssh/id_ed25519_work
    ```

=== "Windows"

    ```powershell
    ssh-add $env:USERPROFILE\.ssh\id_ed25519_personal
    ssh-add $env:USERPROFILE\.ssh\id_ed25519_work
    ```

### Step 3: Configure SSH Config

Edit `~/.ssh/config` (or `C:\Users\<username>\.ssh\config` on Windows):

```
# Personal GitHub
Host github.com
    HostName ssh.github.com
    Port 443
    User git
    IdentityFile ~/.ssh/id_ed25519_personal

# Work GitHub (use custom host alias)
Host github_work
    HostName ssh.github.com
    Port 443
    User git
    IdentityFile ~/.ssh/id_ed25519_work
```

### Step 4: Add Keys to GitHub Accounts

Add each public key to its respective GitHub account:

- Personal: Add `id_ed25519_personal.pub` to your personal GitHub
- Work: Add `id_ed25519_work.pub` to your work GitHub

### Step 5: Clone Using Profile

```bash
# Personal (default)
git clone git@github.com:personal-user/repo.git

# Work (use custom host)
git clone git@github_work:work-org/repo.git
```

### Step 6: Configure Git User Per Repository

After cloning, set the correct user for each repo:

```bash
cd /path/to/work/repo
git config user.name "Your Work Name"
git config user.email "work@company.com"
```

> **_TIP:_** For automatic user config based on folder, see [Conditional Git Config](https://git-scm.com/docs/git-config#_conditional_includes).

---

## Troubleshooting

### Permission denied (publickey)

```bash
# Check which key is being used
ssh -vT git@github.com

# Verify key is added to agent
ssh-add -l
```

### Connection timed out

Your firewall may be blocking port 22. Use port 443 config above.

### Multiple profiles not working

Make sure you're using the correct host alias in your git remote:

```bash
# Check current remote
git remote -v

# Update to use correct profile
git remote set-url origin git@github_work:org/repo.git
```
