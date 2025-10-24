# Community Solid Server (CSS) in an Azure App Service

## Pre-requisite

```bash
brew update && brew install azure-cli
```


An existing [App Service on Linux app](https://learn.microsoft.com/en-us/azure/app-service/).
An Azure Storage account.
An Azure file share and directory.


## Deploy ARM Template from local



```bash
az login

az group create --name 182764JH --location "UK South"

az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>

az deployment group create --resource-group 182764JH --template-file ./infrastructure/template.json





az deployment sub create --location <location> --template-file <path-to-template>

az deployment sub create --location "UK South" --template-file ./infrastructure/template.json



```

GitHub action authenticate for ARM Template deploy.


## Setup CI

App Service > Configuration > General settings > SCM Basic Auth Publishing > ON

App Service > Overview > Download publish profile

Github > settings > Secrets and variables > Actions > set

AZUREAPPSERVICE_PUBLISHPROFILE

Set

AZUREAPPSERVICE_NAME (using template name)
