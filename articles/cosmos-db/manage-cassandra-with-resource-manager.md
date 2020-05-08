---
title: Azure Cosmos DB 的 Resource Manager 範本 Cassandra API
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB Cassandra API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: f16dec74b15f4945b54fe1423835fd8f5c8d96f1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791267"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本管理 Azure Cosmos DB Cassandra API 資源

在本文中，您將瞭解如何使用 Azure Resource Manager 範本來協助部署和管理您的 Azure Cosmos DB 帳戶、keyspace 和資料表。

本文僅提供 Cassandra API 帳戶的範例，若要尋找其他 API 類型帳戶的範例，請參閱：搭配使用 Azure Resource Manager 範本與適用于[SQL](manage-sql-with-resource-manager.md)的 Azure Cosmos DB API、 [Gremlin](manage-gremlin-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)、[資料表](manage-table-with-resource-manager.md)發行項。

> [!IMPORTANT]
>
> * 帳戶名稱限制為44個字元，全部小寫。
> * 若要變更輸送量值，請使用已更新的 RU/秒來重新部署範本。
> * 當您新增或移除 Azure Cosmos 帳戶的位置時，您無法同時修改其他屬性。 這些作業必須分別執行。

若要建立下列任何 Azure Cosmos DB 資源，請將下列範例範本複製到新的 json 檔案中。 當您使用不同的名稱和值來部署相同資源的多個實例時，可以選擇性地建立參數 json 檔案以供使用。 有許多方式可以部署 Azure Resource Manager 範本，包括、 [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)、 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、 [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)和[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>適用于 Cassandra 的 Azure Cosmos 帳戶，具有自動調整布建的輸送量

此範本會在兩個區域中建立 Azure Cosmos 帳戶，其中包含一致性和容錯移轉的選項，並已針對自動調整輸送量設定 keyspace 和資料表。 此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autosscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-manual-provisioned-throughput"></a>適用于 Cassandra 的 Azure Cosmos 帳戶，具有標準（手動）布建的輸送量

此範本會在兩個區域中建立 Azure Cosmos 帳戶，其中包含一致性和容錯移轉的選項，並已針對標準輸送量設定 keyspace 和資料表。 此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

* [Azure Resource Manager 文件](/azure/azure-resource-manager/)
* [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)
