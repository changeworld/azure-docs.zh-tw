---
title: Azure CLI 指令碼範例 - 建立 Azure 應用程式組態存放區
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 指令碼來建立 Azure 應用程式組態存放區
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: lcozzens
ms.openlocfilehash: 667cf9545d87bd9277c59d066933710339b6c079
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77623074"
---
# <a name="create-an-azure-app-configuration-store"></a>建立 Azure 應用程式設定存放區

此範例指令碼會在新資源群組中建立新的 Azure 應用程式組態執行個體。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query hostName \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

記下新資源群組所產生的實際名稱。 當您想要刪除所有群組資源時，就會用到該資源群組名稱。

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立新的資源群組與應用程式組態存放區。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az appconfig create](/cli/azure/appconfig#az-appconfig-create) | 建立應用程式組態存放區資源。 |
| [az appconfig credential list](/cli/azure/appconfig/credential#az-appconfig-credential-list) | 列出應用程式組態存放區的存取金鑰。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式組態 CLI 範例](../cli-samples.md)中找到其他的應用程式組態 CLI 指令碼範例。
