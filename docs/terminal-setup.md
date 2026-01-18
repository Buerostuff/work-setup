# Terminal Setup

## Install Starship

=== "macOS"

    ```bash
    brew install starship
    ```

=== "Linux"

    ```bash
    curl -sS https://starship.rs/install.sh | sh
    ```

=== "Windows"

    ```powershell
    # winget
    winget install Starship.Starship

    # scoop
    scoop install starship
    ```

---

## Shell Init

=== "macOS / Linux (zsh)"

    Add to `~/.zshrc`:

    ```bash
    eval "$(starship init zsh)"
    ```

=== "macOS / Linux (bash)"

    Add to `~/.bashrc`:

    ```bash
    eval "$(starship init bash)"
    ```

=== "Windows (PowerShell)"

    Add to `$PROFILE`:

    ```powershell
    Invoke-Expression (&starship init powershell)
    ```

---

## Minimal Config

Create `~/.config/starship.toml`:

```toml
add_newline = true

[character]
success_symbol = "[➜](green)"
error_symbol = "[✗](red)"

[directory]
truncation_length = 3

[git_branch]
symbol = " "
```
