---
title: 教學課程說明如何使用適用於 MongoDB 的 Azure Cosmos DB API 來設定全域散發
description: 了解如何使用適用於 MongoDB 的 Azure Cosmos DB API 來設定全域散發。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 89826eab7b1686ae695a2716a03b2f5d03da277f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099244"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>使用適用於 MongoDB 的 Azure Cosmos DB API 來設定全域散發的資料庫
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

在本文中，我們將示範如何使用 Azure 入口網站，利用適用於 MongoDB 的 Azure Cosmos DB API 來設定全域散發的資料庫並與之連線。

本文涵蓋下列工作： 

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用[適用於 MongoDB 的 Azure Cosmos DB API](mongodb-introduction.md) 來設定全域散發

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>驗證您的區域設定 
使用適用於 MongoDB 的 Azure Cosmos DB API 來檢查全域設定的最簡單方式就是從 Mongo 殼層執行 *isMaster()* 命令。

從您的 Mongo 殼層︰

   ```
      db.isMaster()
   ```
   
範例結果︰

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>連線到慣用的區域 

適用於 MongoDB 的 Azure Cosmos DB API 可讓您針對全域散發的資料庫，指定集合的讀取喜好設定。 為了兼顧低延遲讀取和全球高可用性，建議將集合的讀取喜好設定設為 [最接近]  。 [最接近]  讀取喜好設定會設定為從最近的區域讀取。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

如果應用程式具有主要讀取/寫入區域和次要地區來因應災害復原 (DR) 情況，我們建議將集合的讀取喜好設定設為 [慣用次要]  。 [慣用次要]  讀取喜好設定會設定當主要區域無法使用時，從次要地區讀取。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

最後，如果您想要手動指定讀取區域， 您可以在讀取喜好設定內設定區域標記。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

就這麼簡單，這樣便已完成本教學課程。 您可以透過閱讀 [Azure Cosmos DB 中的一致性層級](consistency-levels.md)，來了解如何管理全域複寫帳戶的一致性。 如需有關 Azure Cosmos DB 中全域資料庫複寫運作方式的詳細資訊，請參閱[使用 Azure Cosmos DB 來全域散發資料](distribute-data-globally.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用適用於 MongoDB 的 Cosmos DB API 來設定全域散發

您現在可以繼續進行到下一個教學課程，以了解如何使用 Azure Cosmos DB 本機模擬器在本機進行開發。

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 模擬器在本機開發](local-emulator.md)
