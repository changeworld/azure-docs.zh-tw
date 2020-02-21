---
title: 適用于 MongoDB 的 Azure Cosmos DB API Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定適用于 MongoDB 的 Azure Cosmos DB API。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 65838c36cadba580c578677185683e27b57b1e27
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525493"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本來管理 Azure Cosmos DB MongoDB API 資源

本文說明如何使用 Azure Resource Manager 範本來執行不同的作業，以自動化管理您的 Azure Cosmos DB 帳戶、資料庫和容器。 本文僅提供適用于 MongoDB Azure Cosmos DB API 的範例，若要尋找其他 API 類型帳戶的範例，請參閱：搭配使用 Azure Resource Manager 範本與 Azure Cosmos DB 的 API 來進行[Cassandra](manage-cassandra-with-resource-manager.md)、 [Gremlin](manage-gremlin-with-resource-manager.md)、 [SQL](manage-sql-with-resource-manager.md)、[資料表](manage-table-with-resource-manager.md)發行項。

## 建立適用于 MongoDB 帳戶、資料庫和集合的 Azure Cosmos DB API<a id="create-resource"></a>

使用 Azure Resource Manager 範本建立 Azure Cosmos DB 資源。 此範本會建立適用于 MongoDB API 的 Azure Cosmos 帳戶，其中包含兩個在資料庫層級共用 400 RU/秒輸送量的集合。 複製範本並如下所示部署，或造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/)，並從 Azure 入口網站進行部署。 您也可以將範本下載到本機電腦，或建立新的範本，並使用 `--template-file` 參數指定本機路徑。

> [!NOTE]
> 帳戶名稱必須是小寫、44或較少的字元。
> 若要更新 RU/秒，請重新提交具有已更新輸送量屬性值的範本。
>
> 目前，您只能使用 PowerShell、CLI 和 Resource Manager 範本，建立3.2 版本（也就是使用 `*.documents.azure.com`的格式端點） Azure Cosmos DB 適用于 MongoDB 帳戶之 API 的帳戶。 若要建立3.6 版的帳戶，請改用 Azure 入口網站。

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-the-azure-cli"></a>透過 Azure CLI 部署

若要使用 Azure CLI 部署 Azure Resource Manager 範本，請**複製**腳本，然後選取 [**試試看**] 以開啟 Azure Cloud Shell。 若要貼上腳本，請以滑鼠右鍵按一下 shell，然後選取 [**貼**上]：

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 命令會在布建之後顯示新建立的 Azure Cosmos 帳戶。 如果您選擇使用本機安裝的 Azure CLI 版本，而不是使用 Cloud Shell，請參閱[Azure CLI](/cli/azure/)文章。

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)
- [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)