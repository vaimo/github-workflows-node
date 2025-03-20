# GitHub Workflows for Node.js

This repository contains reusable workflows for Node.js projects.

**Author:** Patryk Waluś (patryk.walus@vaimo.com)

## Supported Versions
- **v1**  
  Initial version of the workflows.

---

## Node.js - Yarn CI Workflow

This workflow sets up a Node.js environment, runs ESLint, and executes tests.

### Usage

```yaml
jobs:
  ci-workflow:
    uses: vaimo/github-workflows-node/.github/workflows/ci.yml@v4
    with:
      # Node.js version to install (Required)
      node-version: 20

      # Directories where JUnit reports are located (Optional, defaults to */junit.xml)
      junit-test-report-path: azure-functions/*/junit.xml,sdk/*/junit.xml

      # Custom coverage report configuration (Optional, defaults to ./coverage/coverage-summary.json)
      junit-test-coverage-configuration: |
        http-cvw-algolia, azure-functions/http-cvw-algolia/coverage/coverage-summary.json
        http-cvw-application-form, azure-functions/http-cvw-application-form/coverage/coverage-summary.json

      # Run full ESLint analysis or only modified files (Optional, defaults to full)
      full-analysis: true

      # Enable or disable custom NPM registry access (Optional, defaults to false)
      enable-npm-registry: true
    secrets:
      # Environment variables for the build process (Optional)
      env-vars: |
        KEY=${{ secrets.KEY }}
        KEY_2=${{ secrets.KEY_2 }}
        
      # Custom NPM registry URL (e.g., https://registry.npmjs.org or private registry)
      npm-registry-url: ${{ secrets.npm-registry-url }}
  
      # NPM package scope (e.g., @yourcompany) (Required if enable-npm-registry is true)
      npm-registry-scope: ${{ secrets.npm-registry-scope }}
      
      # Custom NPM registry authentication details (Required if enable-npm-registry is true)
      npm-registry-email: ${{ secrets.NPM_REGISTRY_EMAIL }}
      npm-registry-user: ${{ secrets.NPM_REGISTRY_USER }}
      npm-registry-password: ${{ secrets.NPM_REGISTRY_PASSWORD }}
```

## Node - Azure WebApp CD workflow

- This workflow is used to deploy a Docker image to an Azure Container App. 
- Before the build, the workflow fetches the environment variables from GitHub secrets and creates a .env file in the root of the project.
- The image is built and pushed using "az acr build" command.
- After successful push, the workflow updates the Azure Container App with the new image using "az containerapp update" command.

```yaml
jobs:
  cd-workflow:
    uses: vaimo/github-workflows-node/.github/workflows/azure-webapp-cd.yml@v4
    with:
      # Azure container registry (Required)
      acr-name: acr-name

      # Resource group names (Required)
      shared-rg-name: shared-rg-name
      rg-name: rg-name
      
      # Azure Container App name (Required)
      container-name: container-name

      # Azure environment name (Required)
      environment: environment

      # Runner type (Optional, defaults to 'ubuntu-latest')
      runner: self-hosted
    secrets:
      # Environment variables (Required)
      env-vars: |
        KEY=${{ secrets.VALUE }}
        KEY_2=${{ secrets.VALUE_2 }}

      # Azure authentication credentials (Required)
      azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      azure-client-id: ${{ secrets.AZURE_CLIENT_ID_DEV }}
      azure-shared-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID_DEV }}
```

## Node - Azure IAC CD workflow

- This workflow is used to deploy multiple functions to corresponding Azure Functions. 
- Before the build, the workflow fetches the environment variables from GitHub secrets and creates a .env file in the function directory.
- Each function image is built and pushed using "az acr build" command.
- After each successful push, the workflow updates all the Azure Functions with the new image using "az functionapp config container set" command.

```yaml
jobs:
  cd-workflow:
    uses: vaimo/github-workflows-node/.github/workflows/azure-iac-cd.yml@v4
    with:
      # Directories where cloud functions are located (Required, split by comma)
      allowed-dirs: 'azure-functions'

      # Azure container registry (Required)
      acr-name: acr-name

      # Resource group names (Required)
      shared-rg-name: shared-rg-name
      rg-name: rg-name

      # Runner type (Optional, defaults to 'ubuntu-latest')
      runner: self-hosted
    secrets:
      # Environment variables per function (Required)
      env-vars: |
        {
          "azure-functions/http-cvw-algolia": "KEY=${{ secrets.VALUE }},KEY_1=${{ secrets.VALUE_2 }}",
          "azure-functions/http-cvw-application-form": "KEY=${{ secrets.VALUE }},KEY_1=${{ secrets.VALUE_2 }}"
        }

      # Azure authentication credentials (Required)
      azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      azure-client-id: ${{ secrets.AZURE_CLIENT_ID_DEV }}
      azure-shared-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID_DEV }}

```
