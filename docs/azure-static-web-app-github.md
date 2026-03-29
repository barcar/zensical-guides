---
icon: lucide/cloud-upload
---

# Azure Static Web Apps with GitHub

This guide is for [Azure Static Web Apps](https://learn.microsoft.com/azure/static-web-apps/overview) when your source is in **GitHub** and deployment runs through **GitHub Actions** that Azure connects to the repository. It does not cover Azure DevOps or other Git hosts.

For **Azure Repos** and **Azure Pipelines**, see [Azure Static Web Apps with Azure DevOps](azure-static-web-app-devops.md).

The [official “Publish your site” page](https://zensical.org/docs/publish-your-site/) links here from its **Other** section. For GitHub Pages or GitLab Pages, use that documentation instead.

**SWA** also supports custom domains, authentication (including Microsoft Entra ID), and more - those topics are not covered here.

Zensical’s default output folder is `site/`. The Static Web Apps deploy step should treat that folder as **already-built** static files: set `skip_app_build` to **true**, point `app_location` at `site`, leave `output_location` empty, and set `skip_api_build` to **true** when you have no API. See Microsoft’s [skip building front-end app](https://learn.microsoft.com/azure/static-web-apps/build-configuration#skip-building-front-end-app) documentation.

Official Microsoft walkthroughs: [Static Web Apps quickstart (GitHub)](https://learn.microsoft.com/azure/static-web-apps/get-started-portal?pivots=github) and [(Azure DevOps)](https://learn.microsoft.com/azure/static-web-apps/get-started-portal?pivots=azure-devops); [build configuration for GitHub Actions](https://learn.microsoft.com/azure/static-web-apps/build-configuration?tabs=github-actions) and [for Azure Pipelines](https://learn.microsoft.com/azure/static-web-apps/build-configuration?tabs=azure-devops).

## Before you start

If you already publish this repo with **GitHub Pages** (for example `.github/workflows/docs.yml`), avoid two hosts deploying the same site: delete that workflow or restrict it to `workflow_dispatch` until you are done moving to Static Web Apps:

```yaml
on:
  workflow_dispatch:
```

## Create the Static Web Apps resource in Azure Portal

### Open Azure Portal and create a Static Web App

Open the [Azure Portal](https://portal.azure.com/), search for **Static Web Apps**, and choose **Create**.

### Basics: subscription, resource group, name, and plan

On **Basics**, set:

- **Subscription:** Your Azure subscription.
- **Resource group:** Create a new group or select an existing one.
- **Name:** A unique name for the app (it appears in the default hostname `https://UNIQUE_NAME.azurestaticapps.net`).
- **Plan type:** **Free** or **Standard** (the free tier is enough for a typical public documentation site).

### Connect your Git host

On the same **Basics** step, connect your Git host to the Static Web App:

- **Source:** **GitHub**. Sign in when prompted, authorize **AzureAppService** if asked, then choose **Organization**, **Repository**, and **Branch** (for example `main`).
- **Build Details:** Choose **Custom** in **Build Presets** (or the closest generic option if **Custom** is not listed). The path fields are only used to generate the first workflow - you will edit that file for Zensical.

### Finish creating the resource

Complete the wizard:

Then select **Review + create**, wait for validation, and choose **Create**. When deployment finishes, open **Go to resource** and note the default site URL: `https://UNIQUE_NAME.azurestaticapps.net`.

## Configure continuous deployment for Zensical

### Expect the first CI run to fail

The first run may **fail** until Zensical is wired into the generated definition - this is normal.

### Pull the commit and open the generated CI file

**Pull** the commit Azure pushed to your repository, then open the generated workflow:

**GitHub:** `.github/workflows/azure-static-web-apps-UNIQUE_NAME.yml`

### Add the Zensical build and deploy

Edit the workflow Azure added to align with the blue highlighted lines below - add the Python and Zensical steps, and modify the SWA deployment configuration. Note that the tokens in the **violet** highlighted sections will be named for your SWA’s `UNIQUE_NAME` and should not be changed.

```yaml {#swa-gha-azure-static-workflow hl_lines="23-29 35 40-44 56" title=".github/workflows/azure-static-web-apps-UNIQUE_NAME.yml"}
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v6
        with:
          submodules: true
          lfs: false

      - uses: actions/setup-python@v6
        with:
          python-version: 3.x

      - run: pip install zensical

      - run: zensical build --clean

      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_UNIQUE_NAME }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # GitHub integrations (e.g. PR comments)
          action: upload
          ###### Repository/Build Configurations - These values can be configured to match your app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "site"
          api_location: ""
          skip_app_build: true
          skip_api_build: true
          output_location: ""
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_UNIQUE_NAME }}
          action: close
```

If you use a `requirements.txt` file (as in this repository), replace the `pip install zensical` line or step with `pip install -r requirements.txt`.

### Commit, push, and verify

**Commit and push.** When the workflow succeeds, open `https://UNIQUE_NAME.azurestaticapps.net` to confirm the site.

For more details, see [Azure Static Web Apps documentation](https://learn.microsoft.com/azure/static-web-apps/overview).
