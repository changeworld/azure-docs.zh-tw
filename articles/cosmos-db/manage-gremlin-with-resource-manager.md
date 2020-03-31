---
title: Azure 宇宙 DB Gremlin API 的資源管理器範本
description: 使用 Azure 資源管理器範本創建和配置 Azure 宇宙 DB Gremlin API。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: e4c18d93f07cb2143dcc5bf9c93b9ac7298d2f7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246743"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 資源管理器範本管理 Azure 宇宙 DB Gremlin API 資源

本文介紹如何使用 Azure 資源管理器範本執行不同的操作來自動管理 Azure Cosmos DB 帳戶、資料庫和容器。 本文僅對 Gremlin API 帳戶的示例，用於查找其他 API 類型帳戶的示例，請參閱：使用 Azure Cosmos DB 的[CASsandra](manage-cassandra-with-resource-manager.md)API、SQL、MongoDB、[表](manage-table-with-resource-manager.md)文章的 Azure 資源管理器範本。 [SQL](manage-sql-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>為 MongoDB 帳戶、資料庫和集合創建 Azure Cosmos DB API<a id="create-resource"></a>

使用 Azure 資源管理器範本創建 Azure Cosmos 資料庫資源。 此範本將為 Gremlin API 創建一個 Azure Cosmos 帳戶，該帳戶包含兩個圖形，在資料庫級別共用 400 RU/s 輸送量。 複製範本並部署如下圖所示，或訪問[Azure 快速入門庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/)並從 Azure 門戶進行部署。 您還可以將範本下載到本地電腦或創建新範本，並使用 參數`--template-file`指定本地路徑。

> [!NOTE]
> 帳戶名稱必須小寫，字元數必須為 44 或更少。
> 要更新 RU/s，請重新提交具有更新輸送量屬性值的範本。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>使用 Azure CLI 進行部署

要使用 Azure CLI 部署 Azure 資源管理器範本，**請複製**腳本並選擇 **"嘗試它**以打開 Azure 雲外殼"。 要粘貼腳本，請按右鍵 shell，然後選擇 **"粘貼**：

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

該`az cosmosdb show`命令顯示新創建的 Azure Cosmos 帳戶在預配後。 如果選擇使用本地安裝的 Azure CLI 版本而不是使用雲外殼，請參閱[Azure CLI](/cli/azure/)一文。

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)
- [Azure 宇宙資料庫資來源提供者架構](/azure/templates/microsoft.documentdb/allversions)
- [Azure 宇宙 DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [排除常見的 Azure 資源管理器部署錯誤](../azure-resource-manager/templates/common-deployment-errors.md)