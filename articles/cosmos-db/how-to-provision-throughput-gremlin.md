---
title: 在 Azure Cosmos DB Gremlin API 資源上布建輸送量
description: 瞭解如何在 Azure Cosmos DB Gremlin API 資源中布建容器、資料庫和自動調整輸送量。 您將使用 Azure 入口網站、CLI、PowerShell 和其他各種 Sdk。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086154"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>在 Azure Cosmos DB Gremlin API 資源上布建資料庫、容器或自動調整輸送量
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

本文說明如何在 Azure Cosmos DB Gremlin API 中布建輸送量。 您可以在容器或資料庫上布建標準 (手動) 或自動調整輸送量，並在資料庫內的容器之間共用。 您可以使用 Azure 入口網站、Azure CLI 或 Azure Cosmos DB Sdk 來布建輸送量。

如果您使用不同的 API，請參閱 [SQL api](how-to-provision-container-throughput.md)、 [Cassandra API](how-to-provision-throughput-cassandra.md)、 [適用于 MongoDB 的 api](how-to-provision-throughput-mongodb.md) 文章，以布建輸送量。

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選取現有 Azure Cosmos 帳戶。

1. 開啟 **資料總管** 窗格，然後選取 [ **新增圖形]** 。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。 如果您想要在資料庫層級布建輸送量，請選取 [布建 **資料庫輸送量** ] 選項。
   * 輸入圖形識別碼。
   * 輸入分割區索引鍵值，(例如 `/ItemID`)。
   * 輸入您要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="使用資料庫層級輸送量建立新圖形時資料總管的螢幕擷取畫面":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> 使用適用于 SQL API 的 Cosmos Sdk 來布建所有 Azure Cosmos DB Api （Cassandra 和 MongoDB API 除外）的輸送量。

### <a name="provision-container-level-throughput"></a>布建容器層級輸送量

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

### <a name="provision-database-level-throughput"></a>布建資料庫層級輸送量

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

您可以使用 Azure Resource Manager 範本，在資料庫或容器層級資源上布建所有 Azure Cosmos DB Api 的自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB Azure Resource Manager 範本](templates-samples-gremlin.md) 。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure CLI 範例](cli-samples-gremlin.md)。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure PowerShell 範例](powershell-samples-gremlin.md)。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的輸送量佈建：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)