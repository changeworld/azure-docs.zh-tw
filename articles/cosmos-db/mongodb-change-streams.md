---
title: 變更 Azure 宇宙 DB MongoDB 的 API 中的串流
description: 瞭解如何在 Azure Cosmos DB 的蒙高 DB API 中使用更改流來獲取對數據所做的更改。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: ecfa98241f74aac43a827b645a6ed877624d643d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437813"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>變更 Azure 宇宙 DB MongoDB 的 API 中的串流

使用更改流 API 可在 Azure Cosmos DB 的蒙戈 DB API 中[更改來源](change-feed.md)支援。 通過使用更改流 API,應用程式可以獲取對集合或單個分片中的項所做的更改。 稍後,您可以根據結果執行進一步操作。 對集合中的項的更改按其修改時間的順序捕捉,並且每個分片鍵都保證排序順序。

> [!NOTE]
> 要使用更改流,請使用 Azure Cosmos DB 的 MongoDB API 版本 3.6 或更高版本創建帳戶。 如果針對早期版本執行變更流範例,您可能會看到錯誤`Unrecognized pipeline stage name: $changeStream`。

## <a name="current-limitations"></a>目前的限制

使用變更串流時,以下限制適用:

* 輸出`operationType`文件`updateDescription`中 尚不支援和 屬性。
* 當前`insert`支援`update`和`replace`操作類型。 尚不支援刪除操作或其他事件。

由於這些限制,需要$match階段、$project階段和完整文檔選項,如前面的示例所示。

與 Azure Cosmos DB 的 SQL API 中的更改來源不同,沒有單獨的[更改饋送處理器庫](change-feed-processor.md)來使用更改流或需要租約容器。 當前不支援[Azure 函數觸發器](change-feed-functions.md)來處理更改流。

## <a name="error-handling"></a>錯誤處理

使用變更串流時,支援以下錯誤碼和訊息:

* **HTTP 錯誤代碼 16500** - 當更改流被限制時,它將返回一個空頁。

* **命名空間找不到(操作型態無效)** ─如果不存在的集合上執行變更流,或者如果刪除集合,則傳回`NamespaceNotFound`錯誤 。 由於無法`operationType`在輸出文檔中返回該屬性,`operationType Invalidate``NamespaceNotFound`因此傳回錯誤不是錯誤。

## <a name="examples"></a>範例

下面的範例展示如何獲取集合中所有項的更改流。 本示例創建一個游標,用於在插入、更新或替換專案時監視它們。 需要`$match`階段、`$project`階段和`fullDocument`選項才能獲取更改流。 當前不支援使用更改流監視刪除操作。 作為解決方法,您可以在要刪除的項上添加軟標記。 例如,您可以在名為"已刪除"的項中添加屬性。 如果要刪除該專案,可以將「已刪除」設定為`true`, 並在該專案上設置 TTL。 由於將「刪除」更新為`true`更新,因此此更改將在更改流中可見。

### <a name="javascript"></a>JavaScript：

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

### <a name="c"></a>C#：

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

## <a name="changes-within-a-single-shard"></a>單個分片中的變更

下面的範例展示如何在單個分片中獲取項的更改。 此示例獲取分片鍵等於"a"和分片鍵值等於"1"的項的更改。 可以讓不同的用戶端並行讀取不同分片的更改。

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

* [使用時間即時使蒙高DB的 Azure Cosmos DB API 中的資料自動過期](mongodb-time-to-live.md)
* [Azure Cosmos DB 的 MongoDB 版 API 中的索引編製](mongodb-indexing.md)
