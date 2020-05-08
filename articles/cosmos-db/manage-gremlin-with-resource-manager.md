---
title: Azure Cosmos DB Gremlin API 的 Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB Gremlin API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: defeec2317f47e9d79245cbde0ef81c8a3543bfb
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791233"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本來管理 Azure Cosmos DB Gremlin API 資源

在本文中，您將瞭解如何使用 Azure Resource Manager 範本來協助部署和管理您的 Azure Cosmos DB 帳戶、資料庫和圖形。

本文僅提供 Gremlin API 帳戶的範例，若要尋找其他 API 類型帳戶的範例，請參閱：使用 Azure Resource Manager 範本搭配 Azure Cosmos DB 的 API 來執行[Cassandra](manage-cassandra-with-resource-manager.md)、 [SQL](manage-sql-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)、[資料表](manage-table-with-resource-manager.md)文章。

> [!IMPORTANT]
>
> * 帳戶名稱限制為44個字元，全部小寫。
> * 若要變更輸送量值，請使用已更新的 RU/秒來重新部署範本。
> * 當您新增或移除 Azure Cosmos 帳戶的位置時，您無法同時修改其他屬性。 這些作業必須分別執行。

若要建立下列任何 Azure Cosmos DB 資源，請將下列範例範本複製到新的 json 檔案中。 當您使用不同的名稱和值來部署相同資源的多個實例時，可以選擇性地建立參數 json 檔案以供使用。 有許多方式可以部署 Azure Resource Manager 範本，包括、 [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)、 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、 [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)和[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>具有自動調整所布建輸送量的 Gremlin Azure Cosmos DB 帳戶

此範本會建立 Gremlin API 的 Azure Cosmos 帳戶，其中包含具有自動調整輸送量的資料庫和圖形。

此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>具有標準布建輸送量的 Gremlin Azure Cosmos DB 帳戶

此範本會使用具有標準（手動）輸送量的資料庫和圖形，建立 Gremlin API 的 Azure Cosmos 帳戶。

此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

* [Azure Resource Manager 文件](/azure/azure-resource-manager/)
* [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)