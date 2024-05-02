# GitHub Workflows for Magento 

This repository contains reusable workflows for Node projects.

# Usage

In order to utilize one of the workflows, it is necessary to include this configuration within the .yml file of your project. 

## Node - Yarn CI workflow

```yaml
jobs:
  
  # Name of the job
  ci-workflow:
    # Specify the workflow and it's version
    uses: vaimo/github-workflows-node/.github/workflows/ci.yml@main
    with:
      # Node version that will be used to run action.
      node-version: 20
      # Directories where junit report is located (separated by comma).
      # Optional - if not specified then uses */junit.xml as default
      junit-test-report-path: azure-functions/*/junit.xml,sdk/*/junit.xml
      # Name and where coverage report file is located (separated by comma). Applicable only if report is not located in coverage/coverage-summary.json folder.
      # Optional - if not specified then uses ./coverage/coverage-summary.json as default
      junit-test-coverage-configuration: |
        http-cvw-algolia, azure-functions/http-cvw-algolia/coverage/coverage-summary.json
        http-cvw-application-form, azure-functions/http-cvw-application-form/coverage/coverage-summary.json
    secrets:
      # If your build process requires environment variables, you can pass them here. Based on the string passed here, the action will create a .env file in the root of the project.
      # Optional - if not specified then .env file will not be created
      env-vars: KEY=${{ secrets.VALUE }},KEY_1=${{ secrets.VALUE_2 }}
```

## Node - Azure WebApp CD workflow

- This workflow is used to deploy a Docker image to an Azure Container App. 
- Before the build, the workflow fetches the environment variables from the Azure Key Vault and creates a .env file in the root of the project.
- The image is built and pushed using "az acr build" command.
- After successful push, the workflow updates the Azure Container App with the new image using "az containerapp update" command.

```yaml
jobs:
  # Name of the job
  cd-workflow:
    # Specify the workflow and it's version
    uses: vaimo/github-workflows-node/.github/workflows/azure-webapp-cd.yml@main
    with:
      # Azure Container Registry name
      # Required
      acr-name: acr-name
      # Azure Container Registry resource group name
      # Required
      shared-rg-name: shared-rg-name
      # Azure Container Registry environment subscription specific resource group name
      # Required
      rg-name: rg-name
      # Azure Container App name
      # Required
      jobs-container-name: jobs-container-name
      # Docker image tag
      # Required
      docker-image-tag: image-name:latest
      # Environment variables configuration (separated by comma)
      # BUNDLE_ANALYZE=Bundle--Analyze - will create a .env file with the key BUNDLE_ANALYZE and value Bundle--Analyze loaded from the Azure key-vault
      # Required
      env-vars: BUNDLE_ANALYZE=Bundle--Analyze
      # Azure Key Vault name
      # Required
      key-vault: key-vault
      # Run in github hosted runner or self-hosted runner
      # Optional - if not specified then uses 'ubuntu-latest' runner
      runner: self-hosted
    secrets:
      # Azure Tenant ID
      # Required
      azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      # Azure Client ID
      # Required
      azure-client-id: ${{ secrets.AZURE_CLIENT_ID_DEV }}
      # Azure Shared Subscription ID
      # Required
      azure-shared-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      # Azure Subscription ID
      # Required
      azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID_DEV }}
```

## Node - Azure IAC CD workflow

- This workflow is used to deploy multiple functions to corresponding Azure Functions. 
- Before the build, the workflow fetches the environment variables from the Azure Key Vault and creates a .env file in the function directory.
- Each function image is built and pushed using "az acr build" command.
- After each successful push, the workflow updates all the Azure Functions with the new image using "az functionapp config container set" command.

```yaml
jobs:
  # Name of the job
  cd-workflow:
    # Specify the workflow and it's version
    uses: vaimo/github-workflows-node/.github/workflows/azure-webapp-cd.yml@main
    with:
      # Directories where Cloud functions are located. Split by comma.
      # Required
      allowed-dirs: 'azure-functions'
      # Azure Container Registry resource group name
      # Required
      shared-rg-name: shared-rg-name
      # Azure Container Registry environment subscription specific resource group name
      # Required
      rg-name: rg-name
      # Docker image tags in a JSON format. Key is the function name and value is the tag.
      # Required
      docker-image-tags: '{"azure-functions/http-cvw-algolia": "function/integration:latest"}'
      # Azure function ids in a JSON format. Key is the function name and value is the function id.
      # Required
      function-ids: '{"azure-functions/http-cvw-algolia": "func-dev-web-jobs"}'
      # Environment variables configuration (separated by comma) per function.
      # BUNDLE_ANALYZE=Bundle--Analyze - will create a .env file with the key BUNDLE_ANALYZE and value Bundle--Analyze loaded from the Azure key-vault
      # Required
      env-vars: '{"azure-functions/http-cvw-algolia": "BUNDLE_ANALYZE=Bundle--Analyze"}'
      # Azure Key Vault name
      # Required
      key-vault: key-vault
      # Run in github hosted runner or self-hosted runner
      # Optional - if not specified then uses 'ubuntu-latest' runner
      runner: self-hosted
    secrets:
      # Azure Tenant ID
      # Required
      azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      # Azure Client ID
      # Required
      azure-client-id: ${{ secrets.AZURE_CLIENT_ID_DEV }}
      # Azure Shared Subscription ID
      # Required
      azure-shared-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      # Azure Subscription ID
      # Required
      azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID_DEV }}
```
