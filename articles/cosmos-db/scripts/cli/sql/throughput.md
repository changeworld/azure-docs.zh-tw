---
title: 針對 Azure Cosmos DB 更新 SQL (Core) API 資料庫和容器的 RU/秒
description: 針對 Azure Cosmos DB 更新 SQL (Core) API 資料庫和容器的 RU/秒
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: cde0615a5312372992d2604f32809a983c248a2b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "71274885"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>使用 Azure CLI 針對 Azure Cosmos DB 更新 SQL (Core) API 資料庫和容器的 RU/秒

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0.73 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

此指令碼會建立具有共用輸送量的 SQL (Core) API 資料庫，以及具有專用輸送量的 SQL (Core) API 容器，然後更新資料庫和容器的輸送量。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Update RU/s for a SQL database and container.")]

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
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | 建立 Azure Cosmos SQL (Core) 資料庫。 |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | 建立 Azure Cosmos SQL (Core) 容器。 |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | 更新 Azure Cosmos SQL (Core) 資料庫的 RU/秒。 |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | 更新 Azure Cosmos SQL (Core) 容器的 RU/秒。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure Cosmos DB CLI 的詳細資訊，請參閱 [Azure Cosmos DB CLI 文件](/cli/azure/cosmosdb)。

您可以在 [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 指令碼範例。
