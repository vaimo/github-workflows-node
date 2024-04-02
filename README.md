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