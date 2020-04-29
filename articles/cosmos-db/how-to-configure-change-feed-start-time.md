---
title: 如何設定變更摘要處理器的開始時間 - Azure Cosmos DB
description: 了解如何將變更摘要處理器設定成從特定的日期和時間開始讀取
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586269"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>如何設定變更摘要處理器的開始時間

本文說明如何將[變更摘要處理器](./change-feed-processor.md)設定成從特定的日期和時間開始讀取。

## <a name="default-behavior"></a>預設行為

變更摘要處理器在第一次啟動時，將會初始化租用容器，並開始其[處理生命週期](./change-feed-processor.md#processing-life-cycle)。 在變更摘要處理器第一次初始化之前發生於容器中的任何變更，都不會被偵測到。

## <a name="reading-from-a-previous-date-and-time"></a>從上一個日期和時間讀取

您可以初始化會從**特定的日期和時間**開始讀取變更的變更摘要處理器，只要將 `DateTime` 的執行個體傳至 `WithStartTime` 建立器延伸模組即可：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

變更摘要處理器會針對該日期和時間進行初始化，並開始讀取其後發生的變更。

## <a name="reading-from-the-beginning"></a>從頭開始讀取

在其他案例中 (例如資料移轉，或分析容器的整個歷程記錄)，我們必須從**該容器存留期的開端**開始讀取變更摘要。 若要這麼做，我們可以在建立器延伸模組上使用 `WithStartTime`，但傳遞 `DateTime.MinValue.ToUniversalTime()`，這會產生 `DateTime` 最小值的 UTC 表示法，如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

變更摘要處理器將會初始化，並從容器存留期的開端開始讀取變更。

> [!NOTE]
> 這些自訂選項只能用來設定變更摘要處理器的開始時間點。 在租用容器第一次初始化之後，其變更就不會有任何作用。

> [!NOTE]
> 指定時間點時，將只會讀取容器中既有項目的變更。 某個項目已刪除後，它在變更摘要上的歷程記錄也會移除。

## <a name="additional-resources"></a>其他資源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的使用範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他範例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要處理器：

* [變更摘要處理器的概觀](change-feed-processor.md)
* [使用變更摘要估算器](how-to-use-change-feed-estimator.md)
