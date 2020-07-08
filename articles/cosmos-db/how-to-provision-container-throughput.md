---
title: 在 Azure Cosmos DB 中佈建容器輸送量
description: 了解如何使用 Azure 入口網站、CLI、PowerShell 及各種其他 SDK，在 Azure Cosmos DB 中的容器層級佈建輸送量。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: 9167df9c763f4004324a3435ba1a2b0fd0171ac4
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851685"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container"></a>在 Azure Cosmos 容器上佈建標準 (手動) 輸送量

本文說明如何在 Azure Cosmos DB 中佈建容器 (集合、圖表或資料表) 的標準 (手動) 輸送量。 您可以佈建單一容器的輸送量，或[佈建資料庫的輸送量](how-to-provision-database-throughput.md)，並在資料庫內的容器之間共用。 您可以使用 Azure 入口網站、Azure CLI 或 Azure CosmosDB SDK 來佈建容器的輸送量。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有 Azure Cosmos 帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器 (或資料表或圖形) 識別碼。
   * 輸入分割區索引鍵值，(例如 `/userid`)。
   * 輸入您要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png" alt-text="醒目提示 [新增集合] 的資料總管螢幕擷取畫面":::

## <a name="azure-cli-or-powershell"></a>Azure CLI 或 PowerShell

若要建立具有專用輸送量的容器，請參閱

* [使用 Azure CLI 建立容器](manage-with-cli.md#create-a-container)
* [使用 PowerShell 建立容器](manage-with-powershell.md#create-container)

> [!Note]
> 如果您要在使用適用於 MongoDB 的 Azure Cosmos DB API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myShardKey`。 如果您要在使用 Cassandra API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myPrimaryKey`。

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> 使用適用于 SQL API 的 Cosmos Sdk 來布建所有 Cosmos DB Api （Cassandra 和 MongoDB API 除外）的輸送量。

### <a name="sql-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL、Gremlin 和資料表 Api

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

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

### <a name="mongodb-api"></a><a id="dotnet-mongodb"></a>MongoDB API

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

類似的命令可以透過任何符合 CQL 規範的驅動程式發出。

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>更改或變更 Cassandra 資料表的輸送量

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [如何佈建資料庫的標準 (手動) 輸送量](how-to-provision-database-throughput.md)
* [如何佈建資料庫的自動調整輸送量](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
