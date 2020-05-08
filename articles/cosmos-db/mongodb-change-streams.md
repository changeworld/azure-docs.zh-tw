---
title: 變更 Azure Cosmos DB 的 MongoDB API 中的資料流程
description: 瞭解如何在 Azure Cosmos DB 適用于 MongoDB 的 API 中使用變更串流，以取得對您的資料所做的變更。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 7a6060448175530ada5ba95ceda470056a7be002
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872152"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>變更 Azure Cosmos DB 的 MongoDB API 中的資料流程

您可以使用變更資料流程 API，取得 Azure Cosmos DB 的 MongoDB API 中的[變更](change-feed.md)摘要支援。 藉由使用變更資料流程 API，您的應用程式可以取得對集合或單一分區中的專案所做的變更。 稍後您可以根據結果採取進一步的動作。 集合中的專案變更會依照其修改時間順序來捕捉，而排序次序則是依據分區索引鍵來保證。

> [!NOTE]
> 若要使用變更資料流程，請建立3.6 版 Azure Cosmos DB 適用于 MongoDB 的 API 或更新版本的帳戶。 如果您針對較舊的版本執行變更資料流程範例，可能會看到`Unrecognized pipeline stage name: $changeStream`錯誤。

## <a name="current-limitations"></a>目前的限制

使用變更資料流程時，適用下列限制：

* 輸出`operationType`檔`updateDescription`中尚未支援和屬性。
* 目前`insert`支援`update`、和`replace`作業類型。 
* 尚未支援刪除作業或其他事件。

由於這些限制，因此需要 $match 階段、$project 階段和 fullDocument 選項，如先前範例所示。

不同于 Azure Cosmos DB 的 SQL API 中的變更摘要，沒有個別的[變更摘要處理器程式庫](change-feed-processor.md)可取用變更串流或需要租用容器。 目前不支援[Azure Functions 觸發](change-feed-functions.md)程式來處理變更資料流程。

## <a name="error-handling"></a>錯誤處理

使用變更資料流程時，支援下列錯誤碼和訊息：

* **HTTP 錯誤碼 16500** -當變更資料流程受到節流處理時，它會傳回空白頁面。

* **NamespaceNotFound （OperationType 無效）** -如果您在不存在的集合上執行變更資料流程，或集合已卸載，則會傳回`NamespaceNotFound`錯誤。 因為無法`operationType`在輸出檔案中傳回屬性，而不是`operationType Invalidate`錯誤，所以會傳回`NamespaceNotFound`錯誤。

## <a name="examples"></a>範例

下列範例顯示如何取得集合中所有專案的變更資料流程。 這個範例會建立一個資料指標，以便在插入、更新或取代專案時加以監看。 若`$match`要取得`$project`變更資料流程， `fullDocument`必須要有階段、階段和選項。 目前不支援使用變更資料流程來監看刪除作業。 因應措施是，您可以在要刪除的專案上新增軟標記。 例如，您可以在名為「已刪除」的專案中新增屬性。 當您想要刪除專案時，可以將「已刪除」設定為`true` ，並在專案上設定 TTL。 因為將「已刪除」 `true`更新為更新，所以這項變更將會顯示在變更資料流程中。

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

## <a name="changes-within-a-single-shard"></a>單一分區中的變更

下列範例顯示如何在單一分區中取得專案的變更。 這個範例會取得分區索引鍵等於 "a" 且分區索引鍵值等於 "1" 之專案的變更。 可以讓不同的用戶端以平行方式從不同的分區讀取變更。

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

## <a name="next-steps"></a>後續步驟

* [使用存留時間以在 Azure Cosmos DB 的 MongoDB API 中自動使資料過期](mongodb-time-to-live.md)
* [Azure Cosmos DB 的 MongoDB 版 API 中的索引編製](mongodb-indexing.md)
