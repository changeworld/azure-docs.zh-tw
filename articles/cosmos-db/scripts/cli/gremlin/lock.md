---
title: 為 Azure Cosmos DB 建立 Gremlin 資料庫與圖表的資源鎖定
description: 為 Azure Cosmos DB 建立 Gremlin 資料庫與圖表的資源鎖定
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: e4aba05df23ce4048ba711680086407cb764a24a
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514600"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>使用 Azure CLI 建立 Azure Cosmos Gremlin API 資料庫與圖表的資源鎖定

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

若選擇在本機安裝及使用 CLI，此主題需要您執行 Azure CLI 2.6.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

> [!IMPORTANT]
> 除非先啟用 `disableKeyBasedMetadataWriteAccess` 屬性鎖定 Cosmos DB 帳戶，否則資源鎖定對使用任何 Gremlin SDK 或 Azure 入口網站進行連線之使用者所做的變更無效。 若要深入了解如何啟用此屬性，請參閱[防止 SDK 進行變更](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | 建立鎖定。 |
| [az lock list](/cli/azure/lock#az-lock-list) | 列出鎖定資訊。 |
| [az lock show](/cli/azure/lock#az-lock-show) | 顯示鎖定的屬性。 |
| [az lock delete](/cli/azure/lock#az-lock-delete) | 刪除鎖定。 |

## <a name="next-steps"></a>後續步驟

-[鎖定資源防止未預期的變更](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI 文件](/cli/azure/cosmosdb)。

-[Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
