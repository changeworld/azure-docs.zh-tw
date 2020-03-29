---
title: Azure 宇宙 DB 格雷姆林的限制
description: 有關圖形引擎運行時限制的參考文檔
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029844"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 限制
本文介紹 Azure Cosmos DB Gremlin 引擎的極限，並解釋它們如何影響客戶遍歷。

宇宙DB格雷姆林建在宇宙資料庫基礎設施之上。 因此[，Azure Cosmos DB 服務限制](https://docs.microsoft.com/azure/cosmos-db/concepts-limits)中解釋的所有限制仍適用。 

## <a name="limits"></a>限制

達到 Gremlin 限制時，將取消遍歷 **，x-ms 狀態碼**為 429，指示限制錯誤。 有關詳細資訊[，請參閱 Gremlin 伺服器回應標頭](gremlin-limits.md)。

**資源**    | **預設限制** | **說明**
--- | --- | ---
*指令碼長度* | **64 KB** | 每個要求的 Gremlin 周遊指令碼長度上限。
*運算子深度* | **400** |  周遊中的特有步驟總數。 例如，```g.V().out()```運算子計數為 2：V（） 和 out（），```g.V('label').repeat(out()).times(100)```運算子深度為 3：V（）、重複（）和 out（），```.times(100)```因為是運算子的```.repeat()```參數。
*並行性程度* | **32** | 在對儲存層發出的單一要求中，可查詢的儲存體分割區數目上限。 具有數百個分區的圖形將受到此限制的影響。
*重複限制* | **32** | ```.repeat()``` 運算子可執行的反覆項目數上限。 在大多數情況下，步驟```.repeat()```的每個反覆運算都運行廣度優先遍歷，這意味著任何遍歷最多限制在頂點之間的 32 個躍點。
*周遊逾時* | **30 秒** | 遍歷將被取消，當它超過這個時間。 Cosmos DB Graph 是在毫秒內完成絕大多數周遊的 OLTP 資料庫。 要在宇宙資料庫圖上運行 OLAP 查詢，請使用具有[圖形資料幀](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes)和宇宙[DB 火花連接器的](https://github.com/Azure/azure-cosmosdb-spark) [Apache Spark。](https://azure.microsoft.com/services/cosmos-db/)
*閒置連線逾時* | **1 小時** | Gremlin 服務將保持空閒 Web 通訊端連接打開的時間量。 TCP 保持活動資料包或 HTTP 保持活動請求不會延長超出此限制的連接壽命。 Cosmos DB Graph 引擎認為，如果沒有運行在 Web 插座上的活動 Gremlin 請求，則 Web通訊端連接處於空閒狀態。
*每小時的資源權杖* | **100** | Gremlin 用戶端用來連線到區域中 Gremlin 帳戶的唯一資源權杖數目。 當應用程式超過每小時唯一權杖限制時，`"Exceeded allowed resource token limit of 100 that can be used concurrently"`將在下一個身份驗證請求上返回。

## <a name="next-steps"></a>後續步驟
* [Azure 宇宙 DB 格雷姆林回應標頭](gremlin-headers.md) 
* [使用 Gremlin 搭配 Azure Cosmos DB 資源權杖](how-to-use-resource-tokens-gremlin.md)
