# Go Setup

Installation and development workflow for Go, including Air for live reload.

## Installation

=== "Windows"

    ```powershell
    # winget
    winget install GoLang.Go

    # scoop
    scoop install go
    ```

=== "macOS"

    ```bash
    brew install go
    ```

---

## Air (live reload)

[Air](https://github.com/air-verse/air) provides live reload for Go apps during development. Follow these steps to install and use it.

### 1. Install Air

=== "Windows / PowerShell"

    ```powershell
    go install github.com/air-verse/air@latest
    ```

=== "macOS / Linux"

    ```bash
    go install github.com/air-verse/air@latest
    ```

    Or on macOS via Homebrew:

    ```bash
    brew install go-air
    ```

### 2. Add Go bin to PATH (Windows)

The `air` binary is installed to `$(go env GOPATH)/bin` (typically `%USERPROFILE%\go\bin`). Add it to your session PATH:

```powershell
$env:Path += ";" + (go env GOPATH) + "\bin"
```

> **_NOTE:_** This only affects the current session. To make it permanent, add the same line to your PowerShell profile, or add `%USERPROFILE%\go\bin` to your user PATH in **Environment Variables** (System Properties → Advanced → Environment Variables).

### 3. Verify PATH

Confirm that Go's `bin` directory is on your PATH:

```powershell
$env:Path -split ";"
```

Check that `%USERPROFILE%\go\bin` (or the path from `(go env GOPATH)\bin`) appears in the list.

### 4. Initialize Air in your backend

Go to the root folder of your backend (where `go.mod` and your Go code live) and create the Air config:

```powershell
cd C:\path\to\your\backend
air init
```

This creates a `.air.toml` file in the current directory.

### 5. Run Air

From the same backend root folder, run:

```powershell
air
```

Air will build and run your app and reload when you change your code.

> **_TIP:_** You can customize build and run behavior by editing `.air.toml`. See the [air-verse/air](https://github.com/air-verse/air) README and `air_example.toml` in the repo for options.
