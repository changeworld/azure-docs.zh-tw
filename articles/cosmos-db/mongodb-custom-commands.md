---
title: MongoDB 擴充命令，用來管理 Azure Cosmos DB 的 MongoDB API 中的資料
description: 本文說明如何使用 MongoDB 延伸模組命令來管理 Azure Cosmos DB 的 MongoDB API 中儲存的資料。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583571"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 延伸模組命令來管理 Azure Cosmos DB 的 MongoDB API 中儲存的資料 

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以使用任何開放原始碼[MongoDB 用戶端驅動程式](https://docs.mongodb.org/ecosystem/drivers)，與適用于 MongoDB 的 Azure Cosmos DB API 進行通訊。 Azure Cosmos DB 適用于 MongoDB 的 API 可讓您藉由遵循[mongodb 有線通訊協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)，來使用現有的用戶端驅動程式。

藉由使用適用于 MongoDB 的 Azure Cosmos DB API，您可以享有 Cosmos DB 的優點，例如全域散發、自動分區化、高可用性、延遲保證、自動、待用加密、備份等，同時保留您在 MongoDB 應用程式中的投資。

## <a name="mongodb-protocol-support"></a>MongoDB 通訊協定支援

根據預設，Azure Cosmos DB 適用于 MongoDB 的 API 與 MongoDB 伺服器版本3.2 相容，如需詳細資訊，請參閱[支援的功能和語法](mongodb-feature-support.md)。 MongoDB 3.4 版中新增的功能或查詢運算子目前可在 Azure Cosmos DB 的 MongoDB API 中預覽。 當對儲存在 MongoDB 的 Azure Cosmos DB API 中的資料執行 CRUD 作業時，下列擴充命令支援特定 Azure Cosmos DB 功能：

* [建立資料庫](#create-database)
* [更新資料庫](#update-database)
* [取得資料庫](#get-database)
* [建立集合](#create-collection)
* [更新集合](#update-collection)
* [取得集合](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>建立資料庫

建立資料庫延伸模組命令會建立新的 MongoDB 資料庫。 資料庫名稱會從執行命令的資料庫內容中使用。 CreateDatabase 命令的格式如下所示：

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

下表描述命令中的參數：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
| customAction   |  字串  |   自訂命令的名稱，必須是 "CreateDatabase"。      |
| offerThroughput | int  | 您在資料庫上設定的布建輸送量。 這是選擇性參數。 |

### <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

**建立資料庫**

若要建立名為 "test" 的資料庫，請使用下列命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**建立具有輸送量的資料庫**

若要建立名為 "test" 的資料庫和布建的 1000 ru 輸送量，請使用下列命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>更新資料庫

更新資料庫延伸模組命令會更新與指定資料庫相關聯的屬性。 目前，您只能更新 "offerThroughput" 屬性。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

下表描述命令中的參數：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
| customAction    |    字串     |   自訂命令的名稱。 必須是 "Updatedatabase.vbs"。      |
|  offerThroughput   |  int       |     您想要在資料庫上設定的新布建輸送量。    |

### <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

**更新與資料庫相關聯的布建輸送量**

若要將名稱為 "test" 之資料庫的已布建輸送量更新為 1200 ru，請使用下列命令：

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>取得資料庫

取得資料庫延伸模組命令會傳回資料庫物件。 資料庫名稱會從執行命令的資料庫內容中使用。

```
{
  customAction: "GetDatabase"
}
```

下表描述命令中的參數：


|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  customAction   |   字串      |   自訂命令的名稱。 必須是 "GetDatabase"|
        
### <a name="output"></a>輸出

如果命令成功，回應會包含具有下欄欄位的檔：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`        |   資料庫的名稱。      |
|   `provisionedThroughput`  |    `int`      |    在資料庫上設定的布建輸送量。 這是選擇性的回應參數。     |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

**取得資料庫**

若要取得名為 "test" 之資料庫的資料庫物件，請使用下列命令：

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>建立集合

建立集合擴充功能命令會建立新的 MongoDB 集合。 資料庫名稱會從執行命令的資料庫內容中使用。 CreateCollection 命令的格式如下所示：

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

下表描述命令中的參數：

| **欄位** | **類型** | **必要** | **說明** |
|---------|---------|---------|---------|
| customAction | 字串 | 必要 | 自訂命令的名稱。 必須是 "CreateCollection"。|
| collection | 字串 | 必要 | 集合的名稱。 不允許任何特殊字元。|
| offerThroughput | int | 選擇性 | 要在資料庫上設定的布建輸送量。 如果未提供此參數，則會預設為最小值 400 RU/秒。 * 若要指定超過 10000 RU/秒的`shardKey`輸送量，則需要參數。|
| shardKey | 字串 | 選擇性 | 分區化集合的分區索引鍵路徑。 如果您在中`offerThroughput`設定了超過 10000 RU/秒，則此為必要參數。  若已指定，則所有插入的檔都需要此值。 |

### <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

**建立分區集合**

若要建立名稱為 "testCollection" 且布建輸送量為 1000 ru 的分區集合，請使用下列命令： 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**建立分區化集合**

若要建立名為 "testCollection" 的分區化集合和布建的 1000 ru 輸送量和 shardkey 屬性 "a. b"，請使用下列命令：

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>更新集合

更新集合延伸模組命令會更新與指定的集合相關聯的屬性。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

下表描述命令中的參數：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  customAction   |   字串      |   自訂命令的名稱。 必須是 "UpdateCollection"。      |
|  collection   |   字串      |   集合的名稱。       |
| offerThroughput   |int|   要在集合上設定的布建輸送量。|

## <a name="output"></a>輸出

傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

**更新與集合相關聯的已布建輸送量**

若要將名為 "testCollection" 之集合的已布建輸送量更新為 1200 ru，請使用下列命令：

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>取得集合

取得集合自訂命令會傳回集合物件。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

下表描述命令中的參數：


|**欄位**|**類型** |**說明** |
|---------|---------|---------|
| customAction    |   字串      |   自訂命令的名稱。 必須是 "GetCollection"。      |
| collection    |    字串     |    集合的名稱。     |

### <a name="output"></a>輸出

如果命令成功，回應會包含具有下欄欄位的檔


|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `database`    |    `string`     |   資料庫的名稱。      |
| `collection`    |    `string`     |    集合的名稱。     |
|  `shardKeyDefinition`   |   `document`      |  當做分區金鑰使用的索引規格檔。 這是選擇性的回應參數。       |
|  `provisionedThroughput`   |   `int`      |    要在集合上設定的布建輸送量。 這是選擇性的回應參數。     |

如果命令失敗，則會傳回預設的自訂命令回應。 請參閱自訂命令的[預設輸出](#default-output)，以取得輸出中的參數。

### <a name="examples"></a>範例

**取得集合**

若要取得名為 "testCollection" 之集合的集合物件，請使用下列命令：

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>自訂命令的預設輸出

如果未指定，自訂回應會包含具有下欄欄位的檔：

|**欄位**|**類型** |**說明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   回應的狀態。 1 = = 成功。 0 = = 失敗。      |
| `code`    |   `int`      |   只有在命令失敗時傳回（亦即 ok = = 0）。 包含 MongoDB 錯誤碼。 這是選擇性的回應參數。      |
|  `errMsg`   |  `string`      |    只有在命令失敗時傳回（亦即 ok = = 0）。 包含使用者易記的錯誤訊息。 這是選擇性的回應參數。      |

## <a name="next-steps"></a>後續步驟

接下來，您可以繼續瞭解下列 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
