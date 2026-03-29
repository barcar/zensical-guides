```yaml
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
      output_location: ""
      skip_app_build: true
      skip_api_build: true
      azure_static_web_apps_api_token: $(deployment_token)
```
