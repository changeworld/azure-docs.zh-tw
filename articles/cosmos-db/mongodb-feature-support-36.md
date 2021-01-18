---
title: 適用於 MongoDB (3.6 版) 的 Azure Cosmos DB API 支援的功能和語法
description: 了解適用於 MongoDB (3.6 版) 的 Azure Cosmos DB API 支援的功能和語法。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/07/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 0ca1f1222881a2b4ca640fa31192bd1c151ebd9f
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028840"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>適用於 MongoDB (3.6 版) 的 Azure Cosmos DB API：支援的功能和語法
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以使用任何開放原始碼 MongoDB 用戶端[驅動程式](https://docs.mongodb.org/ecosystem/drivers)與適用於 MongoDB 的 Azure Cosmos DB API 通訊。 Azure Cosmos DB 適用於 MongoDB 的 API 需遵循 MongoDB [有線通訊協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)，才能使用現有的用戶端驅動程式。

藉由使用 Azure Cosmos DB 適用於 MongoDB 的 API，您除了可享有慣用的 MongoDB 權益外，還可使用 Cosmos DB 提供的所有企業功能：[全域發佈](distribute-data-globally.md)、[自動分區](partitioning-overview.md)、可用性和延遲保證、待用加密、備份等功能。

## <a name="protocol-support"></a>通訊協定支援

針對新帳戶，Azure Cosmos DB 的 MongoDB 版 API 依預設會與 MongoDB 伺服器 **3.6** 版相容。 以下列出支援的運算子及任何限制或例外狀況。 任何能解析這些通訊協定的用戶端驅動程式，都應該能夠連線到 Azure Cosmos DB 適用於 MongoDB 的 API。 請注意，使用適用於 MongoDB 的 Azure Cosmos DB API 帳戶時，3.6 版的帳戶會具有 `*.mongo.cosmos.azure.com` 格式的端點，而 3.2 版的帳戶則具有 `*.documents.azure.com` 格式的端點。

## <a name="query-language-support"></a>查詢語言支援

Azure Cosmos DB 適用於 MongoDB 的 API 可完整支援 MongoDB 查詢語言結構。 下列各節顯示 Azure Cosmos DB 目前支援的伺服器作業、運算子、階段、命令和選項詳細清單。

> [!NOTE]
> 本文只會列出支援的伺服器命令，並排除用戶端包裝函式。 `deleteMany()` 和 `updateMany()` 等用戶端包裝函式會在內部利用 `delete()` 和 `update()` 伺服器命令。 利用支援伺服器命令的函式與適用於 MongoDB 的 Azure Cosmos DB API 相容。

## <a name="database-commands"></a>資料庫命令

Azure Cosmos DB 適用於 MongoDB 的 API 支援下列資料庫命令：

### <a name="query-and-write-operation-commands"></a>查詢和寫入作業命令

|Command  |支援 |
|---------|---------|
|delete | 是 |
|尋找 | 是     |
|findAndModify | 是  |
|getLastError|   是 |
|getMore  |  是  |
|getPrevError | 否  |
|insert  |   是  |
|parallelCollectionScan  | 是   |
|resetError |    否  |
|update  |   是  |
|[變更資料流](mongodb-change-streams.md)  |  是  |
|GridFS |   是  |

### <a name="authentication-commands"></a>驗證命令

|Command  |支援 |
|---------|---------|
|authenticate    |   是      |
|logout    |      是   |
|getnonce   |    是     |


### <a name="administration-commands"></a>系統管理命令

|Command  |支援 |
|---------|---------|
|受限的集合   |   否      |
|cloneCollectionAsCapped     |   否      |
|collMod     |   否      |
|collMod: expireAfterSeconds   |   否      |
|convertToCapped   |  否       |
|copydb     |  否       |
|建立   |    是     |
|createIndexes     |  是       |
|currentOp     |  是       |
|drop     |   是      |
|dropDatabase     |  是       |
|dropIndexes     |   是      |
|filemd5    |   是      |
|killCursors    |  是       |
|killOp     |   否      |
|listCollections     |  是       |
|listDatabases     |  是       |
|listIndexes     |  是       |
|reIndex     |    是     |
|renameCollection     |    否     |
|connectionStatus    |     否    |

### <a name="diagnostics-commands"></a>診斷命令

|Command  |支援 |
|---------|---------|
|buildInfo         |   是      |
|collStats    |  是       |
|connPoolStats     |  否       |
|connectionStatus     |  否       |
|dataSize     |   否      |
|dbHash    |    否     |
|dbStats     |   是      |
|explain     |   是      |
|explain: executionStats     |   是      |
|特性     |    否     |
|hostInfo     |   否      |
|listDatabases         |   是      |
|listCommands     |  否       |
|profiler     |  否       |
|serverStatus     |  否       |
|top     |    否     |
|whatsmyuri     |   是      |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>彙總管線 </a>

### <a name="aggregation-commands"></a>彙總命令

|Command  |支援 |
|---------|---------|
|彙總 (aggregate) |   是  |
|count     |   是  |
|distinct  | 是 |
|mapReduce | 否 |

### <a name="aggregation-stages"></a>彙總階段

|Command  |支援 |
|---------|---------|
|$collStats    |否|
|$project    |是|
|$match    |是|
|$redact|    是|
|$limit    |是|
|$skip    |是|
|$unwind|    是|
|$group    |    是|
|$sample|        是|
|$sort    |是|
|$geoNear|    否|
|$lookup    |    是|
|$out        |是|
|$indexStats|        否|
|$facet    |是|
|$bucket|    否|
|$bucketAuto|    否|
|$sortByCount|    是|
|$addFields    |是|
|$replaceRoot|    是|
|$count    |是|
|$currentOp|    否|
|$listLocalSessions    |否|
|$listSessions    |否|
|$graphLookup    |是|

### <a name="boolean-expressions"></a>布林運算式

|Command  |支援 |
|---------|---------|
|$and| 是|
|$or|是|
|$not|是|

### <a name="set-expressions"></a>Set expressions

|Command  |支援 |
|---------|---------|
| $setEquals | 是|
|$setIntersection|是|
| $setUnion|是|
| $setDifference|是|
| $setIsSubset|是|
| $anyElementTrue|是|
| $allElementsTrue|是|

### <a name="comparison-expressions"></a>比較運算式

|Command  |支援 |
|---------|---------|
|$cmp     |  是       |
|$eq|    是| 
|$gt |    是| 
|$gte|    是| 
|$lt    |是|
|$lte|    是| 
|$ne    |    是| 
|$in    |    是| 
|$nin    |    是| 

### <a name="arithmetic-expressions"></a>算術運算式

|Command  |支援 |
|---------|---------|
|$abs |  是       |
| $add |  是       |
| $ceil |  是       |
| $divide |  是       |
| $exp |  是       |
| $floor |  是       |
| $ln |  是       |
| $log |  是       |
| $log10 |  是       |
| $mod |  是       |
| $multiply |  是       |
| $pow |  是       |
| $sqrt |  是       |
| $subtract |  是       |
| $trunc |  是       |

### <a name="string-expressions"></a>字串運算式

|Command  |支援 |
|---------|---------|
|$concat |  是       |
| $indexOfBytes|  是       |
| $indexOfCP|  是       |
| $split|  是       |
| $strLenBytes|  是       |
| $strLenCP|  是       |
| $strcasecmp|  是       |
| $substr|  是       |
| $substrBytes|  是       |
| $substrCP|  是       |
| $toLower|  是       |
| $toUpper|  是       |

### <a name="text-search-operator"></a>文字搜尋運算子

|Command  |支援 |
|---------|---------|
| $meta | 否|

### <a name="array-expressions"></a>陣列運算式

|Command  |支援 |
|---------|---------|
|$arrayElemAt    |    是|
|$arrayToObject|    是|
|$concatArrays    |    是|
|$filter    |    是|
|$indexOfArray    |是|
|$isArray    |    是|
|$objectToArray    |是|
|$range    |是|
|$reverseArray    |    是|
|$reduce|    是|
|$size    |    是|
|$slice    |    是|
|$zip    |    是|
|$in    |    是|

### <a name="variable-operators"></a>變數運算子

|Command  |支援 |
|---------|---------|
|$map    |否|
|$let    |是|

### <a name="system-variables"></a>系統變數

|Command  |支援 |
|---------|---------|
|$$CURRENT|    是|
|$$DESCEND|        是|
|$$KEEP        |是|
|$$PRUNE    |    是|
|$$REMOVE    |是|
|$$ROOT        |是|

### <a name="literal-operator"></a>常值運算子

|Command  |支援 |
|---------|---------|
|$literal    |是|

### <a name="date-expressions"></a>日期運算式

|Command  |支援 |
|---------|---------|
|$dayOfYear    |是    |
|$dayOfMonth|    是    |
|$dayOfWeek    |是    |
|$year    |是    |
|$month    |是|    
|$week    |是    |
|$hour    |是    |
|$minute|    是|    
|$second    |是    |
|$millisecond|    是|    
|$dateToString    |是    |
|$isoDayOfWeek    |是    |
|$isoWeek    |是    |
|$dateFromParts|    否|    
|$dateToParts    |否    |
|$dateFromString|    否|
|$isoWeekYear    |是    |

### <a name="conditional-expressions"></a>條件運算式

|Command  |支援 |
|---------|---------|
| $cond| 是|
| $ifNull| 是|
| $switch |是|

### <a name="data-type-operator"></a>資料類型運算子

|Command  |支援 |
|---------|---------|
| $type| 是|

### <a name="accumulator-expressions"></a>累加器運算式

|Command  |支援 |
|---------|---------|
|$sum    |是    |
|$avg    |是    |
|$first|    是|
|$last    |是    |
|$max    |是    |
|$min    |是    |
|$push|    是|
|$addToSet|    是|
|$stdDevPop|    否    |
|$stdDevSamp|    否|

### <a name="merge-operator"></a>合併運算子

|Command  |支援 |
|---------|---------|
| $mergeObjects | 是|

## <a name="data-types"></a>資料類型

|Command  |支援 |
|---------|---------|
|Double    |是    |
|String    |是    |
|Object    |是    |
|Array    |是    |
|Binary Data    |是|    
|ObjectId    |是    |
|Boolean    |是    |
|Date    |是    |
|Null    |是    |
|32 位元整數 (int)    |是    |
|時間戳記    |是    |
|64 位元整數 (long)    |是    |
|MinKey    |是    |
|MaxKey    |是    |
|Decimal128    |是|    
|規則運算式    |是|
|JavaScript    |是|
|JavaScript (具範圍)|    是    |
|未定義    |是    |

## <a name="indexes-and-index-properties"></a>索引和索引屬性

### <a name="indexes"></a>索引

|Command  |支援 |
|---------|---------|
|單一欄位索引    |是    |
|複合索引    |是    |
|多重索引鍵索引    |是    |
|文字索引    |否|
|2dsphere    |是    |
|2d 索引    |否    |
|雜湊索引    | 是|

### <a name="index-properties"></a>索引屬性

|Command  |支援 |
|---------|---------|
|TTL|    是    |
|唯一    |是|
|部分|    否|
|不區分大小寫    |否|
|疏鬆    |否 |
|背景|    是 |

## <a name="operators"></a>操作員

### <a name="logical-operators"></a>邏輯運算子

|Command  |支援 |
|---------|---------|
|$or    |    是|
|$and    |    是|
|$not    |    是|
|$nor    |    是| 

### <a name="element-operators"></a>元素運算子

|Command  |支援 |
|---------|---------|
|$exists|    是|
|$type    |    是|

### <a name="evaluation-query-operators"></a>評估查詢運算子

|Command  |支援 |
|---------|---------|
|$expr    |    否|
|$jsonSchema    |    否|
|$mod    |    是|
|$regex |    是|
|$text    | 否 (不支援。 請改用 $regex。)| 
|$where    |否| 

在 $regex 查詢中，左側錨點運算式允許索引搜尋。 不過，使用 'i' 修飾詞 (不區分大小寫) 和 'm' 修飾詞 (多行) 會在所有運算式中造成集合掃描。

當需要包含 '$' 或 '|' 時，最好先建立兩個 (或以上) regex 查詢。 例如，假設原始查詢如下：```find({x:{$regex: /^abc$/})```，則必須修改為：

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

第一個部分會使用索引來僅限搜尋以 ^abc 開頭的文件，而第二個部分會比對完整項目。 垂直線運算子 '|' 的作用如同 "or" 函式 - ```find({x:{$regex: /^abc|^def/})``` 查詢會比對 'x' 欄位值以 "abc" 或 "def" 開頭的文件。 若要利用索引，則建議將查詢分成兩個以 $or 運算子聯結的不同查詢：```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```。

### <a name="array-operators"></a>陣列運算子

|Command  |支援 | 
|---------|---------|
| $all | 是| 
| $elemMatch | 是| 
| $size | 是 | 

### <a name="comment-operator"></a>註解運算子

|Command  |支援 | 
|---------|---------|
$comment |是| 

### <a name="projection-operators"></a>投射運算子

|Command  |支援 |
|---------|---------|
|$elemMatch    |是|
|$meta|    否|
|$slice    | 是|

### <a name="update-operators"></a>更新運算子

#### <a name="field-update-operators"></a>欄位更新運算子

|Command  |支援 |
|---------|---------|
|$inc    |    是|
|$mul    |    是|
|$rename    |    是|
|$setOnInsert|    是|
|$set    |是|
|$unset| 是|
|$min    |是|
|$max    |是|
|$currentDate    | 是|

#### <a name="array-update-operators"></a>陣列更新運算子

|Command  |支援 |
|---------|---------|
|$    |是|
|$[]|    是|
|$[<identifier>]|    是|
|$addToSet    |是|
|$pop    |是|
|$pullAll|    是|
|$pull    |是|
|$push    |是|
|$pushAll| 是|


#### <a name="update-modifiers"></a>更新修飾詞

|Command  |支援 |
|---------|---------|
|$each    |    是|
|$slice    |是|
|$sort    |是|
|$position    |是|

#### <a name="bitwise-update-operator"></a>位元更新運算子

|Command  |支援 |
|---------|---------|
| $bit    |    是|    
|$bitsAllSet    |    否|
|$bitsAnySet    |    否|
|$bitsAllClear    |否|
|$bitsAnyClear    |否|

### <a name="geospatial-operators"></a>地理空間運算子

運算子 | 支援| 
--- | --- |
$geoWithin | 是 |
$geoIntersects | 是 | 
$near |  是 |
$nearSphere |  是 |
$geometry |  是 |
$minDistance | 是 |
$maxDistance | 是 |
$center | 否 |
$centerSphere | 否 |
$box | 否 |
$polygon |  否 |

## <a name="cursor-methods"></a>指標方法

|Command  |支援 |
|---------|---------|
|cursor.batchSize()    |    是|
|cursor.close()    |是|
|cursor.isClosed()|        是|
|cursor.collation()|    否|
|cursor.comment()    |是|
|cursor.count()    |是|
|cursor.explain()|    否|
|cursor.forEach()    |是|
|cursor.hasNext()    |是|
|cursor.hint()    |是|
|cursor.isExhausted()|    是|
|cursor.itcount()    |是|
|cursor.limit()    |是|
|cursor.map()    |是|
|cursor.maxScan()    |是|
|cursor.maxTimeMS()|    是|
|cursor.max()    |是|
|cursor.min()    |是|
|cursor.next()|    是|
|cursor.noCursorTimeout()    |否|
|cursor.objsLeftInBatch()    |是|
|cursor.pretty()|    是|
|cursor.readConcern()|    是|
|cursor.readPref()        |是|
|cursor.returnKey()    |否|
|cursor.showRecordId()|    否|
|cursor.size()    |是|
|cursor.skip()    |是|
|cursor.sort()    |    是|
|cursor.tailable()|    否|
|cursor.toArray()    |是|

## <a name="sort-operations"></a>排序作業

使用 `findOneAndUpdate` 作業時，支援單一欄位的排序作業，但不支援多個欄位的排序作業。

## <a name="unique-indexes"></a>唯一索引

[唯一索引](mongodb-indexing.md#unique-indexes)可確保集合中所有文件的特定欄位沒有重複值，類似於預設 "_id" 索引鍵上會保留唯一性的方式。 您可以使用 `createIndex` 命令搭配 `unique` 限制式參數，在 Cosmos DB 中建立唯一的索引：

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>複合索引

[複合索引](mongodb-indexing.md#compound-indexes-mongodb-server-version-36)提供一種方式，為欄位群組建立最多 8 個欄位的索引。 這種類型的索引與原生 MongoDB 複合索引不同。 在 Azure Cosmos DB 中，複合索引會用於套用至多個欄位作業的排序。 若要建立複合索引，您需要指定一個以上的屬性作為參數：

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

## <a name="time-to-live-ttl"></a>存留時間 (TTL)

Cosmos DB 支援以文件時間戳記為基礎的存留時間 (TTL)。 前往 [Azure 入口網站](https://portal.azure.com)即可為集合啟用 TTL。

## <a name="user-and-role-management"></a>使用者和角色管理

Cosmos DB 尚不支援使用者和角色。 不過，Cosmos DB 支援可透過 [Azure 入口網站](https://portal.azure.com) ([連接字串] 頁面) 取得的 Azure 角色型存取控制 (Azure RBAC) 及讀寫和唯讀密碼/金鑰。

## <a name="replication"></a>複寫

Cosmos DB 支援最低層級的自動、原生複寫。 此邏輯也可延伸至達到低延遲且全域的複寫。 Cosmos DB 不支援手動複寫命令。

## <a name="write-concern"></a>寫入考量

有些應用程式依賴[寫入考量](https://docs.mongodb.com/manual/reference/write-concern/)，其會指定寫入作業期間所需的回應數目。 由於 Cosmos DB 在背景中處理複寫的方式，所有的寫入依預設全部都會自動仲裁。 用戶端程式碼所指定的任何寫入考量都會受到忽略。 深入了解[使用一致性層級將可用性和效能最大化](consistency-levels.md)。

## <a name="sharding"></a>分區化

Azure Cosmos DB 支援自動與伺服器端的分區化。 它會自動管理分區的建立、放置和平衡。 Azure Cosmos DB 不支援手動將命令分區化，這表示您不需要叫用 addShard、balancerStart、moveChunk 之類的命令。您只需要在建立容器或查詢資料時指定分區索引鍵。

## <a name="sessions"></a>工作階段

Azure Cosmos DB 尚不支援伺服器端的工作階段命令。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱 [Mongo 3.6 版功能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 搭配 Azure Cosmos DB 適用於 MongoDB 的 API。
- 使用 Azure Cosmos DB 適用於 MongoDB 的 API 瀏覽 Cosmos DB [範例](mongodb-samples.md)。

<sup>注意：本文描述 Azure Cosmos DB 的功能，該功能提供對 MongoDB 資料庫的 Wire Protocol 相容性。Microsoft 不會執行 MongoDB 資料庫來提供這項服務。Azure Cosmos DB 與 MongoDB, Inc. 沒有附屬關係</sup>
