---
title: Azure Cosmos DB 的 Resource Manager 範本 Cassandra API
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB Cassandra API。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251891"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本管理 Azure Cosmos DB Cassandra API 資源

本文說明如何使用 Azure Resource Manager 範本來執行不同的作業，以自動化管理您的 Azure Cosmos DB 帳戶、資料庫和容器。 本文僅包含 SQL API 帳戶的範例，若要尋找其他 API 類型帳戶的範例，請參閱：使用 Azure Resource Manager 範本搭配 Azure Cosmos DB 適用于[SQL](manage-sql-with-resource-manager.md)的 api、 [Gremlin](manage-gremlin-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)、[資料表](manage-table-with-resource-manager.md)發行項。

## 建立 Azure Cosmos 帳戶、keyspace 和資料表<a id="create-resource"></a>

使用 Azure Resource Manager 範本建立 Azure Cosmos DB 資源。 此範本會為 Cassandra API 建立 Azure Cosmos 帳戶，其中包含兩個數據表，在 keyspace 層級共用 400 RU/秒的輸送量。 複製範本並如下所示部署，或造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/)，並從 Azure 入口網站進行部署。 您也可以將範本下載到本機電腦，或建立新的範本，並使用 `--template-file` 參數指定本機路徑。

> [!NOTE]
> 帳戶名稱必須是小寫、44或較少的字元。
> 若要更新 RU/秒，請重新提交具有已更新輸送量屬性值的範本。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>使用 Azure CLI 部署

若要使用 Azure CLI 部署 Azure Resource Manager 範本，請**複製**腳本，然後選取 [**試試看**] 以開啟 Azure Cloud Shell。 若要貼上腳本，請以滑鼠右鍵按一下 shell，然後選取 [**貼**上]：

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 命令會在布建之後顯示新建立的 Azure Cosmos 帳戶。 如果您選擇使用本機安裝的 Azure CLI 版本，而不是使用 Cloud Shell，請參閱[Azure CLI](/cli/azure/)文章。


## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)
- [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)
