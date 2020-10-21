---
title: 使用 Resource Manager 範本建立和管理 Azure Cosmos DB
description: 使用 Azure Resource Manager 範本建立和設定 Azure Cosmos DB for Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 02e9fbbe98458e4f9ba39e971b2c5fe50fd8fa4d
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283841"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本管理 Azure Cosmos DB Core (SQL) API 資源

在本文中，您會了解如何利用 Azure Resource Manager 範本來部署和管理 Azure Cosmos DB 帳戶、資料庫和容器。

本文僅顯示 Core (SQL) API 帳戶的 Azure Resource Manager 範本範例。 您也可以尋找 [Cassandra](templates-samples-cassandra.md)、[Gremlin](templates-samples-gremlin.md)、[MongoDB](templates-samples-mongodb.md) 和[資料表](templates-samples-table.md) API 的範本範例。

> [!IMPORTANT]
>
> * 帳戶名稱限制為 44 個字元，全部小寫。
> * 若要變更輸送量值，請使用已更新的 RU/秒重新部署範本。
> * 您在新增或移除 Azure Cosmos 帳戶的位置時，無法同時修改其他屬性。 這些作業必須個別執行。
> * 無法重新命名 Azure Cosmos DB 資源，因為這違反了 Azure Resource Manager 搭配資源 Uri 運作的方式。

若要建立下列任何 Azure Cosmos DB 資源，請將下列範例範本複製到新的 JSON 檔案中。 當您使用不同的名稱和值來部署相同資源的多個執行個體時，可以選擇建立參數 JSON 檔案以供使用。 部署 Azure Resource Manager 範本的方式有很多種，包括 [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 和 [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>具有自動調整輸送量的 Azure Cosmos 帳戶

此範本會在兩個區域中建立 Azure Cosmos 帳戶，其中包含一致性和容錯移轉的選項，以及針對已啟用多數原則選項的自動調整輸送量進行設定的資料庫和容器。 此範本也適用於從 Azure 快速入門範本資源庫執行的單鍵式部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署至 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>具有分析存放區的 Azure Cosmos 帳戶

此範本會在一個區域中建立 Azure Cosmos 帳戶，以及已啟用分析 TTL、並且有手動或自動調整輸送量選項的容器。 此範本也適用於從 Azure 快速入門範本資源庫執行的單鍵式部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署至 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Azure Cosmos 帳戶，具有標準佈建的輸送量

此範本會在兩個區域中建立 Azure Cosmos 帳戶，其中包含一致性和容錯移轉的選項，以及針對已啟用多數原則選項的標準輸送量進行設定的資料庫和容器。 此範本也適用於從 Azure 快速入門範本資源庫執行的單鍵式部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署至 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>具有伺服器端功能的 Azure Cosmos DB 容器

此範本會建立具有預存程序、觸發程序和使用者定義函式的 Azure Cosmos 帳戶、資料庫和容器。 此範本也適用於從 Azure 快速入門範本資源庫執行的單鍵式部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署至 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>免費層 Azure Cosmos DB 帳戶

此範本會建立免費層的 Azure Cosmos 帳戶，以及具有共用輸送量 (最多可與 25 個容器共用) 的資料庫。 此範本也適用於從 Azure 快速入門範本資源庫執行的單鍵式部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署至 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

* [Azure Resource Manager 文件](/azure/azure-resource-manager/)
* [Azure Cosmos DB 資源提供者結構描述](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [對常見的 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)
