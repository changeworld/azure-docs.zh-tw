---
title: 建立使用 SignalR 服務和 GitHub 驗證的 Web 應用程式
description: Azure CLI 指令碼範例 - 建立使用 SignalR 服務和 GitHub 驗證的 Web 應用程式
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ee9f0253f2ec27b4afbbb6c5dc1ff3f5c129fe8a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841796"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>建立使用 SignalR 服務和 GitHub 驗證的 Web 應用程式

這個範例指令碼會建立新的 Azure SignalR 服務資源，以供用來將即時內容更新推送給用戶端。 這個指令碼也會新增 Web 應用程式和 App Service 方案來裝載 ASP.NET Core Web 應用程式，以使用 SignalR Service。 Web 應用程式會透過「應用程式設定」來設定，以連線至新的 SignalR 服務資源，並使用 [GitHub 驗證](https://developer.github.com/v3/guides/basics-of-authentication/)來進行驗證。 Web 應用程式也會設定為使用本機 Git 存放庫部署來源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

此指令碼會使用適用於 Azure CLI 的 signalr  擴充功能。 在使用這個範例指令碼之前，請執行下列命令以安裝適用於 Azure CLI 2.0 的 signalr  擴充功能：

```azurecli-interactive
#!/bin/bash

#========================================================================
#=== Update these values based on your desired deployment username    ===
#=== and password.                                                    ===
#========================================================================
deploymentUser=<Replace with your desired username>
deploymentUserPassword=<Replace with your desired password>

#========================================================================
#=== Update these values based on your GitHub OAuth App registration. ===
#========================================================================
GitHubClientId=<Replace with your GitHub OAuth app Client ID>
GitHubClientSecret=<Replace with your GitHub OAuth app Client Secret>


# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "Azure:SignalR:ConnectionString=$primaryConnectionString" 

#Add app settings to use with GitHub authentication
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientId=$GitHubClientId" 
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientSecret=$GitHubClientSecret" 

# Add the desired deployment user name and password
az webapp deployment user set --user-name $deploymentUser --password $deploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $myWebAppName --resource-group $myResourceGroupName \
  --query [url] -o tsv
```

記下新資源群組所產生的實際名稱。 此名稱會顯示在輸出中。 當您想要刪除所有群組資源時，就會用到該資源群組名稱。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

下表中的每個命令都會連結至命令特定的文件。 此指令碼會使用下列命令：

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | 建立 Azure SignalR 服務資源。 |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | 列出應用程式在使用 SignalR 推送即時內容更新時，所會使用的金鑰。 |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | 建立用來裝載 Web 應用程式的 Azure App Service 方案。 |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | 使用 App Service 主控方案建立 Azure Web 應用程式。 |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | 新增 Web 應用程式的應用程式設定。 這些應用程式設定會用來儲存 SignalR 連接字串和 GitHub OAuth 應用程式祕密。 |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | 更新部署認證。 |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | 取得為了部署 Web 應用程式而要作為 git 存放庫端點複製與推送目標的 URL。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure SignalR 服務文件](../signalr-reference-cli.md)中找到其他的 Azure SignalR 服務 CLI 指令碼範例。
