---
title: 在 Azure Cosmos DB 資料表 API 中建立容器
description: 瞭解如何使用 Azure 入口網站、.NET、JAVA、Python、Node.js 和其他 Sdk，在 Azure Cosmos DB 資料表 API 中建立容器。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101573"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>在 Azure Cosmos DB 資料表 API 中建立容器
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

本文說明在 Azure Cosmos DB 資料表 API 中建立容器的不同方式。 它會顯示如何使用 Azure 入口網站、Azure CLI、PowerShell 或支援的 Sdk 來建立容器。 本文將示範如何建立容器、指定分割區索引鍵，以及佈建輸送量。

本文說明在 Azure Cosmos DB 資料表 API 中建立容器的不同方式。 如果您使用不同的 API，請參閱 [適用于 MongoDB 的 API](how-to-create-container-mongodb.md)、 [CASSANDRA API](how-to-create-container-cassandra.md)、 [Gremlin api](how-to-create-container-gremlin.md)和 [SQL API](how-to-create-container.md) 文章，以建立容器。

> [!NOTE]
> 建立容器時，請確定您不會建立兩個名稱相同但大小寫不同的容器。 這是因為 Azure 平台的某些部分不會區分大小寫，而這可能導致在具有這類名稱的容器上發生遙測和動作的混淆/衝突。

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>使用 Azure 入口網站建立

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-table-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增資料表** ]。 接下來，提供下列詳細資料：

   * 輸入資料表識別碼。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]  。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="資料表 API 中 [新增資料表] 對話方塊的螢幕擷取畫面":::

> [!Note]
> 對於資料表 API，在您每次新增資料列時都會指定分割區索引鍵。

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>使用 Azure CLI 建立

[使用 Azure CLI 建立資料表 API 資料表](./scripts/cli/table/create.md)。 如需所有 Azure Cosmos DB Api 中所有 Azure CLI 範例的清單，請參閱 [Azure Cosmos DB 的 Azure CLI 範例](cli-samples.md)。

## <a name="create-using-powershell"></a>使用 PowerShell 建立

[使用 PowerShell 建立資料表 API 資料表](./scripts/powershell/table/create.md)。 如需所有 Azure Cosmos DB Api 上所有 PowerShell 範例的清單，請參閱 [Powershell 範例](powershell-samples.md)

## <a name="next-steps"></a>下一步

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](./account-databases-containers-items.md)