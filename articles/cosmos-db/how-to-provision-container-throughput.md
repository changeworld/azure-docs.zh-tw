---
title: 在 Azure Cosmos DB 中佈建容器輸送量
description: 瞭解如何使用 Azure 入口網站、CLI、PowerShell 和各種其他 Sdk，在 Azure Cosmos DB 的容器層級布建輸送量。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: 6d163609d505516514f078e5d5374c20b5a24e89
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585578"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>在 Azure Cosmos 容器上佈建輸送量

本文說明如何在 Azure Cosmos DB 中佈建容器 (集合、圖表或資料表) 的輸送量。 您可以佈建單一容器的輸送量，或[佈建資料庫的輸送量](how-to-provision-database-throughput.md)，並在資料庫內的容器之間共用。 您可以使用 Azure 入口網站、Azure CLI 或 Azure CosmosDB SDK 來佈建容器的輸送量。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有 Azure Cosmos 帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器 (或資料表或圖形) 識別碼。
   * 輸入分割區索引鍵值，(例如 `/userid`)。
   * 輸入您要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    ![醒目提示 [新增集合] 的資料總管螢幕擷取畫面](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI 或 PowerShell

若要建立具有專用輸送量的容器，請參閱

* [使用 Azure CLI 建立容器](manage-with-cli.md#create-a-container)
* [使用 Powershell 建立容器](manage-with-powershell.md#create-container)

> [!Note]
> 如果您要在使用適用於 MongoDB 的 Azure Cosmos DB API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myShardKey`。 如果您要在使用 Cassandra API 設定的 Azure Cosmos 帳戶中佈建容器的輸送量，請使用分割區索引鍵路徑的 `/myPrimaryKey`。

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> 使用適用於 SQL API 的 Cosmos SDK 來為 Cassandra API 以外的所有 Cosmos DB API 佈建輸送量。

### <a id="dotnet-most"></a>SQL、MongoDB、Gremlin 和資料表 API
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

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs" id="ContainerCreateWithThroughput":::

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

### <a id="dotnet-cassandra"></a>Cassandra API

類似的命令可以透過任何符合 CQL 規範的驅動程式來發出。

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>改變或變更 Cassandra 資料表的輸送量

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [如何佈建資料庫的輸送量](how-to-provision-database-throughput.md)
* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
