# GitHub Actions & Azure Key Vault

A micro-demo showcasing secure secrets management in GitHub Actions using Azure Key Vault with OIDC/federated identity authentication. The demo walks through the hierarchy of GitHub secrets, establishes a passwordless connection to Azure via Workload Identity Federation, and retrieves secrets from Azure Key Vault within a CI/CD pipeline.

## Demo Architecture

```plaintext
┌─────────────────────────────────────────────────────────────┐
│  GitHub Actions Workflow                                     │
│                                                             │
│  1. Trigger on push/workflow_dispatch                        │
│  2. azure/login@v2 (OIDC federated credential)              │
│  3. azure/get-keyvault-secrets@v1 OR az CLI                 │
│  4. Use secrets in subsequent steps (masked in logs)         │
└──────────────────────────┬──────────────────────────────────┘
                           │ OIDC token exchange
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  Microsoft Entra ID                                         │
│                                                             │
│  - App Registration / Managed Identity                      │
│  - Federated Credential (trust GitHub OIDC issuer)          │
│  - Scoped to repo + branch/environment                      │
└──────────────────────────┬──────────────────────────────────┘
                           │ Access token (OAuth 2.0)
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  Azure Key Vault                                            │
│                                                             │
│  - RBAC: "Key Vault Secrets User" role on identity          │
│  - Secrets: demo-api-key, demo-connection-string            │
│  - Audit logging enabled (Diagnostic Settings)              │
└─────────────────────────────────────────────────────────────┘
```
