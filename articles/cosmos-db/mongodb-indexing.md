---
title: Azure Cosmos DB 的 MongoDB 版 API 中的索引編製
description: 對於 Azure Cosmos DB 的 MongoDB 版 API 中的索引功能提供概觀。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/27/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 7c75f0d6f74fe8cf1417e0dc40a5ad01615d7057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371066"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的 MongoDB 版 API 進行索引編製

Azure Cosmos DB 的蒙戈DB API 利用了 Azure Cosmos DB 的核心索引管理功能。 本文檔重點介紹如何使用 Azure Cosmos DB 的 MongoDB API 添加索引。 您還可以閱讀 Azure [Cosmos DB 中](index-overview.md)與所有 API 相關的索引概述。

## <a name="indexing-for-version-36"></a>版本 3.6 的索引

該`_id`欄位始終自動編制索引，不能刪除。 Azure Cosmos DB 的 MongoDB API 自動強制每個`_id`分片鍵的欄位的唯一性。

要索引其他欄位，應應用 MongoDB 索引管理命令。 與 MongoDB 中一`_id`樣，只有欄位自動編制索引。 此預設索引策略不同于 Azure Cosmos DB SQL API，後者預設對所有欄位進行索引。

要將排序應用於查詢，必須在排序操作中使用的欄位上創建索引。

## <a name="index-types"></a>索引類型

### <a name="single-field"></a>單欄位

您可以在任何單個欄位上創建索引。 單個欄位索引的排序次序並不重要。 以下命令將在欄位`name`上創建索引：

`db.coll.createIndex({name:1})`

一個查詢將使用多個單欄位索引（如果可用）。

### <a name="compound-indexes-36"></a>複合指數 （3.6）

對於使用 3.6 線協定的帳戶，支援複合索引。 複合索引中最多可以包含 8 個欄位。 與 MongoDB 不同，僅當查詢需要同時對多個欄位進行有效排序時，才應創建複合索引。 對於具有多個篩選器不需要排序的查詢，應創建多個單個欄位索引，而不是單個複合索引。

以下命令將在欄位`name`上創建複合索引，並`age`：

`db.coll.createIndex({name:1,age:1})`

複合索引可用於一次對多個欄位進行有效排序，例如：

`db.coll.find().sort({name:1,age:1})`

上述複合索引還可用於對所有欄位上具有相反排序次序的查詢進行有效排序。 以下是範例：

`db.coll.find().sort({name:-1,age:-1})`

但是，複合索引中的路徑序列必須與查詢完全符合。 下面是需要附加複合索引的查詢示例：

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>多鍵索引

Azure Cosmos DB 創建多鍵索引來索引存儲在陣列中的內容。 如果使用陣列值對欄位編制索引，則 Azure Cosmos DB 會自動為數組中的每個元素編制索引。

### <a name="geospatial-indexes"></a>地理空間索引

許多地理空間運算子將受益于地理空間索引。 目前，Azure Cosmos DB 的蒙戈DB `2dsphere` API 支援索引。 `2d`尚不支援索引。

下面是在欄位上創建地理空間索引的示例`location`：

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>文本索引

當前不支援文本索引。 對於字串上的文本搜索查詢，應使用[Azure 認知搜索](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb)與 Azure Cosmos DB 的集成。

## <a name="index-properties"></a>索引屬性

以下操作對於提供有線協定版本 3.6 的帳戶和提供早期有線協定版本的帳戶都很常見。 您還可以瞭解有關[受支援的索引和索引屬性](mongodb-feature-support-36.md#indexes-and-index-properties)的更多。

### <a name="unique-indexes"></a>唯一索引

[唯一索引](unique-keys.md)適合用於強制不會有任何兩份或多份文件包含相同的索引欄位值。

>[!Important]
> 僅當集合為空（不包含任何文檔）時，才能創建唯一索引。

下列命令可在 "student_id" 欄位上建立唯一索引：

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

對於分片集合，創建唯一索引需要提供分片（分區）鍵。 換句話說，分區化集合上的所有唯一索引都是複合索引，其中有一個欄位是分割區索引鍵。

下列命令會建立分區化集合 ```coll``` (分區索引鍵是 ```university```)，其 student_id 和 university 欄位上有一個唯一索引：

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

在上述範例中，如果省略 ```"university":1``` 子句，則會傳回下列錯誤訊息：

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 索引

若要啟用特定集合中的文件到期，則需建立[TTL 索引 (存留時間索引)](../cosmos-db/time-to-live.md)。 TTL 索引是_ts 欄位上具有 "expireAfterSeconds" 值的索引。

範例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

上述命令會導致 ```db.coll``` 集合中最近 10 秒內未經修改的文件遭到刪除。

> [!NOTE]
> **_ts**是特定于 Azure Cosmos 的特定于 DB 的欄位，無法從 MongoDB 用戶端存取。 這是保留 (系統) 屬性，其中包含上次修改文件的時間戳記。

## <a name="track-the-index-progress"></a>跟蹤索引進度

3.6 版本的 Azure Cosmos DB 的 MongoDB 帳戶`currentOp()`API 支援跟蹤資料庫實例上的索引進度的命令。 此命令返回包含有關資料庫實例上正在進行的操作的資訊的文檔。 該`currentOp`命令用於跟蹤本機 MongoDB 中的所有正在進行的操作，而在 Azure Cosmos DB 的 MongoDB API 中，此命令僅支援跟蹤索引操作。

下面是一些演示如何使用 命令`currentOp`跟蹤索引進度的示例：

* 獲取集合的索引進度：

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* 獲取資料庫中所有集合的索引進度：

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* 獲取 Azure Cosmos 帳戶中所有資料庫和集合的索引進度：

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

索引進度詳細資訊包含當前索引操作的進度百分比。 下面的示例顯示了索引進度不同階段的輸出文檔格式：

1. 如果"foo"集合和具有 60% 索引完成的"bar"資料庫上的索引操作將具有以下輸出文檔。 `Inprog[0].progress.total`將顯示 100 作為目標完成。

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. 對於剛剛開始在"foo"集合和"bar"資料庫上啟動的索引操作，輸出文檔可能會顯示 0% 的進度，直到達到可測量級別。

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. 當正在進行的索引操作完成時，輸出文檔將顯示空的 inprog 操作。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>背景索引更新

無論為**背景**索引屬性指定的值如何，索引更新始終在後臺完成。 索引更新使用 RU 的優先順序低於其他資料庫操作。 因此，索引更改不會導致寫入、更新或刪除的任何停機時間。

添加新索引時，查詢將立即使用它。 這意味著查詢可能不會返回所有匹配的結果，並且不會在不返回任何錯誤的情況下返回。 索引轉換完成後，查詢結果將一致。 您可以[跟蹤索引進度](#track-the-index-progress)。

## <a name="migrating-collections-with-indexes"></a>移轉具有索引的集合

目前，只有在集合不包含任何文件時，才可能建立唯一索引。 熱門 MongoDB 移轉工具會嘗試在匯入資料後建立唯一索引。 為了規避此問題，建議使用者手動創建相應的集合和唯一索引，而不是允許遷移工具（對於```mongorestore```此行為，通過使用命令列中的`--noIndexRestore`標誌來實現）。

## <a name="indexing-for-version-32"></a>版本 3.2 的索引

對於與 3.2 版本的 MongoDB 有線協定相容的 Azure Cosmos DB 帳戶，可用的索引功能和預設值是不同的。 您可以[檢查您的帳戶版本](mongodb-feature-support-36.md#protocol-support)。 您可以通過提交[支援請求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升級到 3.6 版本。

如果您使用的是版本 3.2，本節概述了版本 3.6 的關鍵區別。

### <a name="dropping-the-default-indexes-32"></a>刪除預設索引 （3.2）

與 3.6 版本的 Azure Cosmos DB 的 MongoDB API 不同，3.2 版本預設索引每個屬性。 以下命令可用於刪除集合```coll```的這些預設索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

刪除預設索引後，可以添加其他索引，如版本 3.6 中所做的那樣。

### <a name="compound-indexes-32"></a>複合指數 （3.2）

複合索引可存放文件中多個欄位的參考。 如果要創建複合索引，請通過提交[支援請求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升級到 3.6 版本。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
