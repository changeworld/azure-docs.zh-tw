---
title: Azure Cosmos DB 適用於 MongoDB 的 API 中的變更資料流
description: 了解如何在 Azure Cosmos DB 適用於 MongoDB 的 API 中使用變更資料流來取得對您資料所做的變更。
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/04/2020
ms.author: rosouz
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9a4e35a235fe6ee6950bd1b4c35cbf9e72ac2893
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359229"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB 適用於 MongoDB 的 API 中的變更資料流
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB 適用於 MongoDB 的 API 中的[變更摘要](change-feed.md)支援，是透過使用變更資料流 API 來提供。 透過使用變更資料流 API，您的應用程式可以取得對集合或單一分區中項目所做的變更。 您稍後可以根據結果採取進一步動作。 系統會依對集合中項目所做變更的修改時間加以擷取，且會針對每個分區索引鍵保證排序次序。

> [!NOTE]
> 若要使用變更資料流，請使用 Azure Cosmos DB 適用於 MongoDB 的 API 的 3.6 版或更新版本。 如果您針對較舊版本執行變更資料流範例，可能會看見 `Unrecognized pipeline stage name: $changeStream` 錯誤。

## <a name="examples"></a>範例

下列範例顯示如何在集合中的所有項目上取得變更資料流。 此範例會建立資料指標，以便在插入、更新或取代項目時加以監看。 需要 `$match` 階段、`$project` 階段及 `fullDocument` 選項來取得變更資料流。 目前不支援使用變更資料流來監看刪除作業。 因應措施是在要刪除的項目上新增軟標記。 例如，您可以在項目中新增名為 "deleted" (已刪除) 的屬性。 當您想要刪除項目時，您可以將項目上的 "deleted" 設為 `true` 並設定 TTL。 由於系統會把將 "deleted" 更新為 `true` 的動作視為更新，此變更將會顯示在變更資料流中。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

下列範例示範如何在 JAVA 中使用變更資料流程功能。如需完整的範例，請參閱此 [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java)存放庫。 此範例也會示範如何使用 `resumeAfter` 方法來搜尋上次讀取的所有變更。 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>單一分區中的變更

下列範例顯示如何取得對單一分區內的項目所做的變更。 此範例會取得分區索引鍵等於 "a" 且分區索引鍵值等於 "1" 的項目變更。 您可以使用不同的用戶端以平行方式讀取來自不同分區的變更。

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>目前的限制

使用變更資料流時有下列限制︰

* 輸出文件中尚未支援 `operationType` 和 `updateDescription` 屬性。
* 目前支援 `insert`、`update` 及 `replace` 作業類型。 但是，尚未支援刪除作業或其他事件。

基於這些限制，需要 $match 階段、$project 階段，以及 fullDocument 選項，如先前範例所示。

和 Azure Cosmos DB 的 SQL API 中的變更摘要不同，並沒有個別的[變更摘要處理器程式庫](change-feed-processor.md)來取用變更資料流或是需要租用容器。 目前並沒有支援使用 [Azure Functions 觸發程序](change-feed-functions.md)來處理變更資料流。

## <a name="error-handling"></a>錯誤處理

使用變更資料流時，支援下列錯誤碼和訊息：

* **HTTP 錯誤碼 16500** - 當系統對變更資料流進行節流時，其會傳回空白頁面。

* **NamespaceNotFound (OperationType 失效)** - 如果您在不存在的集合上，或是在集合已卸除時執行變更資料流，系統便會傳回 `NamespaceNotFound` 錯誤。 因為 `operationType` 屬性無法在輸出文件中傳回，系統不會傳回 `operationType Invalidate` 錯誤，而是會改為傳回 `NamespaceNotFound` 錯誤。

## <a name="next-steps"></a>後續步驟

* [在 Azure Cosmos DB 適用於 MongoDB 的 API 中使用存留時間來使資料自動過期](mongodb-time-to-live.md)
* [Azure Cosmos DB 適用於 MongoDB 的 API 中的索引編製](mongodb-indexing.md)
