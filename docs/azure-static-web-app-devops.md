---
icon: lucide/cloud-upload
---

# Azure Static Web Apps with Azure DevOps

This guide is for [Azure Static Web Apps](https://learn.microsoft.com/azure/static-web-apps/overview) when your source is in **Azure Repos** and deployment runs through **Azure Pipelines**, using the `azure-pipelines.yml` Azure usually adds at the repository root. It does not cover GitHub Actions or other Git hosts.

For **GitHub** and **GitHub Actions**, see [Azure Static Web Apps with GitHub](azure-static-web-app-github.md).

The [official “Publish your site” page](https://zensical.org/docs/publish-your-site/) links here from its **Other** section. For GitHub Pages or GitLab Pages, use that documentation instead.

**SWA** also supports custom domains, authentication (including Microsoft Entra ID), and more - those topics are not covered here.

Zensical’s default output folder is `site/`. The Static Web Apps deploy step should treat that folder as **already-built** static files: set `skip_app_build` to **true**, point `app_location` at `site`, leave `output_location` empty, and set `skip_api_build` to **true** when you have no API. See Microsoft’s [skip building front-end app](https://learn.microsoft.com/azure/static-web-apps/build-configuration#skip-building-front-end-app) documentation.

Official Microsoft walkthroughs: [Static Web Apps quickstart (GitHub)](https://learn.microsoft.com/azure/static-web-apps/get-started-portal?pivots=github) and [(Azure DevOps)](https://learn.microsoft.com/azure/static-web-apps/get-started-portal?pivots=azure-devops); [build configuration for GitHub Actions](https://learn.microsoft.com/azure/static-web-apps/build-configuration?tabs=github-actions) and [for Azure Pipelines](https://learn.microsoft.com/azure/static-web-apps/build-configuration?tabs=azure-devops).

## Before you start

Use a branch Azure can push to when it links the Static Web App. If **Repos → Branches → … → Branch policies** block those commits, the portal flow can fail - see Microsoft’s guidance on [branch policies](https://learn.microsoft.com/azure/devops/repos/git/branch-policies) and the [Azure DevOps quickstart](https://learn.microsoft.com/azure/static-web-apps/get-started-portal?pivots=azure-devops).

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

- **Source:** **Azure DevOps**. Choose **Organization**, **Project**, **Repository**, and **Branch** (for example `main`).
- **Build Details:** In **Build Presets**, choose **Custom** ([Microsoft’s quickstart](https://learn.microsoft.com/azure/static-web-apps/get-started-portal?pivots=azure-devops) uses the same). The app and output paths you enter here are only what Azure uses to generate the first pipeline - you will edit **`azure-pipelines.yml`** for Zensical anyway.

### Finish creating the resource

Complete the wizard:

Then select **Review + create**, wait for validation, and choose **Create**. When deployment finishes, open **Go to resource** and note the default site URL: `https://UNIQUE_NAME.azurestaticapps.net`.

## Configure continuous deployment for Zensical

### Expect the first CI run to fail

The first run may **fail** until Zensical is wired into the generated definition - this is normal.

### Pull the commit and open the generated CI file

**Pull** the commit Azure pushed to your repository, then open the generated pipeline:

**Azure DevOps:** `azure-pipelines.yml` at the repository root

### Add the Zensical build and deploy

Edit **`azure-pipelines.yml`** - add the Python and Zensical steps after checkout, then align the deploy task with the sample below.

```yaml {#swa-ado-azure-static-workflow hl_lines="11-20 24-27" title="azure-pipelines.yml"}
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - checkout: self
    submodules: true

  - task: UsePythonVersion@0
    inputs:
      versionSpec: "3.x"
      addToPath: true

  - script: pip install zensical
    displayName: Install Zensical

  - script: zensical build --clean
    displayName: Build site

  - task: AzureStaticWebApp@0
    inputs:
      app_location: "site"
      api_location: ""
      skip_app_build: true
      skip_api_build: true
      output_location: ""
      azure_static_web_apps_api_token: $(deployment_token)
```

If you use a `requirements.txt` file (as in this repository), replace the `pip install zensical` line or step with `pip install -r requirements.txt`.

### Commit, push, and verify

**Commit and push.** When the pipeline succeeds, open `https://UNIQUE_NAME.azurestaticapps.net` to confirm the site.

For more details, see [Azure Static Web Apps documentation](https://learn.microsoft.com/azure/static-web-apps/overview).
