---
title: 管理在 Azure Cosmos DB 的 MongoDB API 中的索引
description: 本文概述了使用 MongoDB API 的 Azure Cosmos DB 索引功能。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732715"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>管理在 Azure Cosmos DB 的 MongoDB API 中的索引

Azure Cosmos DB 的蒙戈DB API 利用了 Azure Cosmos DB 的核心索引管理功能。 本文重點介紹如何使用 Azure Cosmos DB 的 MongoDB API 添加索引。 您還可以閱讀 Azure [Cosmos DB 中](index-overview.md)與所有 API 相關的索引概述。

## <a name="indexing-for-mongodb-server-version-36"></a>MongoDB 伺服器版本 3.6 的索引

Azure Cosmos DB 的 MongoDB`_id`伺服器版本 3.6 的 API 自動索引欄位,無法刪除該欄位。 它自動強制每個分片鍵的`_id`欄位的唯一性。

要索引其他欄位,請應用 MongoDB 索引管理命令。 與 MongoDB 一樣,Azure Cosmos DB`_id`的 MongoDB API 僅自動索引字段。 此預設索引策略不同於 Azure Cosmos DB SQL API,後者預設對所有欄位進行索引。

要將排序應用於查詢,必須在排序操作中使用的欄位上創建索引。

## <a name="index-types"></a>索引類型

### <a name="single-field"></a>單欄位

您可以在任何單個字段上創建索引。 單個字段索引的排序順序並不重要。 以下命令在欄位`name`上建立索引:

`db.coll.createIndex({name:1})`

一個查詢使用多個單個字段索引(如果可用)。 每個容器最多可以創建 500 個單欄位索引。

### <a name="compound-indexes-mongodb-server-version-36"></a>複合索引(MongoDB 伺服器版本 3.6)

Azure Cosmos DB 的 MongoDB API 支援使用版本 3.6 線協定的帳戶的複合索引。 複合索引中最多可以包含八個字段。 與 MongoDB 不同,僅當查詢需要同時對多個字段進行有效排序時,才應創建複合索引。 對於具有多個不需要排序的篩選器的查詢,創建多個單個字段索引,而不是單個複合索引。

以下命令在欄位`name`與 上建立複合索`age`引, 然後 :

`db.coll.createIndex({name:1,age:1})`

您可以使用複合索引一次對多個字段進行有效排序,如以下範例所示:

`db.coll.find().sort({name:1,age:1})`

您還可以使用前面的複合索引對所有欄位上具有相反排序順序的查詢進行有效排序。 以下是範例：

`db.coll.find().sort({name:-1,age:-1})`

但是,複合索引中的路徑序列必須與查詢完全匹配。 下面是需要附加複合索引的查詢範例:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>多鍵索引

Azure Cosmos DB 創建多鍵索引來索引存儲在陣列中的內容。 如果使用陣列值對欄位編制索引,則 Azure Cosmos DB 會自動為陣列中的每個元素編制索引。

### <a name="geospatial-indexes"></a>地理空間索引

許多地理空間運算元將受益於地理空間索引。 目前,Azure Cosmos DB 的蒙`2dsphere`戈DB API 支援索引。 API 尚不`2d`支援 索引。

下面是在欄位上建立地理空間索引的範例`location`:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>文字索引

Azure Cosmos DB 的蒙戈DB API 當前不支援文本索引。 對於字串上的文本搜索查詢,應使用[Azure 認知搜索](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb)與 Azure Cosmos DB 整合。

## <a name="index-properties"></a>索引屬性

以下操作對於提供有線協定版本 3.6 的帳戶和提供早期版本的帳戶很常見。 您可以瞭解有關[受支援的索引和索引屬性](mongodb-feature-support-36.md#indexes-and-index-properties)的更多。

### <a name="unique-indexes"></a>唯一索引

[唯一索引](unique-keys.md)可用於強制兩個或多個文檔不包含索引欄位的相同值。

> [!IMPORTANT]
> 僅當集合為空(不包含任何文檔)時,才能創建唯一索引。

以下命令在欄位`student_id`上建立唯一索引:

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

對於分片集合,必須提供分片(分區)鍵才能創建唯一索引。 換句話說，分區化集合上的所有唯一索引都是複合索引，其中有一個欄位是分割區索引鍵。

以下指令```coll```建立分片集合(分片鍵```university```為 ),`student_id`欄位 上具有唯一索`university`引,並且 :

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

在前面的範例中,省略子```"university":1```句返回一個錯誤,其中帶有以下消息:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 索引

要啟用特定集合中的文件過期,您需要建立[一個存取時間 (TTL) 索引](../cosmos-db/time-to-live.md)。 TTL 索引是欄位上具有`_ts`值`expireAfterSeconds`的 索引。

範例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前面的命令刪除```db.coll```集合中最近 10 秒內未修改的任何文檔。

> [!NOTE]
> **_ts**字段特定於 Azure Cosmos DB,並且無法從 MongoDB 用戶端訪問。 它是一個保留(系統)屬性,其中包含文檔上次修改的時間戳。

## <a name="track-index-progress"></a>追蹤索引進度

Azure Cosmos DB 的 MongoDB API 版本 3.6 支援`currentOp()`追蹤資料庫實例上的索引進度 的命令。 此命令返回包含有關資料庫實例上正在進行的操作的文件。 使用`currentOp`該 命令追蹤本機 MongoDB 中的所有正在進行的操作。 在 Azure Cosmos DB 的蒙戈 DB API 中,此命令僅支援跟蹤索引操作。

下面是一些展示如何使用`currentOp`命令 追蹤索引進度的範例:

* 取得集合的索引進度:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* 取得資料庫中所有集合的索引進度:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* 取得 Azure Cosmos 帳號中所有資料庫和集合的索引進度:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>索引進度輸出範例

索引進度詳細信息顯示當前索引操作的進度百分比。 下面是一個範例,該範例顯示了索引進度的不同階段的輸出文檔格式:

- 對「foo」集合和 60% 完成的「bar」資料庫的索引操作將具有以下輸出文檔。 此欄位`Inprog[0].progress.total`顯示 100 作為目標完成百分比。

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

- 如果索引操作剛剛在「foo」集合和「bar」資料庫中啟動,則輸出文檔可能會顯示 0% 的進度,直到達到可衡量的水準。

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

- 當正在進行的索引操作完成時,輸出文檔將顯示`inprog`空 操作。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>背景索引更新

無論為**背景**索引屬性指定的值如何,索引更新始終在後台完成。 由於索引更新使用請求單位 (RUs) 的優先順序低於其他資料庫操作,因此索引更改不會導致寫入、更新或刪除的任何停機時間。

添加新索引時,查詢將立即使用該索引。 這意味著查詢可能不會返回所有匹配的結果,並且不會返回任何錯誤。 索引轉換完成後,查詢結果將一致。 您可以[追蹤索引進度](#track-index-progress)。

## <a name="migrate-collections-with-indexes"></a>使用索引移至集合

目前,只有在集合不包含文檔時,才能創建唯一索引。 流行的 MongoDB 遷移工具嘗試在導入數據後創建唯一索引。 若要規避此問題,可以手動創建相應的集合和唯一索引,而不是允許嘗試遷移工具。 (您可以通過在命令列中```mongorestore````--noIndexRestore`使用 標誌來實現此行為。

## <a name="indexing-for-mongodb-version-32"></a>MongoDB 版本 3.2 的索引

對於與 MongoDB 有線協定版本 3.2 相容的 Azure Cosmos 帳戶,可用的索引功能和預設值是不同的。 您可以[檢查您的帳號版本](mongodb-feature-support-36.md#protocol-support)。 您可以通過提交[支援請求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升級到 3.6 版本。

如果您使用的是版本 3.2,本節概述了版本 3.6 的關鍵區別。

### <a name="dropping-default-indexes-version-32"></a>移除預設索引(版本 3.2)

與 3.6 版本的 Azure Cosmos DB 的 MongoDB API 不同,版本 3.2 預設索引每個屬性。 使用以下指令移除集合的預設索引 (```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

刪除預設索引後,可以像在版本 3.6 中添加更多索引。

### <a name="compound-indexes-version-32"></a>複合索引(版本 3.2)

複合索引可存放文件中多個欄位的參考。 如果要創建複合索引,請通過提交[支援請求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升級到版本 3.6。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
