---
title: 在 Azure Cosmos DB 中佈建容器輸送量
description: 瞭解如何使用 Azure 門戶、CLI、PowerShell 和各種其他 SDK 在 Azure Cosmos DB 中的容器級別預配輸送量。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273284"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>在 Azure Cosmos 容器上佈建輸送量

本文說明如何在 Azure Cosmos DB 中佈建容器 (集合、圖表或資料表) 的輸送量。 您可以在單個容器上預配輸送量，也可以[在資料庫上預配輸送量](how-to-provision-database-throughput.md)，並在資料庫中的容器之間共用輸送量。 您可以使用 Azure 入口網站、Azure CLI 或 Azure CosmosDB SDK 來佈建容器的輸送量。

## <a name="azure-portal"></a>Azure 入口網站

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有 Azure Cosmos 帳戶。

1. 打開 **"資料資源管理器"** 窗格，然後選擇 **"新集合**"。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器 (或資料表或圖形) 識別碼。
   * 輸入分割區索引鍵值，(例如 `/userid`)。
   * 輸入您要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]****。

    ![醒目提示 [新增集合] 的資料總管螢幕擷取畫面](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI 或 PowerShell

要創建具有專用輸送量的容器，請參閱，

* [使用 Azure CLI 建立容器](manage-with-cli.md#create-a-container)
* [使用 Powershell 創建容器](manage-with-powershell.md#create-container)

> [!Note]
> 如果您要在使用適用於 MongoDB 的 Azure Cosmos DB API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myShardKey`。 如果您要在使用 Cassandra API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myPrimaryKey`。

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> 使用適用於 SQL API 的 Cosmos SDK 來為 Cassandra API 以外的所有 Cosmos DB API 佈建輸送量。

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL、MongoDB、Gremlin 和資料表 API
### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>卡珊多拉 API

類似的命令可以通過任何符合 CQL 的驅動程式發出。

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>更改或更改卡珊多拉表的輸送量

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [如何佈建資料庫的輸送量](how-to-provision-database-throughput.md)
* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
