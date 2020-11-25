---
title: 使用 Azure CLI 建立與 App Service 運作的 SignalR Service
description: 使用 Azure CLI 建立與 App Service 運作的 SignalR Service。 了解 Azure SignalR Service 的所有 CLI 命令。
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 673a3583b1ec80fb3ad61ec35a1786a59939f6e8
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563319"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>使用 App Service 建立 SignalR 服務

這個範例指令碼會建立新的 Azure SignalR 服務資源，以供用來將即時內容更新推送給用戶端。 這個指令碼也會新增 Web 應用程式和 App Service 方案來裝載 ASP.NET Core Web 應用程式，以使用 SignalR Service。 Web 應用程式會透過名為 AzureSignalRConnectionString  的「應用程式設定」來設定，以連線至新的 SignalR 服務資源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教學課程需要 2.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="sample-script"></a>範例指令碼

此指令碼會使用適用於 Azure CLI 的 signalr 擴充功能。 在使用這個範例指令碼之前，請執行下列命令以安裝適用於 Azure CLI 2.0 的 signalr 擴充功能：

```azurecli-interactive
#!/bin/bash

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
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
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
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | 新增 Web 應用程式的應用程式設定。 此應用程式設定會用來儲存 SignalR 連接字串。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure SignalR 服務文件](../signalr-reference-cli.md)中找到其他的 Azure SignalR 服務 CLI 指令碼範例。
