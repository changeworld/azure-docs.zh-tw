---
title: 針對 Azure Cosmos 帳戶新增區域、變更容錯移轉優先順序、觸發容錯移轉
description: 針對 Azure Cosmos 帳戶新增區域、變更容錯移轉優先順序、觸發容錯移轉
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275105"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>使用 Azure CLI 針對 Azure Cosmos 帳戶新增區域、變更容錯移轉優先順序、觸發容錯移轉

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0.73 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

此指令碼示範三項作業。

- 將區域新增至現有的 Azure Cosmos 帳戶。
- 變更區域容錯移轉優先順序 (適用於使用自動容錯移轉的帳戶)
- 觸發從主要到次要區域的手動容錯移轉 (適用於採用手動容錯移轉的帳戶)

> [!NOTE]
> 變更其他屬性時，無法在 Cosmos 帳戶上新增和移除區域作業。

> [!NOTE]
> 此範例示範如何使用 SQL (Core) API 帳戶，但這些作業在 Cosmos DB 的所有資料庫 API 中都是相同的。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | 建立 Azure Cosmos DB 帳戶。 |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | 更新 Azure Cosmos DB 帳戶 (新增或移除區域)。 |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | 更新 Azure Cosmos DB 帳戶的容錯移轉優先順序或觸發容錯移轉。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure Cosmos DB CLI 的詳細資訊，請參閱 [Azure Cosmos DB CLI 文件](/cli/azure/cosmosdb)。

您可以在 [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 指令碼範例。
