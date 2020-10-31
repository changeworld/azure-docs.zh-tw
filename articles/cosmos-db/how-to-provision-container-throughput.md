---
title: 在 Azure Cosmos DB SQL API 中布建容器輸送量
description: 瞭解如何使用 Azure 入口網站、CLI、PowerShell 和各種其他 Sdk，在 Azure Cosmos DB SQL API 的容器層級布建輸送量。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100094"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>在 Azure Cosmos 容器上布建標準 (手動) 輸送量-SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文說明如何在 Azure Cosmos DB SQL API 的容器上布建標準 (手動) 輸送量。 您可以佈建單一容器的輸送量，或[佈建資料庫的輸送量](how-to-provision-database-throughput.md)，並在資料庫內的容器之間共用。 您可以使用 Azure 入口網站、Azure CLI 或 Azure CosmosDB SDK 來佈建容器的輸送量。

如果您使用不同的 API，請參閱 [適用于 MongoDB 的 API](how-to-provision-throughput-mongodb.md)， [Cassandra API](how-to-provision-throughput-cassandra.md) [Gremlin api](how-to-provision-throughput-gremlin.md) 文章來布建輸送量。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有 Azure Cosmos 帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增容器** ]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器識別碼。
   * 輸入分割區索引鍵值，(例如 `/ItemID`)。
   * 輸入您要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="醒目提示 [新增集合] 的資料總管螢幕擷取畫面":::

## <a name="azure-cli-or-powershell"></a>Azure CLI 或 PowerShell

若要建立具有專用輸送量的容器，請參閱

* [使用 Azure CLI 建立容器](manage-with-cli.md#create-a-container)
* [使用 PowerShell 建立容器](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> 使用適用于 SQL API 的 Cosmos Sdk 來布建所有 Cosmos DB Api （Cassandra 和 MongoDB API 除外）的輸送量。

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

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [如何佈建資料庫的標準 (手動) 輸送量](how-to-provision-database-throughput.md)
* [如何佈建資料庫的自動調整輸送量](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
