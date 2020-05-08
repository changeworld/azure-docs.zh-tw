---
title: 遷移您的應用程式，以使用 Azure Cosmos DB JAVA SDK v4 （Cosmos）
description: 瞭解如何使用舊版的 Azure Cosmos DB JAVA Sdk，將您現有的 JAVA 應用程式升級為適用于 Core （SQL） API 的較新 JAVA SDK 4.0 （Cosmos 套件）。
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.reviewer: sngun
ms.openlocfilehash: 929fa936cdb864fd9b84f8feba55ef01ae6fed9c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984703"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>遷移應用程式以使用 JAVA SDK v4 Azure Cosmos DB

> [!IMPORTANT]  
> 如需此 SDK 的詳細資訊，請參閱 Azure Cosmos DB JAVA SDK v4 版本資訊、 [Maven 存放庫](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、AZURE COSMOS DB java sdk v4[效能秘訣](performance-tips-java-sdk-v4-sql.md)和 Azure Cosmos DB java sdk v4[疑難排解指南](troubleshoot-java-sdk-v4-sql.md)。
>

本文說明如何將使用舊版 Azure Cosmos DB JAVA SDK 的現有 JAVA 應用程式升級至較新的 Azure Cosmos DB JAVA SDK 4.0 for Core （SQL） API。 Azure Cosmos DB JAVA SDK v4 會對應到`com.azure.cosmos`封裝。 如果您要從下列任一 Azure Cosmos DB JAVA Sdk 遷移應用程式，您可以使用本檔中的指示： 

* 同步處理 JAVA SDK 2.x. x
* Async JAVA SDK 2.x. x
* JAVA SDK 3.x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB JAVA SDK 的和封裝對應

下表列出不同的 Azure Cosmos DB JAVA Sdk、套件名稱和版本資訊：

| Java SDK| 發行日期 | 配套的 Api   | Maven Jar  | JAVA 封裝名稱  |API 參考   | 版本資訊  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2.x. x  | 2018 年 6 月    | 非同步（RxJAVA）  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [版本資訊](sql-api-sdk-async-java.md) |
| Sync 2.x. x     | 2018年9月    | 同步   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [版本資訊](sql-api-sdk-java.md)  |
| 3.x. x    | 2019 年 7 月    | Async （Reactor）/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | 2020 年 4 月   | Async （Reactor）/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>SDK 層級的執行變更

以下是不同 Sdk 之間的主要執行差異：

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJAVA 會在 Azure Cosmos DB JAVA SDK 3.x 版和4.0 中取代為 reactor

如果您不熟悉非同步程式設計或回應式程式設計，請參閱[Reactor 模式指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)，以取得非同步程式設計和專案 Reactor 的簡介。 如果您一直在使用 Azure Cosmos DB 同步處理 JAVA SDK 2.x 或 Azure Cosmos DB JAVA SDK 3.x 同步 API，本指南可能會很有用。

如果您已使用 Azure Cosmos DB 非同步 JAVA SDK 2.x，並打算遷移至 4.0 SDK，請參閱[Reactor Vs RxJAVA 指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)，以取得將 RxJAVA 程式碼轉換為使用 Reactor 的指引。

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB JAVA SDK v4 在非同步和同步 Api 中都具有直接連線模式

如果您已使用 Azure Cosmos DB 同步處理 JAVA SDK 2.x，請注意以 TCP 為基礎的直接連線模式（相對於 HTTP），會在適用于非同步和同步 Api 的 Azure Cosmos DB JAVA SDK 4.0 中執行。

## <a name="api-level-changes"></a>API 層級變更

相較于先前的 Sdk，以下是 Azure Cosmos DB JAVA SDK 4.x 中的 API 層級變更（JAVA SDK 3.x、Async JAVA SDK 2.x 和 Sync JAVA SDK 2.x）：。

![Azure Cosmos DB JAVA SDK 命名慣例](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* Azure Cosmos DB 的 JAVA SDK 3.x 和4.0 會將用戶端資源參考為`Cosmos<resourceName>`。 例如，`CosmosClient`、`CosmosDatabase`、`CosmosContainer`。 而在2.x 版中，Azure Cosmos DB JAVA Sdk 並沒有統一的命名配置。

* Azure Cosmos DB JAVA SDK 3.x 和4.0 提供同步和非同步 Api。

  * **JAVA SDK 4.0** ：除非在`Async`後面`Cosmos`附加類別名稱，否則所有類別都屬於同步 API。

  * **JAVA SDK**3.X：所有類別都屬於非同步 API，除非類別名稱附加`Async`在之後。 `Cosmos`

  * **Async JAVA sdk**2.x：類別名稱類似于同步 java sdk 2.x，不過名稱是以*Async*開頭。

### <a name="hierarchical-api-structure"></a>階層式 API 結構

Azure Cosmos DB JAVA SDK 4.0 和3.x 引進階層式 API 結構，以嵌套的方式組織用戶端、資料庫和容器，如下列 4.0 SDK 程式碼片段所示：

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

在2.x 版的 Azure Cosmos DB JAVA SDK 中，資源和檔上的所有作業都是透過用戶端實例執行。

### <a name="representing-documents"></a>代表檔

在 Azure Cosmos DB JAVA SDK 4.0 中，自訂 POJO `JsonNodes`的和是從 Azure Cosmos DB 讀取和寫入檔的兩個選項。

在 Azure Cosmos DB JAVA SDK 3.x 中， `CosmosItemProperties`物件是由公用 API 公開，並以檔標記法的形式提供。 此類別不再公開于4.0 版。

### <a name="imports"></a>匯入

* Azure Cosmos DB 的 JAVA SDK 4.0 套件開頭為`com.azure.cosmos`
  * Azure Cosmos DB JAVA SDK 3.x 套件開頭為`com.azure.data.cosmos`

* Azure Cosmos DB JAVA SDK 4.0 會將數個類別放在`com.azure.cosmos.models`一個嵌套的封裝中。 其中一些封裝包括：

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * 所有上述封裝的非同步 API 類比
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`...等.

### <a name="accessors"></a>存取子

Azure Cosmos DB JAVA SDK 4.0 會`get`公開`set`和方法，以存取實例成員。 例如， `CosmosContainer`實例具有`container.getId()`和`container.setId()`方法。

這不同于會公開流暢介面的 Azure Cosmos DB JAVA SDK 3.x. x。 例如， `CosmosSyncContainer`實例已`container.id()`多載，以取得或設定`id`值。

## <a name="code-snippet-comparisons"></a>程式碼片段比較

### <a name="create-resources"></a>建立資源

下列程式碼片段顯示如何在4.0 和3.x 非同步 Api 之間建立資源的差異：

# <a name="java-sdk-40-async-api"></a>[JAVA SDK 4.0 非同步 API](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[JAVA SDK 3.x-x 非同步 API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>專案作業

下列程式碼片段顯示如何在4.0 和3.x 非同步 Api 之間執行專案作業的差異：

# <a name="java-sdk-40-async-api"></a>[JAVA SDK 4.0 非同步 API](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[JAVA SDK 3.x-x 非同步 API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>編製索引

下列程式碼片段顯示如何在4.0 和3.x 非同步 Api 之間建立索引的差異：

# <a name="java-sdk-40-async-api"></a>[JAVA SDK 4.0 非同步 API](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[JAVA SDK 3.x-x 非同步 API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>預存程序

下列程式碼片段顯示如何在4.0 和3.x 非同步 Api 之間建立預存程式的差異：

# <a name="java-sdk-40-async-api"></a>[JAVA SDK 4.0 非同步 API](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[JAVA SDK 3.x-x 非同步 API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>變更摘要

下列程式碼片段顯示如何在4.0 和3.x 非同步 Api 之間執行變更摘要作業的差異：

# <a name="java-sdk-40-async-api"></a>[JAVA SDK 4.0 非同步 API](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[JAVA SDK 3.x-x 非同步 API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>容器層級存留時間（TTL）

下列程式碼片段顯示如何使用4.0 和3.x 非同步 Api，為容器中的資料建立存留時間的差異：

# <a name="java-sdk-40-async-api"></a>[JAVA SDK 4.0 非同步 API](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[JAVA SDK 3.x-x 非同步 API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>專案層級存留時間（TTL）

下列程式碼片段顯示如何使用4.0 和3.x 非同步 Api，為專案建立存留時間的差異：

# <a name="java-sdk-40-async-api"></a>[JAVA SDK 4.0 非同步 API](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[JAVA SDK 3.x-x 非同步 API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>後續步驟

* [建立 JAVA 應用程式](create-sql-api-java.md)，以使用 V4 SDK 來管理 AZURE COSMOS DB SQL API 資料
* 瞭解以[Reactor 為基礎的 JAVA sdk](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* 瞭解如何使用[Reactor Vs RxJAVA 指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)將 RxJAVA 非同步程式碼轉換成 Reactor 非同步程式碼