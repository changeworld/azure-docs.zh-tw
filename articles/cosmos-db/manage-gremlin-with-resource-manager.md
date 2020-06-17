---
title: 適用於 Azure Cosmos DB Gremlin API 的 Resource Manager 範本
description: 使用 Azure Resource Manager 範本建立和設定 Azure Cosmos DB Gremlin API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 0f807213d68e2731e784198f8ce968cdea9b3957
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684807"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本管理 Azure Cosmos DB Gremlin API 資源

在本文中，您會了解如何利用 Azure Resource Manager 範本來部署和管理 Azure Cosmos DB 帳戶、資料庫和圖形。

本文僅提供 Gremlin API 帳戶的範例，若要尋找其他 API 類型帳戶的範例，請參閱：搭配使用 Azure Resource Manager 範本與適用於 [Cassandra](manage-cassandra-with-resource-manager.md)、[SQL](manage-sql-with-resource-manager.md)、[MongoDB](manage-mongodb-with-resource-manager.md)、[資料表](manage-table-with-resource-manager.md)的 Azure Cosmos DB API 文章。

> [!IMPORTANT]
>
> * 帳戶名稱限制為 44 個字元，全部小寫。
> * 若要變更輸送量值，請使用已更新的 RU/秒重新部署範本。
> * 您在新增或移除 Azure Cosmos 帳戶的位置時，無法同時修改其他屬性。 這些作業必須個別執行。

若要建立下列任何 Azure Cosmos DB 資源，請將下列範例範本複製到新的 JSON 檔案中。 當您使用不同的名稱和值來部署相同資源的多個執行個體時，可以選擇建立參數 JSON 檔案以供使用。 部署 Azure Resource Manager 範本的方式有很多種，包括 [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 和 [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>適用於 Gremlin 的 Azure Cosmos DB 帳戶，具有自動調整佈建的輸送量

此範本會建立適用於 Gremlin API 的 Azure Cosmos 帳戶，其中包含具有自動調整輸送量的資料庫和圖表。 此範本也適用於從 Azure 快速入門範本資源庫執行的單鍵式部署。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>適用於 Gremlin 的 Azure Cosmos DB 帳戶，具有標準佈建的輸送量

此範本會建立適用於 Gremlin API 的 Azure Cosmos 帳戶，其中包含具有標準 (手動) 輸送量的資料庫和圖表。 此範本也適用於從 Azure 快速入門範本資源庫執行的單鍵式部署。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

* [Azure Resource Manager 文件](/azure/azure-resource-manager/)
* [Azure Cosmos DB 資源提供者結構描述](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [對常見的 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)