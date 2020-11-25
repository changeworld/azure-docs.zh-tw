---
title: 為 Azure Cosmos DB MongoDB API 的資料庫和集合建立資源鎖定
description: 為 Azure Cosmos DB MongoDB API 的資料庫和集合建立資源鎖定
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 2ea833b72b9522cdfca836a7b13f7b411402103e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562690"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>使用 Azure CLI 為 Azure Cosmos DB 的 MongoDB API 建立資源鎖定
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.9.1 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

> [!IMPORTANT]
> 除非先鎖定 Cosmos DB 帳戶並啟用 `disableKeyBasedMetadataWriteAccess` 屬性，否則資源鎖定不適用於使用任何 MongoDB SDK、Mongoshell、任何工具或 Azure 入口網站進行連線之使用者所做的變更。 若要深入了解如何啟用此屬性，請參閱[防止來自 SDK 的變更](../../../role-based-access-control.md#prevent-sdk-changes) (機器翻譯)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | 建立鎖定。 |
| [az lock list](/cli/azure/lock#az-lock-list) | 列出鎖定資訊。 |
| [az lock show](/cli/azure/lock#az-lock-show) | 顯示鎖定的屬性。 |
| [az lock delete](/cli/azure/lock#az-lock-delete) | 刪除鎖定。 |

## <a name="next-steps"></a>後續步驟

- [鎖定資源以防止非預期的變更](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI 文件](/cli/azure/cosmosdb)。

- [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
