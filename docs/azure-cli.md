# Azure CLI

## Installation

=== "macOS"

    ```bash
    brew install azure-cli
    ```

=== "Linux"

    ```bash
    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
    ```

=== "Windows"

    ```powershell
    # winget
    winget install Microsoft.AzureCLI

    # scoop
    scoop install azure-cli
    ```

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

## Resource Group

```bash
az group list -o table
az resource list -g <rg> -o table
```

---

## Container Registry (ACR)

```bash
az acr list -o table
az acr login -n <acr-name>
```

---

## Key Vault

```bash
az keyvault list -o table
az keyvault secret list --vault-name <kv-name> -o table
az keyvault secret show --vault-name <kv-name> -n <secret-name> --query value -o tsv
```

---

## AKS

```bash
az aks list -o table
az aks get-credentials -g <rg> -n <cluster>
```
