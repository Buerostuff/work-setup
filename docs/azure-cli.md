---
layout: default
title: Azure CLI
---

# Azure CLI

[← Home](../)

---

## Installation

<details>
<summary><strong>macOS</strong></summary>

```bash
brew install azure-cli
```
</details>

<details>
<summary><strong>Linux (Ubuntu/Debian)</strong></summary>

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
</details>

<details>
<summary><strong>Windows</strong></summary>

```powershell
winget install Microsoft.AzureCLI
```
</details>

---

## Login

```bash
az login
az login --use-device-code    # for remote sessions
```

---

## Subscription

```bash
az account list -o table
az account set -s "<name-or-id>"
```

---

## Common Commands

```bash
az group list -o table
az vm list -o table
az aks list -o table
az aks get-credentials -g <rg> -n <cluster>
```
