---
title: 更改 Azure 宇宙 DB MongoDB 的 API 中的流
description: 瞭解如何使用更改流 n Azure Cosmos DB 的 MongoDB API 來獲取對資料的更改。
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467772"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>更改 Azure 宇宙 DB MongoDB 的 API 中的流

使用更改流 API 可在 Azure Cosmos DB 的蒙戈DB API 中[更改源](change-feed.md)支援。 通過使用更改流 API，應用程式可以獲取對集合或單個分片中的項所做的更改。 稍後，您可以根據結果執行進一步操作。 對集合中的項的更改按其修改時間的順序捕獲，並且每個分片鍵都保證排序次序。

> [!NOTE]
> 要使用更改流，請使用 Azure Cosmos DB 的 MongoDB API 版本 3.6 或更高版本創建帳戶。 如果針對早期版本運行更改流示例，您可能會看到錯誤`Unrecognized pipeline stage name: $changeStream`。 

下面的示例演示如何獲取集合中所有項的更改流。 本示例創建一個游標，用於在插入、更新或替換專案時監視它們。 獲取更改流需要$match階段、$project階段和完整文檔選項。 當前不支援使用更改流監視刪除操作。 作為解決方法，您可以在要刪除的項上添加軟標記。 例如，您可以在名為"已刪除"的項中添加屬性並將其設置為"true"，並在該專案上設置 TTL，以便可以自動刪除它並跟蹤它。

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

下面的示例演示如何在單個分片中獲取項的更改。 此示例獲取分片鍵等於"a"和分片鍵值等於"1"的項的更改。

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

## <a name="current-limitations"></a>目前的限制

使用更改流時，以下限制適用：

* 輸出`operationType`文檔中`updateDescription`尚不支援 和 屬性。
* 當前`insert`支援`update`和`replace`操作類型。 尚不支援刪除操作或其他事件。

由於這些限制，需要$match階段、$project階段和完整文檔選項，如前面的示例所示。

## <a name="error-handling"></a>錯誤處理

使用更改流時，支援以下錯誤代碼和消息：

* **HTTP 錯誤代碼 429** - 當更改流被限制時，它將返回一個空頁。

* **命名空間未找到（操作類型無效）** - 如果在不存在的集合上運行更改流，或者如果刪除集合，則返回錯誤`NamespaceNotFound`。 由於無法`operationType`在輸出文檔中返回該屬性，因此返回`operationType Invalidate``NamespaceNotFound`錯誤不是錯誤。

## <a name="next-steps"></a>後續步驟

* [使用時間即時使蒙高DB的 Azure Cosmos DB API 中的資料自動過期](mongodb-time-to-live.md)
* [Azure Cosmos DB 的 MongoDB 版 API 中的索引編製](mongodb-indexing.md)
