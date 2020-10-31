---
title: 在 Azure Cosmos DB 的 MongoDB API 中管理資料的 MongoDB 擴充功能命令
description: 本文說明如何使用 MongoDB 擴充功能命令來管理 Azure Cosmos DB 的 MongoDB API 中儲存的資料。
author: jasonwhowell
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: jasonh
ms.custom: devx-track-js
ms.openlocfilehash: 68f7c9331423fa4ef350bd7915ad85e3152c6885
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096541"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 擴充功能命令來管理 Azure Cosmos DB 的 MongoDB API 中儲存的資料 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

下列檔包含 Azure Cosmos DB 適用于 MongoDB 的 API 專屬的自訂動作命令。 您可以使用這些命令來建立和取得 [Azure Cosmos DB 容量模型](account-databases-containers-items.md)專屬的資料庫資源。

藉由使用適用于 MongoDB 的 Azure Cosmos DB API，您可以享有 Cosmos DB 的優點，例如全域散發、自動分區化、高可用性、延遲保證、自動、待用加密、備份等，同時保留您的 MongoDB 應用程式投資。 您可以使用任何開放原始碼 [MongoDB 用戶端驅動程式](https://docs.mongodb.org/ecosystem/drivers)，與 Azure Cosmos DB 的 mongodb API 進行通訊。 適用于 MongoDB 的 Azure Cosmos DB API 可透過遵循 [MongoDB 網路通訊協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)的方式，來使用現有的用戶端驅動程式。

## <a name="mongodb-protocol-support"></a>MongoDB 通訊協定支援

Azure Cosmos DB 適用于 MongoDB 的 API 與 MongoDB 伺服器3.2 和3.6 版相容。 如需詳細資訊，請參閱 [支援的功能和語法](mongodb-feature-support.md) 。 

下列延伸模組命令可讓您透過資料庫要求建立和修改 Azure Cosmos DB 特定的資源：

* [建立資料庫](#create-database)
* [更新資料庫](#update-database)
* [取得資料庫](#get-database)
* [建立集合](#create-collection)
* [更新集合](#update-collection)
* [取得集合](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> 建立資料庫

建立資料庫延伸模組命令會建立新的 MongoDB 資料庫。 資料庫名稱可從命令所設定的資料庫內容中使用 `use database` 。 下表描述命令內的參數：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
| `customAction`   |  `string`  |   自訂命令的名稱，必須是 "CreateDatabase"。      |
| `offerThroughput` | `int`  | 您在資料庫上設定的布建輸送量。 這是選擇性參數。 |
| `autoScaleSettings` | `Object` | 自動調整 [模式](provision-throughput-autoscale.md)的必要參數。 此物件包含與自動調整容量模式相關聯的設定。 您可以設定 `maxThroughput` 值，以描述集合將會以動態方式增加的最高要求單位數量。 |

### <a name="output"></a>輸出

如果命令成功，它會傳回下列回應：

```javascript
{ "ok" : 1 }
```

請參閱輸出中參數的自訂命令 [預設輸出](#default-output) 。

### <a name="examples"></a>範例

#### <a name="create-a-database"></a>建立資料庫

若要使用所有預設值來建立名為 `"test"` 的資料庫，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

此命令會建立資料庫，而不使用資料庫層級的輸送量。 這表示此資料庫中的集合將需要指定您需要使用的輸送量量。

#### <a name="create-a-database-with-throughput"></a>建立具有輸送量的資料庫

若要建立名為的資料庫， `"test"` 並指定 [資料庫層級](set-throughput.md#set-throughput-on-a-database) 的 1000 ru 布建輸送量，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

這將會建立資料庫，並為其設定輸送量。 除非使用 [特定的輸送量層級](set-throughput.md#set-throughput-on-a-database-and-a-container)建立集合，否則此資料庫中的所有集合都會共用設定的輸送量。

#### <a name="create-a-database-with-autoscale-throughput"></a>建立具有自動調整輸送量的資料庫

若要建立名為的資料庫， `"test"` 並在 [資料庫層級](set-throughput.md#set-throughput-on-a-database)指定 20000 RU/秒的自動調整最大輸送量，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> 更新資料庫

更新資料庫延伸模組命令會更新與指定資料庫相關聯的屬性。 下表描述命令內的參數：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
| `customAction`    |    `string`     |   自訂命令的名稱。 必須是 "Updatedatabase.vbs"。      |
|  `offerThroughput`   |  `int`       |     當資料庫使用[資料庫層級輸送量](set-throughput.md#set-throughput-on-a-database)時，您要在資料庫上設定的新布建輸送量  |
| `autoScaleSettings` | `Object` | 自動調整 [模式](provision-throughput-autoscale.md)的必要參數。 此物件包含與自動調整容量模式相關聯的設定。 您可以設定 `maxThroughput` 值，這個值描述資料庫將會以動態方式增加的最高要求單位數量。 |

此命令會使用會話內容中指定的資料庫。 這是您在命令中使用的資料庫 `use <database>` 。 目前無法使用此命令來變更資料庫名稱。

### <a name="output"></a>輸出

如果命令成功，它會傳回下列回應：

```javascript
{ "ok" : 1 }
```

請參閱輸出中參數的自訂命令 [預設輸出](#default-output) 。

### <a name="examples"></a>範例

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>更新與資料庫相關聯的布建輸送量

若要將名稱為 1200 ru 的資料庫布建輸送量更新為 `"test"` ru，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>更新與資料庫相關聯的自動調整輸送量

若要將名稱為 20000 ru 的資料庫布建輸送量更新為 `"test"` ru，或將其轉換為 [自動調整輸送量層級](provision-throughput-autoscale.md)，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> 取得資料庫

取得資料庫延伸模組命令會傳回資料庫物件。 資料庫名稱會從執行命令的資料庫內容中使用。

```javascript
{
  customAction: "GetDatabase"
}
```

下表描述命令內的參數：


|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   自訂命令的名稱。 必須是 ">getdatabase"|
        
### <a name="output"></a>輸出

如果命令成功，回應會包含具有下欄欄位的檔：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`        |   資料庫的名稱。      |
|   `provisionedThroughput`  |    `int`      |    資料庫使用[手動資料庫層級輸送量](set-throughput.md#set-throughput-on-a-database)時，在資料庫上設定的布建輸送量     |
| `autoScaleSettings` | `Object` | 如果使用 [自動調整模式](provision-throughput-autoscale.md)，此物件會包含與資料庫相關聯的容量參數。 此 `maxThroughput` 值描述資料庫將增加為動態的最高要求單位數量。 |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱輸出中參數的自訂命令 [預設輸出](#default-output) 。

### <a name="examples"></a>範例

#### <a name="get-the-database"></a>取得資料庫

若要取得名為之資料庫的資料庫物件 `"test"` ，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

如果資料庫沒有相關聯的輸送量，則輸出會是：

```javascript
{ "database" : "test", "ok" : 1 }
```

如果資料庫具有與其相關聯的 [資料庫層級手動輸送量](set-throughput.md#set-throughput-on-a-database) ，則輸出會顯示這些 `provisionedThroughput` 值：

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

如果資料庫具有相關聯的 [資料庫層級自動調整輸送量](provision-throughput-autoscale.md) ，輸出會顯示 `provisionedThroughput` ，其中描述資料庫的最小 ru/秒，以及包含的物件（其中 `autoScaleSettings` `maxThroughput` 描述資料庫的最大 ru/秒）。

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> 建立集合

建立集合延伸模組命令會建立新的 MongoDB 集合。 資料庫名稱會從命令所設定的資料庫內容中使用 `use database` 。 CreateCollection 命令的格式如下所示：

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

下表描述命令內的參數：

| **欄位** | **型別** | **必要** | **說明** |
|---------|---------|---------|---------|
| `customAction` | `string` | 必要 | 自訂命令的名稱。 必須是 "CreateCollection"。|
| `collection` | `string` | 必要 | 集合的名稱。 不允許任何特殊字元或空格。|
| `offerThroughput` | `int` | 選擇性 | 要在資料庫上設定的布建輸送量。 如果未提供此參數，則會預設為最小 400 RU/秒。 * 若要指定超過 10000 RU/秒的輸送量，則 `shardKey` 需要參數。|
| `shardKey` | `string` | 具有大型輸送量的集合所需 | 分區化集合之分區索引鍵的路徑。 如果您在中設定超過 10000 RU/秒，則需要此參數 `offerThroughput` 。  如果已指定，則所有插入的檔都需要此索引鍵和值。 |
| `autoScaleSettings` | `Object` | 自動調整[模式](provision-throughput-autoscale.md)的必要參數 | 此物件包含與自動調整容量模式相關聯的設定。 您可以設定 `maxThroughput` 值，以描述集合將會以動態方式增加的最高要求單位數量。 |

### <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱輸出中參數的自訂命令 [預設輸出](#default-output) 。

### <a name="examples"></a>範例

#### <a name="create-a-collection-with-the-minimum-configuration"></a>建立具有最小設定的集合

若要使用名稱和預設值建立新的集合 `"testCollection"` ，請使用下列命令： 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

這將會產生新的 fixed、分區、collection 與 400RU/s，以及 `_id` 自動建立之欄位的索引。 這種類型的設定也適用于透過函式建立新的集合時 `insert()` 。 例如： 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>建立分區集合

若要建立分區集合，其名稱 `"testCollection"` 和布建的輸送量為 1000 ru，請使用下列命令： 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

您可以建立最多 10000 RU/秒的集合， `offerThroughput` 而不需要指定分區索引鍵。 針對輸送量較大的集合，請參閱下一節。

#### <a name="create-a-sharded-collection"></a>建立分區化集合

若要建立分區化集合，其名稱 `"testCollection"` 和布建的輸送量為 11000 ru，而 `shardkey` 屬性為 "a. b"，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

此命令現在需要 `shardKey` 參數，因為中指定了超過 10000 RU/秒 `offerThroughput` 。

#### <a name="create-an-unsharded-autoscale-collection"></a>建立分區自動調整規模集合

若要建立名為的分區集合 `'testCollection'` ，並使用設定為 4000 RU/秒的 [自動調整輸送量容量](provision-throughput-autoscale.md) ，請使用下列命令：

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

針對 `autoScaleSettings.maxThroughput` 值，您可以指定從 4000 ru/秒到 10000 ru/s 的範圍，而不使用分區金鑰。 如需更高的自動調整輸送量，您必須指定 `shardKey` 參數。

#### <a name="create-a-sharded-autoscale-collection"></a>建立分區化自動調整集合

若要使用名為的分區索引鍵建立名為的分區化集合 `'testCollection'` `'a.b'` ，並將 [自動調整輸送量容量](provision-throughput-autoscale.md) 設定為 20000 RU/秒，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> 更新集合

更新集合延伸模組命令會更新與指定的集合相關聯的屬性。

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

下表描述命令內的參數：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   自訂命令的名稱。 必須是 "UpdateCollection"。      |
|  `collection`   |   `string`      |   集合的名稱。       |
| `offerThroughput` | `int` |   要在集合上設定的布建輸送量。|
| `autoScaleSettings` | `Object` | 自動調整 [模式](provision-throughput-autoscale.md)的必要參數。 此物件包含與自動調整容量模式相關聯的設定。 此 `maxThroughput` 值描述集合將會以動態方式增加的最高要求單位數量。 |

## <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱輸出中參數的自訂命令 [預設輸出](#default-output) 。

### <a name="examples"></a>範例

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>更新與集合相關聯的布建輸送量

若要將名稱為 1200 ru 的集合布建輸送量更新為 `"testCollection"` ru，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> 取得集合

取得集合自訂命令會傳回集合物件。

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

下表描述命令內的參數：


|**欄位**|**型別** |**說明** |
|---------|---------|---------|
| `customAction`    |   `string`      |   自訂命令的名稱。 必須是 ">getcollection"。      |
| `collection`    |    `string`     |    集合的名稱。     |

### <a name="output"></a>輸出

如果命令成功，回應會包含具有下欄欄位的檔


|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`     |   資料庫的名稱。      |
| `collection`    |    `string`     |    集合的名稱。     |
|  `shardKeyDefinition`   |   `document`      |  用來作為分區索引鍵的索引規格檔。 這是選擇性的回應參數。       |
|  `provisionedThroughput`   |   `int`      |    要在集合上設定的布建輸送量。 這是選擇性的回應參數。     |
| `autoScaleSettings` | `Object` | 如果使用 [自動調整模式](provision-throughput-autoscale.md)，此物件會包含與資料庫相關聯的容量參數。 此 `maxThroughput` 值描述集合將會以動態方式增加的最高要求單位數量。 |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱輸出中參數的自訂命令 [預設輸出](#default-output) 。

### <a name="examples"></a>範例

#### <a name="get-the-collection"></a>取得集合

若要取得名為之集合的集合物件 `"testCollection"` ，請使用下列命令：

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

如果集合具有相關聯的輸送量容量，則會包含 `provisionedThroughput` 值，而且輸出會是：

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

如果集合具有相關聯的自動調整輸送量，則會包含 `autoScaleSettings` 具有參數的物件 `maxThroughput` ，而此參數會定義集合將以動態方式增加的最大輸送量。 此外，它也會包含 `provisionedThroughput` 值，此值會定義此集合將會降低的最小輸送量（如果集合中沒有任何要求的話）： 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

如果集合是在自動調整模式或手動上共用 [資料庫層級的輸送量](set-throughput.md#set-throughput-on-a-database)，則輸出會是：

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> 自訂命令的預設輸出

如果未指定，自訂回應會包含具有下欄欄位的檔：

|**欄位**|**型別** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `code`    |   `int`      |   只有在命令失敗時才會傳回 (例如 ok = = 0) 。 包含 MongoDB 錯誤碼。 這是選擇性的回應參數。      |
|  `errMsg`   |  `string`      |    只有在命令失敗時才會傳回 (例如 ok = = 0) 。 包含使用者易記的錯誤訊息。 這是選擇性的回應參數。      |

例如：

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續瞭解下列 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
