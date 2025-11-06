# Hosting the Community Solid Server (CSS) in an Azure App Service

This repository is setup to facilitate the deployment of the [Community Solid Server](https://github.com/CommunitySolidServer/CommunitySolidServer) in Azure.

## Pre-requisite

An [Azure Subscription](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account).


## Deploy infrastructure from CLI

You can deploy a minimal infrastructure to host the CSS using the [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/) and the arm template in `./infrastructure/template.json` ([install Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)).

You'll need to choose a [resource group](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview#resource-groups) name and a [location](https://learn.microsoft.com/en-us/azure/reliability/regions-list#azure-regions-list-1).

```zsh
CSS_INFRASTRUCTURE_RESOURCE_GROUP_NAME=css
CSS_INFRASTRUCTURE_LOCATION="UK South"
```

To keep track of the app service name which is useful for retrieving its publish profile and setting up the GiHub Action secrets, you can set a deployment time:

```zsh
CSS_INFRASTRUCTURE_DEPLOYMENT_TIME=$(date '+%Y%m%d%H%M%S')
```

Then login (and select the desired Azure Subscription), create the resource group if it doesn't exist and deploy the arm template.

```zsh
az login

az group create --name $CSS_INFRASTRUCTURE_RESOURCE_GROUP_NAME --location $CSS_INFRASTRUCTURE_LOCATION

az deployment group create --resource-group $CSS_INFRASTRUCTURE_RESOURCE_GROUP_NAME --template-file ./infrastructure/template.json --parameters deployment_time=$CSS_INFRASTRUCTURE_DEPLOYMENT_TIME
```


## Deploy CSS in an existing App service using a GitHub action

You'll need a copy of this repository with two [actions secrets set](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets): `AZUREAPPSERVICE_NAME` & `AZUREAPPSERVICE_PUBLISHPROFILE`.

You can use the [GitHub CLI](https://cli.github.com/manual/) to set secrets and trigger the workflow ([install GitHub CLI](https://cli.github.com/)).

The app service name is the one you set deploying the infrastructure:

```zsh
echo ${CSS_INFRASTRUCTURE_DEPLOYMENT_TIME}app
```

Retrieve the app service's Publish Profile:

```zsh
az webapp deployment list-publishing-profiles --resource-group $CSS_INFRASTRUCTURE_RESOURCE_GROUP_NAME --name ${CSS_INFRASTRUCTURE_DEPLOYMENT_TIME}app --xml
```

Then just trigger the Deploy CSS action from GitHub.

See also: [Deploy Node.js to Azure App Services](https://docs.github.com/en/actions/how-tos/deploy/deploy-to-third-party-platforms/nodejs-to-azure-app-service).


## Setting up CSS

This repository is setup to run CSS with bare minimum Solid standard functionality using [ACP](https://solidproject.org/TR/acp) for authorization.

Considering you will start from an empty Azure blob storage, in its initial state, CSS will find no authorization and all requests will return a `401` Unauthorized HTTP Error.

You can bootstrap your environment by uploading the adequate access control policies and creating the default folder and resource structure in your Azure Blob storage file share.

For example, you could upload `data/.acr` to make the Solid server fully open to everyone (Read, Write, Control) and then let your app bootstrap permissions.


## Note

The first boot of the CSS will take a little time (about 3 minutes). If you choose a [paid App Service Plan](https://azure.microsoft.com/en-gb/pricing/details/app-service/linux/), your CSS instance could always be on and not have to go through booting time often.

Currently, the App Service Free Plan is limited to 60 CPU minutes / day.


## Acknowledgements

A huge thanks goes to imec research groups at Ghent University for the [development of the Community Solid Server](https://solidlab.be/community-solid-server/).


## License

This work is dual-licensed under MIT and Apache 2.0.
You can choose between one of them if you use this work.

`SPDX-License-Identifier: MIT OR Apache-2.0`
