---
title: 建立 Azure Cosmos DB 的 Core (SQL) API 資料庫和容器
description: 建立 Azure Cosmos DB 的 Core (SQL) API 資料庫和容器
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 416da39df9bfb49d6323ee789d5e67b1743a1cd7
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509384"
---
# <a name="create-an-azure-cosmos-core-sql-api-account-database-and-container-using-azure-cli"></a>使用 Azure CLI 建立 Azure Cosmos Core (SQL) API 帳戶、資料庫及容器

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0.73 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/create.sh "Create an Azure Cosmos DB SQL (Core) API account, database, and container.")]

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
| [az group delete](/cli/azure/resource#az-resource-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure Cosmos DB CLI 的詳細資訊，請參閱 [Azure Cosmos DB CLI 文件](/cli/azure/cosmosdb)。

您可以在 [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 指令碼範例。
