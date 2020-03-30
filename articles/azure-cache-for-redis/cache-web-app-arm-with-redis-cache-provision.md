---
title: 佈建 Web 應用程式與 Azure Redis 快取
description: 使用 Azure Resource Manager 範本來部署 Web 應用程式與「Azure Redis 快取」。
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 11c854491ab030394eb61964979cb04a5a4b489b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433384"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>使用範本來建立 Web 應用程式加 Azure Redis 快取

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在本主題中，您將了解如何建立 Azure Resource Manager 範本，以部署 Azure Web 應用程式與「Azure Redis 快取」。 您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

如需關於建立範本的詳細資訊，請參閱 [編寫 Azure 資源管理員範本](../azure-resource-manager/templates/template-syntax.md)。 若要深入了解快取資源類型的 JSON 語法和屬性，請參閱 [Microsoft.Cache 資源類型](/azure/templates/microsoft.cache/allversions)。

如需完整的範本，請參閱 [Web 應用程式與 Azure Redis 快取範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json) \(英文\)。

## <a name="what-you-will-deploy"></a>部署內容
在此範本中，您將部署：

* Azure Web 應用程式
* Azure Cache for Redis

若要自動執行部署，請按一下下列按鈕：

[![部署到 Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>要指定的參數
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>名稱的變數
這個範本會使用變數來建構資源的名稱。 它會使用 [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) 函式，根據資源群組識別碼來建構值。

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>要部署的資源
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
建立與 Web 應用程式搭配使用的「Azure Redis 快取」。 快取的名稱指定於 **cacheName** 變數中。

範本會在資源群組的相同位置建立快取。

    {
      "name": "[variables('cacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [ ],
      "tags": {
        "displayName": "cache"
      },
      "properties": {
        "sku": {
          "name": "[parameters('cacheSKUName')]",
          "family": "[parameters('cacheSKUFamily')]",
          "capacity": "[parameters('cacheSKUCapacity')]"
        }
      }
    }


### <a name="web-app"></a>Web 應用程式
使用 **webSiteName** 變數中所指定的名稱來建立 Web 應用程式。

請注意，Web 應用程式上會設定可讓它與「Azure Redis 快取」搭配運作的應用程式設定屬性。 這些應用設置根據部署期間提供的值動態創建。

    {
      "apiVersion": "2015-08-01",
      "name": "[variables('webSiteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
        "displayName": "Website"
      },
      "properties": {
        "name": "[variables('webSiteName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "appsettings",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
            "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
          ],
          "properties": {
            "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>執行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
