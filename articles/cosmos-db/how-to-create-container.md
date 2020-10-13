---
title: 在 Azure Cosmos DB 中建立容器
description: 瞭解如何使用 Azure 入口網站、.NET、JAVA、Python、Node.js 及其他 Sdk，在 Azure Cosmos DB 中建立容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2362326bccd90af997aa9237ec5f14e39ae62c85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019992"
---
# <a name="create-an-azure-cosmos-container"></a>建立 Azure Cosmos 容器

本文說明使用 Azure 入口網站、Azure CLI、PowerShell 或支援的 Sdk，建立 Azure Cosmos 容器 (集合、資料表或圖形) 的不同方式。 本文將示範如何建立容器、指定分割區索引鍵，以及佈建輸送量。

> [!NOTE]
> 建立容器時，請確定您不會建立兩個名稱相同但大小寫不同的容器。 這是因為 Azure 平台的某些部分不會區分大小寫，而這可能導致在具有這類名稱的容器上發生遙測和動作的混淆/衝突。

## <a name="create-a-container-using-azure-portal"></a>使用 Azure 入口網站建立容器

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增容器**]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器識別碼。
   * 輸入分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]  。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="資料總管窗格的螢幕擷取畫面，其中已醒目提示新的容器":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增容器**]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器識別碼。
   * 輸入分區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]  。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="資料總管窗格的螢幕擷取畫面，其中已醒目提示新的容器":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-cassandra-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增資料表**]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的 Keyspace，還是使用現有的 Keyspace。
   * 輸入資料表名稱。
   * 輸入屬性並指定主索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]  。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="資料總管窗格的螢幕擷取畫面，其中已醒目提示新的容器":::

> [!NOTE]
> Cassandra API 的主索引鍵會作為分割區索引鍵。

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-graph-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增圖形]**。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入圖形識別碼。
   * 選取 [不受限]**** 的儲存體容量。
   * 輸入頂點的分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]  。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="資料總管窗格的螢幕擷取畫面，其中已醒目提示新的容器":::

### <a name="table-api"></a><a id="portal-table"></a>資料表 API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-table-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增資料表**]。 接下來，提供下列詳細資料：

   * 輸入資料表識別碼。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]  。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="資料總管窗格的螢幕擷取畫面，其中已醒目提示新的容器":::

> [!Note]
> 對於資料表 API，在您每次新增資料列時都會指定分割區索引鍵。

## <a name="create-a-container-using-azure-cli"></a>使用 Azure CLI 建立容器<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

下列連結顯示如何使用 Azure CLI 來建立 Azure Cosmos DB 的容器資源。

如需所有 Azure Cosmos DB Api 中所有 Azure CLI 範例的清單，請參閱 [Azure Cosmos DB 的 Azure CLI 範例](cli-samples.md)。

* [使用 Azure CLI 建立容器](manage-with-cli.md#create-a-container)
* [使用 Azure CLI 建立適用于 MongoDB API 的 Azure Cosmos DB 集合](./scripts/cli/mongodb/create.md)
* [使用 Azure CLI 建立 Cassandra 資料表](./scripts/cli/cassandra/create.md)
* [使用 Azure CLI 建立 Gremlin 圖形](./scripts/cli/gremlin/create.md)
* [使用 Azure CLI 建立資料表 API 資料表](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>使用 PowerShell 建立容器

下列連結說明如何使用 PowerShell 建立 Azure Cosmos DB 的容器資源。

如需所有 Azure Cosmos DB Api 上所有 PowerShell 範例的清單，請參閱 [Powershell 範例](powershell-samples.md)

* [使用 PowerShell 建立容器](manage-with-powershell.md#create-container)
* [使用 PowerShell 建立適用于 MongoDB API 的 Azure Cosmos DB 集合](./scripts/powershell/mongodb/create.md)
* [使用 PowerShell 建立 Cassandra 資料表](./scripts/powershell/cassandra/create.md)
* [使用 PowerShell 建立 Gremlin 圖形](./scripts/powershell/gremlin/create.md)
* [使用 PowerShell 建立資料表 API 資料表](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>使用 .NET SDK 建立容器

如果您在建立集合時遇到 timeout 例外狀況，請執行讀取作業來驗證是否已成功建立集合。 讀取作業會擲回例外狀況，直到收集建立作業成功為止。 如需建立作業所支援的狀態代碼清單，請參閱 [Azure Cosmos DB 文章的 HTTP 狀態碼](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 。

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL API 和 Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB 網路通訊協定不了解[要求單位](request-units.md)的概念。 若要建立已佈建輸送量的新集合，請使用 Azure 入口網站或適用於 SQL API 的 Cosmos DB SDK。

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)
