---
title: 在 Azure Cosmos DB 中建立容器
description: 瞭解如何使用 Azure 門戶 、.Net、JAVA、Python、Node.js 和其他 SDK 在 Azure Cosmos DB 中創建容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873704"
---
# <a name="create-an-azure-cosmos-container"></a>建立 Azure Cosmos 容器

本文說明建立 Azure Cosmos 容器 (集合、資料表或圖形) 的不同方式。 您可使用 Azure 入口網站、Azure CLI 或支援的 SKD。 本文將示範如何建立容器、指定分割區索引鍵，以及佈建輸送量。

## <a name="create-a-container-using-azure-portal"></a>使用 Azure 入口網站建立容器

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有帳戶。

1. 打開 **"資料資源管理器"** 窗格，然後選擇 **"新容器**"。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器 ID。
   * 輸入分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]****。

    ![資料資源管理器窗格的螢幕截圖，突出顯示了新容器](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. [創建新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選擇現有帳戶。

1. 打開 **"資料資源管理器"** 窗格，然後選擇 **"新容器**"。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器 ID。
   * 輸入分區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]****。

    ![蒙戈DB的 Azure Cosmos DB API 螢幕截圖，添加容器對話方塊](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>卡珊多拉 API

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. [創建新的 Azure Cosmos 帳戶](create-cassandra-dotnet.md#create-a-database-account)，或選擇現有帳戶。

1. 打開 **"資料資源管理器"** 窗格，然後選擇 **"新建表**"。 接下來，提供下列詳細資料：

   * 指出您正在建立新的 Keyspace，還是使用現有的 Keyspace。
   * 輸入資料表名稱。
   * 輸入屬性並指定主索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]****。

    ![Cassandra API 中 [新增資料表] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API 的主索引鍵會作為分割區索引鍵。

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin API

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. [創建新的 Azure Cosmos 帳戶](create-graph-dotnet.md#create-a-database-account)，或選擇現有帳戶。

1. 打開 **"資料資源管理器"** 窗格，然後選擇 **"新圖形**"。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入圖形識別碼。
   * 選取 [不受限]**** 的儲存體容量。
   * 輸入頂點的分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]****。

    ![Gremlin API 中 [新增圖形] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a name="table-api"></a><a id="portal-table"></a>表 API

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. [創建新的 Azure Cosmos 帳戶](create-table-dotnet.md#create-a-database-account)，或選擇現有帳戶。

1. 打開 **"資料資源管理器"** 窗格，然後選擇 **"新建表**"。 接下來，提供下列詳細資料：

   * 輸入資料表識別碼。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]****。

    ![資料表 API 中 [新增資料表] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> 對於資料表 API，在您每次新增資料列時都會指定分割區索引鍵。

## <a name="create-a-container-using-azure-cli"></a>使用 Azure CLI 創建容器<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

以下連結演示如何使用 Azure CLI 為 Azure Cosmos DB 創建容器資源。

有關所有 Azure Cosmos DB API 中的所有 Azure CLI 示例的清單，請參閱[SQL API、](cli-samples.md)[卡珊多拉](cli-samples-cassandra.md)API、MongoDB [Table API](cli-samples-table.md) [API、Gremlin API](cli-samples-gremlin.md)和表 API [MongoDB API](cli-samples-mongodb.md)

* [使用 Azure CLI 創建容器](manage-with-cli.md#create-a-container)
* [使用 Azure CLI 為蒙戈DB API 創建 Azure 宇宙 DB 集合](./scripts/cli/mongodb/create.md)
* [使用 Azure CLI 創建卡珊多拉表](./scripts/cli/cassandra/create.md)
* [使用 Azure CLI 創建格雷姆林圖形](./scripts/cli/gremlin/create.md)
* [使用 Azure CLI 創建表 API 表](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>使用 PowerShell 創建容器<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

以下連結演示如何使用 PowerShell 為 Azure Cosmos DB 創建容器資源。

有關所有 Azure Cosmos DB API 中的所有 Azure CLI 示例的清單，請參閱[SQL API、](powershell-samples-sql.md)[卡珊多拉](powershell-samples-cassandra.md)API、MongoDB [Table API](powershell-samples-table.md) [API、Gremlin API](powershell-samples-gremlin.md)和表 API [MongoDB API](powershell-samples-mongodb.md)

* [使用電源外殼創建容器](manage-with-powershell.md#create-container)
* [使用 Powershell 為 MongoDB API 創建 Azure 宇宙 DB 集合](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [使用電源shell 創建卡珊多拉表](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [使用電源殼創建格雷姆林圖形](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [使用電源外殼創建表 API 表](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>使用 .NET SDK 建立容器

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

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>卡珊多拉 API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [在 Azure 宇宙資料庫中請求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)
