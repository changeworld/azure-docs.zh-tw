---
title: Azure Cosmos DB Gremlin 的限制
description: 圖形引擎執行時間限制的參考檔
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "72029844"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 限制
本文討論 Azure Cosmos DB Gremlin 引擎的限制，並說明它們可能對客戶周遊有何影響。

Cosmos DB Gremlin 是建置於 Cosmos DB 基礎結構之上。 因此， [Azure Cosmos DB 服務限制](https://docs.microsoft.com/azure/cosmos-db/concepts-limits)中所述的所有限制仍適用。 

## <a name="limits"></a>限制

達到 Gremlin 限制時，會取消具有**x 毫秒狀態碼**429 的遍歷，表示發生節流錯誤。 如需詳細資訊，請參閱[Gremlin 伺服器回應標頭](gremlin-limits.md)。

**Resource**    | **預設限制** | **說明**
--- | --- | ---
*指令碼長度* | **64 KB** | 每個要求的 Gremlin 周遊指令碼長度上限。
*運算子深度* | **400** |  周遊中的特有步驟總數。 例如，的 ```g.V().out()``` 運算子計數為2： V （）和 out （）， ```g.V('label').repeat(out()).times(100)``` 運算子深度為3： V （）、重複（）和 out （），因為 ```.times(100)``` 是運算子的參數 ```.repeat()``` 。
*平行處理原則的程度* | **32** | 在對儲存層發出的單一要求中，可查詢的儲存體分割區數目上限。 具有數百個數據分割的圖形會受到此限制的影響。
*重複限制* | **32** | ```.repeat()``` 運算子可執行的反覆項目數上限。 ```.repeat()```在大部分情況下，步驟的每個反復專案都會執行廣度優先的回合，這表示任何一項迴圈最多隻能有32個頂點之間的躍點。
*周遊逾時* | **30 秒** | 當遍歷超過此時間時，將會將其取消。 Cosmos DB Graph 是在毫秒內完成絕大多數周遊的 OLTP 資料庫。 若要在 Cosmos DB 圖形上執行 OLAP 查詢，請使用[圖表資料框架](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes)和[Cosmos DB Spark 連接器](https://github.com/Azure/azure-cosmosdb-spark)的[Apache Spark](https://azure.microsoft.com/services/cosmos-db/) 。
*閒置連線逾時* | **1 小時** | Gremlin 服務將維持閒置 websocket 連線開啟的時間量。 TCP keep-alive 封包或 HTTP keep-alive 要求不會延伸超過此限制的連線壽命。 Cosmos DB Graph 引擎會將 websocket 連線視為閒置，如果沒有任何作用中的 Gremlin 要求正在執行。
*每小時的資源權杖* | **100** | Gremlin 用戶端用來連線到區域中 Gremlin 帳戶的唯一資源權杖數目。 當應用程式超過每小時唯一的權杖限制時， `"Exceeded allowed resource token limit of 100 that can be used concurrently"` 將會在下一次驗證要求時傳回。

## <a name="next-steps"></a>後續步驟
* [Azure Cosmos DB Gremlin 回應標頭](gremlin-headers.md) 
* [使用 Gremlin 搭配 Azure Cosmos DB 資源權杖](how-to-use-resource-tokens-gremlin.md)
