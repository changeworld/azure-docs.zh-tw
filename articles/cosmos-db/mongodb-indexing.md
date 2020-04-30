---
title: 管理 Azure Cosmos DB 的 MongoDB API 中的索引編制
description: 本文概述使用 MongoDB API 的 Azure Cosmos DB 索引功能。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732715"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>管理 Azure Cosmos DB 的 MongoDB API 中的索引編制

Azure Cosmos DB 適用于 MongoDB 的 API 會利用 Azure Cosmos DB 的核心索引管理功能。 本文著重于如何使用 Azure Cosmos DB 適用于 MongoDB 的 API 來新增索引。 您也可以閱讀在與所有 Api 相關的[Azure Cosmos DB 中編制索引的總覽](index-overview.md)。

## <a name="indexing-for-mongodb-server-version-36"></a>MongoDB 伺服器3.6 版的索引

Azure Cosmos DB 適用于 MongoDB 的 API 伺服器3.6 版會自動`_id`為無法卸載的欄位編制索引。 它會自動強制執行每個`_id`分區索引鍵的欄位唯一性。

若要為其他欄位編制索引，請套用 MongoDB 索引管理命令。 如同在 MongoDB 中，Azure Cosmos DB 的適用于 MongoDB 的 API `_id`只會自動為欄位編制索引。 此預設索引編制原則與 Azure Cosmos DB SQL API 不同，預設會為所有欄位編制索引。

若要將排序套用至查詢，您必須在排序作業中使用的欄位上建立索引。

## <a name="index-types"></a>索引類型

### <a name="single-field"></a>單一欄位

您可以在任何單一欄位上建立索引。 單一欄位索引的排序次序並不重要。 下列命令會在欄位`name`上建立索引：

`db.coll.createIndex({name:1})`

一個查詢會使用多個單一欄位索引（如果有的話）。 您最多可以為每個容器建立500單一欄位索引。

### <a name="compound-indexes-mongodb-server-version-36"></a>複合索引（MongoDB 伺服器版本3.6）

Azure Cosmos DB 的適用于 MongoDB 的 API 支援使用3.6 線路通訊協定版本之帳戶的複合索引。 複合索引中最多可以包含八個欄位。 不同于 MongoDB，只有在您的查詢需要一次在多個欄位上有效率地排序時，才應該建立複合索引。 對於具有多個篩選準則而不需要排序的查詢，請建立多個單一欄位索引，而不是單一複合索引。

下列命令會在欄位`name`和`age`上建立複合索引：

`db.coll.createIndex({name:1,age:1})`

您可以使用複合索引一次在多個欄位上有效率地進行排序，如下列範例所示：

`db.coll.find().sort({name:1,age:1})`

您也可以使用上述的複合索引，有效率地排序在所有欄位上具有相反排序次序的查詢。 範例如下：

`db.coll.find().sort({name:-1,age:-1})`

不過，複合索引中的路徑順序必須完全符合查詢。 以下是需要額外複合索引的查詢範例：

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Multikey 索引

Azure Cosmos DB 會建立 multikey 索引，以索引儲存在陣列中的內容。 如果您使用陣列值來索引欄位，Azure Cosmos DB 會自動為數組中的每個元素編制索引。

### <a name="geospatial-indexes"></a>地理空間索引

許多地理空間索引可受益于地理空間索引。 目前，Azure Cosmos DB 適用于 MongoDB 的 API `2dsphere`支援索引。 API 尚不支援`2d`索引。

以下是在`location`欄位上建立地理空間索引的範例：

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>文字索引

Azure Cosmos DB 適用于 MongoDB 的 API 目前不支援文字索引。 針對字串的文字搜尋查詢，您應該使用[Azure 認知搜尋](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb)與 Azure Cosmos DB 整合。

## <a name="index-properties"></a>索引屬性

下列作業適用于服務線路通訊協定版本3.6 的帳戶，以及服務較早版本的帳戶。 您可以深入瞭解[支援的索引和索引屬性](mongodb-feature-support-36.md#indexes-and-index-properties)。

### <a name="unique-indexes"></a>唯一索引

[唯一索引](unique-keys.md)適用于強制執行兩個以上的檔，而不包含索引欄位的相同值。

> [!IMPORTANT]
> 只有在集合是空的（不包含任何檔）時，才能建立唯一索引。

下列命令會在欄位`student_id`上建立唯一索引：

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

對於分區化集合，您必須提供分區（分割區）金鑰來建立唯一索引。 換句話說，分區化集合上的所有唯一索引都是複合索引，其中有一個欄位是分割區索引鍵。

```coll```下列命令會使用欄位```university``` `student_id`上的唯一索引來建立分區化集合（分區索引鍵為）： `university`

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

在上述範例中，省略```"university":1```子句會傳回錯誤，並顯示下列訊息：

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 索引

若要在特定集合中啟用檔到期日，您必須建立生存[時間（TTL）索引](../cosmos-db/time-to-live.md)。 TTL 索引是`_ts`欄位上具有`expireAfterSeconds`值的索引。

範例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

上述命令會刪除集合中過去 10 ```db.coll```秒內尚未修改的任何檔。

> [!NOTE]
> [ **_Ts** ] 欄位專屬於 Azure Cosmos DB，而且無法從 MongoDB 用戶端存取。 這是保留（系統）屬性，其中包含上次修改檔的時間戳記。

## <a name="track-index-progress"></a>追蹤索引進度

Azure Cosmos DB 適用于 MongoDB 的 API 版本3.6 支援用`currentOp()`來追蹤資料庫實例上索引進度的命令。 此命令會傳回一份檔，其中包含資料庫實例上進行中作業的相關資訊。 您可以使用`currentOp`命令來追蹤原生 MongoDB 中所有進行中的作業。 在 Azure Cosmos DB 的 MongoDB 版 API 中，此命令僅支援追蹤索引作業。

以下是一些範例，說明如何使用`currentOp`命令來追蹤索引進度：

* 取得集合的索引進度：

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* 取得資料庫中所有集合的索引進度：

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* 取得 Azure Cosmos 帳戶中所有資料庫和集合的索引進度：

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>索引進度輸出的範例

索引進度詳細資料會顯示目前索引作業的進度百分比。 以下範例顯示索引進度的不同階段的輸出檔案格式：

- 在60% 完成的 "foo" 集合和 "bar" 資料庫上進行索引作業，將會有下列輸出檔案。 `Inprog[0].progress.total`欄位會顯示100做為目標完成百分比。

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

- 如果索引作業剛在「foo」集合和「bar」資料庫上啟動，輸出檔案可能會顯示百分之0的進度，直到達到可測量的層級為止。

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

- 當進行中的索引作業完成時，輸出檔案會顯示空白`inprog`作業。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>背景索引更新

不論為 [**背景**索引] 屬性指定的值為何，索引更新一律會在背景中完成。 因為索引更新會使用比其他資料庫作業低的優先順序來取用要求單位（ru），所以索引變更不會導致寫入、更新或刪除的任何停機時間。

當您加入新的索引時，查詢會立即使用索引。 這表示查詢可能不會傳回所有相符的結果，因此不會傳回任何錯誤。 當索引轉換完成時，查詢結果會是一致的。 您可以[追蹤索引進度](#track-index-progress)。

## <a name="migrate-collections-with-indexes"></a>使用索引來遷移集合

目前，當集合未包含任何檔時，您只能建立唯一的索引。 熱門的 MongoDB 遷移工具會在匯入資料之後，嘗試建立唯一的索引。 若要避免這個問題，您可以手動建立對應的集合和唯一索引，而不是允許遷移工具嘗試。 （您可以在命令列中```mongorestore```使用`--noIndexRestore`旗標，來達到的這項行為）。

## <a name="indexing-for-mongodb-version-32"></a>為 MongoDB 版本3.2 編制索引

適用于 Azure Cosmos 帳戶的可用索引編制功能和預設值，與 MongoDB 有線通訊協定版本3.2 相容。 您可以[檢查您的帳戶版本](mongodb-feature-support-36.md#protocol-support)。 您可以藉由提出[支援要求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來升級至3.6 版。

如果您使用的是3.2 版，本節會概述3.6 版的主要差異。

### <a name="dropping-default-indexes-version-32"></a>卸載預設索引（版本3.2）

不同于適用于 MongoDB 的3.6 版 Azure Cosmos DB API，版本3.2 預設會編制每個屬性的索引。 您可以使用下列命令來卸載集合的這些預設索引（```coll```）：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

卸載預設索引之後，您可以新增更多索引，就像在3.6 版中所做的一樣。

### <a name="compound-indexes-version-32"></a>複合索引（版本3.2）

複合索引可存放文件中多個欄位的參考。 如果您想要建立複合索引，請[提出支援要求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以升級至3.6 版。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
