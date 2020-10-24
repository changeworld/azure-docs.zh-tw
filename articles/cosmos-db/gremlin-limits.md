---
title: Azure Cosmos DB Gremlin 的限制
description: 圖形引擎執行時間限制的參考檔
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: sngun
ms.openlocfilehash: 23a265f1ae6e925440e2804056348cb705b09da5
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490555"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 限制
本文討論 Azure Cosmos DB Gremlin 引擎的限制，並說明它們可能會對客戶周遊有何影響。

Cosmos DB Gremlin 建置於 Cosmos DB 基礎結構之上。 因此， [Azure Cosmos DB 服務限制](./concepts-limits.md) 中所述的所有限制仍然適用。

## <a name="limits"></a>限制

當達到 Gremlin 限制時，就會取消以429表示節流錯誤的 **x 毫秒狀態碼** 。 如需詳細資訊，請參閱 [Gremlin server 回應標頭](gremlin-limits.md) 。

**Resource**    | **預設限制** | **說明**
--- | --- | ---
*指令碼長度* | **64 KB** | 每個要求的 Gremlin 周遊指令碼長度上限。
*運算子深度* | **400** |  周遊中的特有步驟總數。 例如， ```g.V().out()``` 具有2： v ( # A1 和 out ( # A3、 ```g.V('label').repeat(out()).times(100)``` 運算子深度為3： v ( # A5、重複 ( # A7 和 out ( # A9，因為 ```.times(100)``` 是運算子的參數 ```.repeat()``` 。
*平行處理原則的程度* | **32** | 在對儲存層發出的單一要求中，可查詢的儲存體分割區數目上限。 具有數百個分割區的圖表會受此限制影響。
*重複限制* | **32** | ```.repeat()``` 運算子可執行的反覆項目數上限。 ```.repeat()```在大多數情況下，步驟的每個反復專案都會執行廣度優先的遍歷，這表示所有的遍歷最多隻能在頂點之間有32的躍點。
*周遊逾時* | **30 秒** | 當您超過此時間時，將會取消進行中的遍歷。 Cosmos DB Graph 是在毫秒內完成絕大多數周遊的 OLTP 資料庫。 若要在 Cosmos DB Graph 上執行 OLAP 查詢，請搭配[圖形資料框架](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes)和[Cosmos DB Spark 連接器](https://github.com/Azure/azure-cosmosdb-spark)使用[Apache Spark](https://azure.microsoft.com/services/cosmos-db/) 。
*閒置連線逾時* | **1 小時** | Gremlin 服務將保持閒置 websocket 連接開啟的時間量。 TCP 保持連線的封包或 HTTP keep-alive 要求不會延長超過此限制的連接存留期。 Cosmos DB Graph 引擎會將 websocket 連線視為閒置，如果沒有任何正在執行的 Gremlin 要求。
*每小時的資源權杖* | **100** | Gremlin 用戶端用來連線到區域中 Gremlin 帳戶的唯一資源權杖數目。 當應用程式超過每小時的唯一權杖限制時， `"Exceeded allowed resource token limit of 100 that can be used concurrently"` 將會在下一次驗證要求時傳回。

## <a name="next-steps"></a>後續步驟
* [Azure Cosmos DB Gremlin 回應標頭](gremlin-headers.md)
* [使用 Gremlin 搭配 Azure Cosmos DB 資源權杖](how-to-use-resource-tokens-gremlin.md)