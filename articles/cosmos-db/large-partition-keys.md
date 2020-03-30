---
title: 使用大分區鍵創建 Azure Cosmos 容器
description: 瞭解如何使用 Azure 門戶和不同的 SDK 在 Azure Cosmos DB 中使用大型分區鍵創建容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887677"
---
# <a name="create-containers-with-large-partition-key"></a>使用大分區鍵創建容器

Azure Cosmos DB 使用基於雜湊的分區方案來實現資料的水準縮放。 2019 年 5 月 3 日之前創建的所有 Azure Cosmos 容器都使用雜湊函數，該函數基於分區鍵的前 100 個位元組計算雜湊。 如果有多個分區鍵的前 100 個位元組具有相同的，則服務將這些邏輯分區視為相同的邏輯分區。 這可能導致問題，如分區大小配額不正確，以及跨分區鍵應用唯一索引。 引入了大分區鍵來解決此問題。 Azure Cosmos DB 現在支援具有高達 2 KB 的值的大型分區鍵。

使用雜湊函數的增強版本的功能支援大型分區鍵，該功能可以從高達 2 KB 的大分區金鑰生成唯一的雜湊。 對於具有高分區金鑰基數的方案，無論分區金鑰的大小如何，也建議使用此雜湊版本。 分區金鑰基數定義為唯一邏輯分區的數量，例如，在容器中按 ±30000 邏輯分區的順序排列。 本文介紹如何使用 Azure 門戶和不同的 SDK 使用大型分區金鑰創建容器。

## <a name="create-a-large-partition-key-azure-portal"></a>創建大型分區鍵（Azure 門戶）

要創建大型分區鍵，使用 Azure 門戶創建新容器時，請檢查 **"我的分區鍵大於 100 位元組**"選項。 如果不需要大型分區鍵，或者在 SDK 版本上運行的應用程式早于 1.18，請取消選中該核取方塊。

![使用 Azure 門戶創建大型分區鍵](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>創建大型分區鍵（PowerShell）

要創建具有大型分區鍵支援的容器，請參閱，

* [創建具有較大分區金鑰大小的 Azure Cosmos 容器](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>創建大型分區金鑰 （.Net SDK）

要使用 .NET SDK 創建具有大型分區鍵的容器，請`PartitionKeyDefinitionVersion.V2`指定該屬性。 下面的示例演示如何在分區鍵定義物件中指定版本屬性並將其設置為分區鍵定義版本.V2。

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>支援的 SDK 版本

大分區鍵支援以下最小版本的 SDK：

|SDK 類型  | 最小版本   |
|---------|---------|
|.Net     |    1.18     |
|JAVA 同步     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | 版本高於`2017-05-03`使用`x-ms-version`請求標頭。|
| Resource Manager 範本 | 版本 2 使用`"version":2``partitionKey`物件中的屬性。 |

目前，在 Power BI 和 Azure 邏輯應用中，不能使用具有大分區鍵的容器。 您可以使用容器，無需來自這些應用程式的大型分區金鑰。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [在 Azure 宇宙資料庫中請求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)
