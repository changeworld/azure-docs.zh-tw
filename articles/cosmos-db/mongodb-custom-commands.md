---
title: MongoDB 擴展命令，用於管理蒙戈DB Azure 宇宙 DB API 中的資料
description: 本文介紹如何使用 MongoDB 擴展命令來管理存儲在 Azure Cosmos DB 的 MongoDB API 中的資料。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445212"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 擴展命令管理存儲在蒙戈DB Azure 宇宙 DB API 中的資料 

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以使用任何開源[MongoDB 用戶端驅動程式](https://docs.mongodb.org/ecosystem/drivers)與 Azure Cosmos DB 的蒙戈DB API 進行通信。 Azure Cosmos DB 的蒙戈DB API 通過遵循[MongoDB 導線協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)，允許使用現有的用戶端驅動程式。

通過使用適用于 MongoDB 的 Azure Cosmos DB API，您可以享受 Cosmos DB 的優勢，例如全域分發、自動分片、高可用性、延遲保證、自動加密、靜態加密、備份等，同時保留投資在 MongoDB 應用程式中。

## <a name="mongodb-protocol-support"></a>蒙戈DB協定支援

預設情況下，Azure Cosmos DB 的 MongoDB API 與 MongoDB 伺服器版本 3.2 相容，有關詳細資訊，請參閱[支援的功能和語法](mongodb-feature-support.md)。 MongoDB 版本 3.4 中添加的功能或查詢運算子目前可在 MongoDB 的 Azure Cosmos DB API 中作為預覽版提供。 以下擴展命令支援 Azure Cosmos DB 的特定功能，當對存儲在 Azure Cosmos DB 的 MongoDB API 中的資料執行 CRUD 操作時：

* [創建資料庫](#create-database)
* [更新資料庫](#update-database)
* [獲取資料庫](#get-database)
* [創建集合](#create-collection)
* [更新集合](#update-collection)
* [獲取集合](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>創建資料庫

創建資料庫擴展命令創建新的 MongoDB 資料庫。 資料庫名稱從執行命令的資料庫上下文中使用。 創建資料庫命令的格式如下：

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

下表描述了命令中的參數：

|**領域**|**類型** |**描述** |
|---------|---------|---------|
| customAction   |  字串  |   自訂命令的名稱，它必須是"創建資料庫"。      |
| 提供輸送量 | int  | 在資料庫上設置的預配輸送量。 這是選擇性參數。 |

### <a name="output"></a>輸出

返回預設自訂命令回應。 有關輸出中的參數，請參閱自訂命令的[預設輸出](#default-output)。

### <a name="examples"></a>範例

**創建資料庫**

要創建名為"test"的資料庫，請使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**創建具有輸送量的資料庫**

要創建名為"test"的資料庫和預配輸送量為 1000 個 R，請使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>更新資料庫

更新資料庫擴展命令更新與指定資料庫關聯的屬性。 目前，您只能更新"優惠輸送量"屬性。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

下表描述了命令中的參數：

|**領域**|**類型** |**描述** |
|---------|---------|---------|
| customAction    |    字串     |   自訂命令的名稱。 必須是"更新資料庫"。      |
|  提供輸送量   |  int       |     要在資料庫上設置的新預配輸送量。    |

### <a name="output"></a>輸出

返回預設自訂命令回應。 有關輸出中的參數，請參閱自訂命令的[預設輸出](#default-output)。

### <a name="examples"></a>範例

**更新與資料庫關聯的預配輸送量**

要將名稱為"test"的資料庫的預配輸送量更新為 1200 個 R，請使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>獲取資料庫

get 資料庫擴展命令返回資料庫物件。 資料庫名稱從執行命令的資料庫上下文中使用。

```
{
  customAction: "GetDatabase"
}
```

下表描述了命令中的參數：


|**領域**|**類型** |**描述** |
|---------|---------|---------|
|  customAction   |   字串      |   自訂命令的名稱。 必須是"獲取資料庫"|
        
### <a name="output"></a>輸出

如果命令成功，回應包含包含以下欄位的文檔：

|**領域**|**類型** |**描述** |
|---------|---------|---------|
|  `ok`   |   `int`     |   回應狀態。 1 = 成功。 0 = 失敗。      |
| `database`    |    `string`        |   資料庫的名稱。      |
|   `provisionedThroughput`  |    `int`      |    在資料庫上設置的預配輸送量。 這是一個可選的回應參數。     |

如果命令失敗，將返回預設的自訂命令回應。 有關輸出中的參數，請參閱自訂命令的[預設輸出](#default-output)。

### <a name="examples"></a>範例

**獲取資料庫**

要獲取名為"test"的資料庫的資料庫物件，請使用以下命令：

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>創建集合

創建集合擴展命令創建新的 MongoDB 集合。 資料庫名稱從執行命令的資料庫上下文中使用。 CreateCollection 命令的格式如下：

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

下表描述了命令中的參數：

|**領域**|**類型** |**描述** |
|---------|---------|---------|
| customAction    | 字串 | 自訂命令的名稱。 必須是"創建集合"     |
| collection      | 字串 | 集合的名稱                                   |
| 提供輸送量 | int    | 預配的輸送量，以設置在資料庫上。 它是可選參數 |
| 分片鍵        | 字串 | 分片鍵路徑以創建分片集合。 它是可選參數 |

### <a name="output"></a>輸出

返回預設自訂命令回應。 有關輸出中的參數，請參閱自訂命令的[預設輸出](#default-output)。

### <a name="examples"></a>範例

**創建未分片的集合**

要創建名稱為"testCollection"且預配輸送量為 1000 個 R 的未分片集合，請使用以下命令： 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**創建分片集合**

要創建名稱為"testCollection"且預配輸送量為 1000 個 R 的分片集合，請使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>更新集合

更新集合擴展命令更新與指定集合關聯的屬性。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

下表描述了命令中的參數：

|**領域**|**類型** |**描述** |
|---------|---------|---------|
|  customAction   |   字串      |   自訂命令的名稱。 必須是"更新集合"。      |
|  collection   |   字串      |   集合的名稱。       |
| 提供輸送量   |int|   預配的輸送量以在集合上設置。|

## <a name="output"></a>輸出

返回預設自訂命令回應。 有關輸出中的參數，請參閱自訂命令的[預設輸出](#default-output)。

### <a name="examples"></a>範例

**更新與集合關聯的預配輸送量**

要將名稱為"testCollection"的集合的預配輸送量更新為 1200 個 R，請使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>獲取集合

get 集合自訂命令返回集合物件。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

下表描述了命令中的參數：


|**領域**|**類型** |**描述** |
|---------|---------|---------|
| customAction    |   字串      |   自訂命令的名稱。 必須是"獲取收集"。      |
| collection    |    字串     |    集合的名稱。     |

### <a name="output"></a>輸出

如果命令成功，回應包含包含以下欄位的文檔


|**領域**|**類型** |**描述** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應狀態。 1 = 成功。 0 = 失敗。      |
| `database`    |    `string`     |   資料庫的名稱。      |
| `collection`    |    `string`     |    集合的名稱。     |
|  `shardKeyDefinition`   |   `document`      |  索引規範文檔用作分片鍵。 這是一個可選的回應參數。       |
|  `provisionedThroughput`   |   `int`      |    預配的輸送量，以在集合上設置。 這是一個可選的回應參數。     |

如果命令失敗，將返回預設的自訂命令回應。 有關輸出中的參數，請參閱自訂命令的[預設輸出](#default-output)。

### <a name="examples"></a>範例

**獲取集合**

要獲取名為"testCollection"的集合集合物件，請使用以下命令：

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>自訂命令的預設輸出

如果未指定，自訂回應包含包含以下欄位的文檔：

|**領域**|**類型** |**描述** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應狀態。 1 = 成功。 0 = 失敗。      |
| `code`    |   `int`      |   僅在命令失敗時返回（即確定 = 0）。 包含蒙戈DB錯誤代碼。 這是一個可選的回應參數。      |
|  `errMsg`   |  `string`      |    僅在命令失敗時返回（即確定 = 0）。 包含方便使用的錯誤訊息。 這是一個可選的回應參數。      |

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續學習以下 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
