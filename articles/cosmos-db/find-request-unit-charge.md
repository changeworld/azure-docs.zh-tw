---
title: 在 Azure Cosmos DB 中尋找 SQL 查詢的要求單位 (RU) 費用
description: 瞭解如何針對針對 Azure Cosmos 容器執行的 SQL 查詢，尋找要求單位 (RU) 費用。 您可以使用 Azure 入口網站、.NET、JAVA、Python 和 Node.js 語言來尋找 RU 費用。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 9d0694a76bca832887d30601711894b953fe22e1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078436"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>找出 Azure Cosmos DB SQL API 中執行之作業的要求單位費用
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。

所有資料庫作業的成本會由 Azure Cosmos DB 進行正規化，並以要求單位 (簡稱 RU) 表示。 您可以將 ru 視為效能貨幣，以抽象化執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源（例如 CPU、IOPS 和記憶體）。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、點讀取或查詢，成本一律會以 ru 來測量。 若要深入瞭解，請參閱 [要求單位和它的考慮](request-units.md) 文章。

本文提供不同的方式，讓您可以針對 Azure Cosmos DB SQL API 中的容器執行的任何作業，找到 [要求單位](request-units.md) (RU) 耗用量。 如果您使用不同的 API，請參閱 [適用于 MongoDB 的 API](find-request-unit-charge-mongodb.md)、 [CASSANDRA API](find-request-unit-charge-cassandra.md)、 [Gremlin api](find-request-unit-charge-gremlin.md)和 [資料表 API](find-request-unit-charge-table.md) 文章，以找出 RU/s 的費用。

目前，該耗用量只能藉由使用 Azure 入口網站，或藉由查看 Azure Cosmos DB 經任一 SDK 所傳回的回應來測量。 如果您使用的是 SQL API，會有多個選項可用來找出對 Azure Cosmos 容器的作業所產生的 RU 耗用量。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)並為其提供資料，或選取已包含資料的現有 Azure Cosmos 帳戶。

1. 前往 [資料總管]  窗格，然後選取您要處理的容器。

1. 選取 [新增 SQL 查詢]。

1. 輸入有效的查詢，然後選取 [執行查詢]  。

1. 選取下 [查詢統計資料]  ，以顯示您執行的要求實際的要求費用。

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Azure 入口網站之中 SQL 查詢要求費用的螢幕擷取畫面":::

## <a name="use-the-net-sdk"></a>使用 .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

從 [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 傳回的物件會公開 `RequestCharge` 屬性：

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

從 [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 傳回的物件會公開 `RequestCharge` 屬性：

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

如需詳細資訊，請參閱 [快速入門：在 Azure Cosmos DB 中使用 SQL API 帳戶建立 .net web 應用程式](create-sql-api-dotnet.md)。

---

## <a name="use-the-java-sdk"></a>使用 Java SDK

從 [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 傳回的物件會公開 `getRequestCharge()` 方法：

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

如需詳細資訊，請參閱 [快速入門：使用 AZURE COSMOS DB SQL API 帳戶建立 JAVA 應用程式](create-sql-api-java.md)。

## <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

從 [Java SDK](https://www.npmjs.com/package/@azure/cosmos) 傳回的物件會公開 `headers` 子物件 (用以對應基礎 HTTP API 所傳回的所有標頭)。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方：

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

如需詳細資訊，請參閱 [快速入門：使用 AZURE COSMOS DB SQL API 帳戶建立 Node.js 應用程式](create-sql-api-nodejs.md)。 

## <a name="use-the-python-sdk"></a>使用 Python SDK

[Python SDK](https://pypi.org/project/azure-cosmos/) 傳回的 `CosmosClient` 物件 會公開 `last_response_headers` 字典 (用以對應基礎 HTTP API 針對最後執行的作業傳回的所有標頭)。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方：

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

如需詳細資訊，請參閱 [快速入門：使用 AZURE COSMOS DB SQL API 帳戶建立 Python 應用程式](create-sql-api-python.md)。 

## <a name="next-steps"></a>下一步

若要了解如何最佳化您的 RU 耗用量，請參閱下列文章：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](./optimize-cost-reads-writes.md)
* [全域調整佈建的輸送量](./request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [佈建容器的輸送量](how-to-provision-container-throughput.md)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)