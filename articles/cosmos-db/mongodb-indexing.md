---
title: Azure Cosmos DB 的 MongoDB 版 API 中的索引編製
description: 對於 Azure Cosmos DB 的 MongoDB 版 API 中的索引功能提供概觀。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029464"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的 MongoDB 版 API 進行索引編製

Azure Cosmos DB 的 MongoDB 版 API 會運用 Cosmos DB 的自動索引管理功能。 因此，使用者可以存取 Cosmos DB 的預設索引原則。 所以，如果使用者未定義任何索引，或未置放任何索引，則所有欄位在插入至集合時，預設都會自動編製索引。 在大部分情況下，我們建議使用帳戶上設定的預設索引原則。

## <a name="indexing-for-version-36"></a>3\.6 版的索引

服務線路通訊協定版本3.6 的帳戶，提供的預設索引編制原則與舊版所提供的原則不同。 根據預設，只有 [_id] 欄位會編制索引。 若要為其他欄位編制索引，使用者必須套用 MongoDB 索引管理命令。 若要將排序套用至查詢，目前必須在排序作業中使用的欄位上建立索引。

### <a name="dropping-the-default-indexes-36"></a>卸載預設索引（3.6）

針對服務線路通訊協定3.6 版的帳戶，唯一的預設索引是 _id，無法卸載。

### <a name="creating-a-compound-index-36"></a>建立複合索引（3.6）

使用3.6 連線通訊協定的帳戶支援真正的複合索引。 下列命令會在欄位 ' a ' 和 ' b ' 上建立複合索引： `db.coll.createIndex({a:1,b:1})`

複合索引可以用來一次在多個欄位上有效率地進行排序，例如： `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>追蹤索引進度

3\.6 版的 Azure Cosmos DB 的 MongoDB API 帳戶支援 `currentOp()` 命令，以追蹤資料庫實例上的索引進度。 此命令會傳回一份檔，其中包含資料庫實例上進行中作業的相關資訊。 `currentOp` 命令是用來追蹤原生 MongoDB 中所有進行中的作業，而在 Azure Cosmos DB 適用于 MongoDB 的 API 中，此命令只支援追蹤索引操作。

以下是一些範例，示範如何使用 `currentOp` 命令來追蹤索引進度：

•取得集合的索引進度：

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

•取得資料庫中所有集合的索引進度：

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

•取得 Azure Cosmos 帳戶中所有資料庫和集合的索引進度：

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

索引進度詳細資料包含目前索引作業的進度百分比。 下列範例顯示索引進度的不同階段的輸出檔案格式：

1. 如果 ' foo ' 集合上的索引作業和具有60% 索引編制的 ' bar ' 資料庫已完成，則會有下列輸出檔案。 `Inprog[0].progress.total` 顯示100做為目標完成。

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

2. 對於剛在 ' foo ' 集合和 ' bar ' 資料庫上啟動的索引作業，輸出檔案可能會顯示0% 的進度，直到達到可測量的層級為止。

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

3. 當進行中的索引作業完成時，輸出檔案會顯示空白的 inprog 作業。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>3\.2 版的索引

### <a name="dropping-the-default-indexes-32"></a>卸載預設索引（3.2）

下列命令可用來置放 ```coll``` 集合的預設索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>建立複合索引（3.2）

複合索引可存放文件中多個欄位的參考。 就邏輯而言，它們等同於為每個欄位建立多個個別索引。 若要利用 Cosmos DB 索引技術所提供的最佳化功能，建議您建立多個個別索引，而不是單一 (非唯一) 複合索引。

## <a name="common-indexing-operations"></a>一般索引作業

下列作業適用于服務線路通訊協定3.6 版的帳戶，以及服務較早的網路通訊協定版本的帳戶。 

## <a name="creating-unique-indexes"></a>建立唯一索引

[唯一索引](unique-keys.md)適合用於強制不會有任何兩份或多份文件包含相同的索引欄位值。

>[!Important]
> 目前，只有在集合是空的 (不包含任何文件) 時，才能建立唯一索引。

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

如果是分區化集合，依據 MongoDB 行為，建立唯一索引時需要提供分區化 (分割區) 索引鍵。 換句話說，分區化集合上的所有唯一索引都是複合索引，其中有一個欄位是分割區索引鍵。

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

## <a name="ttl-indexes"></a>TTL 索引

若要啟用特定集合中的文件到期，則需建立[TTL 索引 (存留時間索引)](../cosmos-db/time-to-live.md)。 TTL 索引是_ts 欄位上具有 "expireAfterSeconds" 值的索引。

範例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

上述命令會導致 ```db.coll``` 集合中最近 10 秒內未經修改的文件遭到刪除。

> [!NOTE]
> **_ts** 是 Cosmos DB 特有欄位，且無法從 MongoDB 用戶端存取。 這是保留 (系統) 屬性，其中包含上次修改文件的時間戳記。

## <a name="migrating-collections-with-indexes"></a>移轉具有索引的集合

目前，只有在集合不包含任何文件時，才可能建立唯一索引。 熱門 MongoDB 移轉工具會嘗試在匯入資料後建立唯一索引。 若要避免這個問題，建議使用者手動建立對應的集合和唯一的索引，而不要允許遷移工具（適用于 ```mongorestore``` 這項行為是透過在命令列中使用 `--noIndexRestore` 旗標來達成）。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
