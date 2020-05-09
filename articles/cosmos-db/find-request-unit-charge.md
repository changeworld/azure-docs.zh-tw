---
title: 在 Azure Cosmos DB 中尋找要求單位 (RU) 費用
description: 了解如何尋找對 Azure Cosmos 容器執行的任何作業所產生的要求單位 (RU) 費用。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: e5420b9b765fffcf7b4ccd6775d05795b1b13871
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872239"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中尋找要求單位費用

本文將介紹不同的方法，供您尋找對 Azure Cosmos DB 中的容器執行的任何作業所產生的[要求單位](request-units.md) (RU) 耗用量。 目前，該耗用量只能藉由使用 Azure 入口網站，或藉由查看 Azure Cosmos DB 經任一 SDK 所傳回的回應來測量。

## <a name="sql-core-api"></a>SQL (Core) API

如果您使用的是 SQL API，會有多個選項可用來找出對 Azure Cosmos 容器的作業所產生的 RU 耗用量。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

目前，在 Azure 入口網站中，您只能找到 SQL 查詢的要求費用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)並為其提供資料，或選取已包含資料的現有 Azure Cosmos 帳戶。

1. 前往 [資料總管]**** 窗格，然後選取您要處理的容器。

1. 選取 [新增 SQL 查詢]****。

1. 輸入有效的查詢，然後選取 [執行查詢]****。

1. 選取下 [查詢統計資料]****，以顯示您執行的要求實際的要求費用。

![Azure 入口網站之中 SQL 查詢要求費用的螢幕擷取畫面](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>使用 .NET SDK

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

如需詳細資訊，請參閱[快速入門：在 Azure Cosmos DB 中使用 SQL API 帳戶建立 .net web 應用程式](create-sql-api-dotnet.md)。

---

### <a name="use-the-java-sdk"></a>使用 Java SDK

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

如需詳細資訊，請參閱[快速入門：使用 AZURE COSMOS DB SQL API 帳戶來建立 JAVA 應用程式](create-sql-api-java.md)。

### <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

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

如需詳細資訊，請參閱[快速入門：使用 AZURE COSMOS DB SQL API 帳戶建立 node.js 應用程式](create-sql-api-nodejs.md)。 

### <a name="use-the-python-sdk"></a>使用 Python SDK

[Python SDK](https://pypi.org/project/azure-cosmos/) 傳回的 `CosmosClient` 物件 會公開 `last_response_headers` 字典 (用以對應基礎 HTTP API 針對最後執行的作業傳回的所有標頭)。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方：

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

如需詳細資訊，請參閱[快速入門：使用 AZURE COSMOS DB SQL API 帳戶建立 Python 應用程式](create-sql-api-python.md)。 

## <a name="azure-cosmos-db-api-for-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

RU 費用可用名為 `getLastRequestStatistics` 的自訂[資料庫命令](https://docs.mongodb.com/manual/reference/command/)公開。 此命令會傳回一份文件，其中包含最後執行的作業名稱，及其要求費用和持續時間。 如果您使用的是 Azure Cosmos DB API for MongoDB，會有多個選項可供擷取 RU 費用。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

目前，在 Azure 入口網站中，您只能找到查詢的要求費用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)並為其提供資料，或選取已包含資料的現有帳戶。

1. 前往 [資料總管]**** 窗格，然後選取您要處理的容器。

1. 選取 [新增查詢]  。

1. 輸入有效的查詢，然後選取 [執行查詢]****。

1. 選取下 [查詢統計資料]****，以顯示您執行的要求實際的要求費用。

![Azure 入口網站之中 MongoDB 查詢要求費用的螢幕擷取畫面](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>使用 MongoDB .NET 驅動程式

使用[官方的 MongoDB .NET 驅動程式](https://docs.mongodb.com/ecosystem/drivers/csharp/)時，可以對 `IMongoDatabase` 物件呼叫 `RunCommand` 方法來執行命令。 使用此方法時，必須實作 `Command<>` 抽象類別：

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

如需詳細資訊，請參閱[快速入門：使用適用于 MongoDB 的 AZURE COSMOS DB API 建立 .net web 應用程式](create-mongodb-dotnet.md)。

### <a name="use-the-mongodb-java-driver"></a>使用 MongoDB Java 驅動程式


使用[官方的 MongoDB Java 驅動程式](https://mongodb.github.io/mongo-java-driver/)時，可以對 `MongoDatabase` 物件呼叫 `runCommand` 方法來執行命令：

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

如需詳細資訊，請參閱[快速入門：使用適用于 MongoDB 的 AZURE COSMOS DB API 和 JAVA SDK 來建立 web 應用程式](create-mongodb-java.md)。

### <a name="use-the-mongodb-nodejs-driver"></a>使用 MongoDB Node.js 驅動程式

使用[官方的 MongoDB Node.js 驅動程式](https://mongodb.github.io/node-mongodb-native/)時，可以於 `db` 物件呼叫 `command` 方法來執行命令：

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

如需詳細資訊，請參閱[快速入門：將現有的 MongoDB node.js web 應用程式遷移至 Azure Cosmos DB](create-mongodb-nodejs.md)。

## <a name="cassandra-api"></a>Cassandra API

對 Azure Cosmos DB 的 Cassandra API 執行作業時，RU 費用會在傳入的裝載中以名為 `RequestCharge` 的欄位傳回。 有多個選項可供擷取 RU 費用。

### <a name="use-the-net-sdk"></a>使用 .NET SDK

使用 [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) 時，可以擷取 `RowSet` 物件的 `Info` 屬性下的傳入承載：

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

如需詳細資訊，請參閱[快速入門：使用 .NET SDK 建立 Cassandra 應用程式和 Azure Cosmos DB](create-cassandra-dotnet.md)。

### <a name="use-the-java-sdk"></a>使用 Java SDK

使用 [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) 時，可以對 `ResultSet` 物件呼叫 `getExecutionInfo()` 方法來擷取傳入承載：

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

如需詳細資訊，請參閱[快速入門：使用 JAVA SDK 建立 Cassandra 應用程式和 Azure Cosmos DB](create-cassandra-java.md)。

## <a name="gremlin-api"></a>Gremlin API

如果您使用的是 Gremlin API 時，會有多個選項可用來找出對 Azure Cosmos 容器的作業所產生的 RU 耗用量。 

### <a name="use-drivers-and-sdk"></a>使用驅動程式和 SDK

Gremlin API 所傳回的標頭會對應至目前透過 Gremlin.NET 和 Java SDK 來呈現的自訂狀態屬性。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方。

### <a name="use-the-net-sdk"></a>使用 .NET SDK

使用 [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) 時，狀態屬性會提供在 `ResultSet<>` 物件的 `StatusAttributes` 屬性下方：

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

如需詳細資訊，請參閱[快速入門：使用 Azure Cosmos DB GREMLIN API 帳戶來建立 .NET Framework 或核心應用程式](create-graph-dotnet.md)。

### <a name="use-the-java-sdk"></a>使用 Java SDK

使用 [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 時，可以對於 `ResultSet` 物件呼叫 `statusAttributes()` 方法來擷取狀態屬性：

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

如需詳細資訊，請參閱[快速入門：使用 JAVA SDK 在 Azure Cosmos DB 中建立圖形資料庫](create-graph-java.md)。

## <a name="table-api"></a>資料表 API

目前，對於資料表作業傳回 RU 費用的唯一 SDK 是 [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)。 `TableResult` 物件會公開您對於 Azure Cosmos DB 資料表 API 使用時由 SDK 填入的 `RequestCharge` 屬性：

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

如需詳細資訊，請參閱[快速入門：使用 .NET SDK 建立資料表 API 應用程式和 Azure Cosmos DB](create-table-dotnet.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何最佳化您的 RU 耗用量，請參閱下列文章：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](optimize-cost-queries.md)
* [全域調整佈建的輸送量](scaling-throughput.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [佈建容器的輸送量](how-to-provision-container-throughput.md)
* [使用 Azure Cosmos DB 中的計量進行監視及偵錯](use-metrics.md)
