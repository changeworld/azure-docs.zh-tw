---
title: 使用 Resource Manager 範本來建立和管理 Azure Cosmos DB
description: 使用 Azure Resource Manager 範本來建立和設定適用于 Core （SQL） API 的 Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 577bc34e5e4b01a234460e5e175c23fd8215743f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791199"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本來管理 Azure Cosmos DB Core （SQL） API 資源

在本文中，您將瞭解如何使用 Azure Resource Manager 範本來協助部署和管理您的 Azure Cosmos DB 帳戶、資料庫和容器。

本文只會顯示適用于 Core （SQL） API 帳戶 Azure Resource Manager 範本範例。 您也可以尋找[Cassandra](manage-cassandra-with-resource-manager.md)、 [Gremlin](manage-gremlin-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)和[資料表](manage-table-with-resource-manager.md)api 的範本範例。

> [!IMPORTANT]
>
> * 帳戶名稱限制為44個字元，全部小寫。
> * 若要變更輸送量值，請使用已更新的 RU/秒來重新部署範本。
> * 當您新增或移除 Azure Cosmos 帳戶的位置時，您無法同時修改其他屬性。 這些作業必須分別執行。

若要建立下列任何 Azure Cosmos DB 資源，請將下列範例範本複製到新的 json 檔案中。 當您使用不同的名稱和值來部署相同資源的多個實例時，可以選擇性地建立參數 json 檔案以供使用。 有許多方式可以部署 Azure Resource Manager 範本，包括、 [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)、 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、 [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)和[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>具有自動調整輸送量的 Azure Cosmos 帳戶

此範本會在兩個區域中建立 Azure Cosmos 帳戶，其中包含一致性和容錯移轉的選項，並已針對已啟用大多數原則選項的自動調整輸送量設定資料庫和容器。 此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-manual-throughput"></a>具有標準（手動）輸送量的 Azure Cosmos 帳戶

此範本會在兩個區域中建立 Azure Cosmos 帳戶，其中包含一致性和容錯移轉的選項，並已針對已啟用大多數原則選項的標準輸送量設定資料庫和容器。 此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>具有伺服器端功能的 Azure Cosmos DB 容器

此範本會使用預存程式、觸發程式和使用者定義函數，來建立 Azure Cosmos 帳戶、資料庫和容器。 此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>免費層 Azure Cosmos DB 帳戶

此範本會建立免費層的 Azure Cosmos 帳戶，以及具有共用輸送量的資料庫，可與多達25個容器共用。 此範本也適用于從 Azure 快速入門範本資源庫中按一下 [部署]。

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

* [Azure Resource Manager 文件](/azure/azure-resource-manager/)
* [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)
