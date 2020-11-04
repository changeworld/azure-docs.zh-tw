---
title: Azure Cosmos DB 中 Gremlin API 的相關常見問題
description: 在 Azure Cosmos DB 中取得 Gremlin API 相關常見問題的解答
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 6840b2188f3d7e7524c4c98d7e1b6d32440e6812
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339354"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Azure Cosmos DB 中 Gremlin API 的相關常見問題
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

本文說明 Azure Cosmos DB 中 Gremlin API 相關常見問題的解答。

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>如何評估 Gremlin 查詢的效率

**executionProfile()** 預覽步驟可提供查詢執行計畫的分析。 您需要將此步驟新增至任何 Gremlin 查詢的結尾，如以下範例所示：

**查詢範例**

```
g.V('mary').out('knows').executionProfile()
```

**範例輸出**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

上述設定檔的輸出會顯示取得頂點物件、邊緣物件和工作資料集大小所花費的時間量。 這與 Azure Cosmos DB 查詢的標準成本量值相關。

## <a name="other-frequently-asked-questions"></a>其他常見問題

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>在圖形資料庫中執行查詢時，每秒的 RU 如何計費？

包括頂點及邊線的所有圖形物件，都會以 JSON 文件形式於後端顯示。 由於一個 Gremlin 查詢一次可以修正一或多個圖形物件，因此與其相關的成本會與這些由查詢處理的物件及邊線有直接關係。 Azure Cosmos DB 使用相同流程處理其他所有的 API。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)。

RU 計費是根據周遊的運作資料庫計算，並不是根據結果集計算。 比方說，若查詢的目標是要獲得單一頂點作為結果，但需要同時周遊其他多個物件，則會依計算單一結果頂點所需的所有圖形物件來計算成本。

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>圖形資料庫在 Azure Cosmos DB Gremlin API 中可擁有的最大規模為何？

Azure Cosmos DB 使用[水平資料分割](partitioning-overview.md)，自動處理儲存體和輸送量需要增加的問題。 工作負載的輸送量和儲存體容量上限取決於與指定容器相關聯的分割區數目。 不過，Gremlin API 容器有一組特定的指導方針，可確保大規模的適當效能體驗。 如需有關分割的詳細資訊及最佳做法，請參閱[在 Azure Cosmos DB 中進行資料分割](partitioning-overview.md)一文。

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>若是使用 C#/.NET 開發，應該使用 Microsoft.Azure.Graphs 套件或 Gremlin.NET？

Azure Cosmos DB Gremlin API 利用開放原始碼驅動程式作為服務的主要連接器。 因此，建議使用受 [ Apache Tinkerpop 支援的驅動程式](https://tinkerpop.apache.org/)。

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>如何防止透過 Gremlin 驅動程式發動的資料隱碼攻擊？

大部分的原生 Apache Tinkerpop Gremlin 驅動程式可讓您選擇提供參數的字典來執行查詢。 這裡提供使用 [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) 和 [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js) 兩種方法的執行範例。

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>為什麼我會收到「Gremlin 查詢編譯錯誤：找不到任何方法」錯誤？

Azure Cosmos DB Gremlin API 會實作一個在 Gremlin 介面區定義的功能子集。 如需支援的步驟和詳細資訊，請參閱 [Gremlin 支援](gremlin-support.md)一文。

最好的解決方法，是以支援的功能重新寫入必要的 Gremlin 步驟，因為 Azure Cosmos DB 支援所有必要的 Gremlin 步驟。

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>為什麼我會收到「WebSocketException：伺服器傳回的狀態碼為 ' 200 '，但需要狀態碼 ' 101 '」錯誤？

系統會在使用了錯誤的端點時擲回此錯誤。 產生此錯誤的端點模式如下：

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

這是供您圖形資料庫使用的文件端點。  正確的端點是 Gremlin Endpoint，其格式如下：

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>為什麼我會收到「RequestRateIsTooLarge」錯誤？

此錯誤表示，已配置的每秒要求單位不足以提供查詢之所用。 通常您會在執行會獲得所有頂點的查詢時看到此錯誤：

```
// Query example:
g.V()
```

此查詢會嘗試從圖形中擷取所有頂點。 因此，此查詢的成本至少等於 RU 的頂點數目。 您應該調整每秒 RU 的設定，才能處理此查詢。

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>為什麼我的 Gremlin 驅動程式連線最後還是失敗？

Gremlin 連線是透過 WebSocket 連線而產生的。 雖然 WebSocket 連線並沒有特定的存留時間，Azure Cosmos DB Gremlin API 仍然會在 30 分鐘的非使用狀態之後終止閒置的連線。

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>為什麼我不能在原生 Gremlin 驅動程式中使用流暢的 API 呼叫？

Azure Cosmos DB Gremlin API 尚未支援 Fluent API 呼叫。 Fluent API 呼叫需要稱為位元組程式碼支援的內部格式化功能，目前 Azure Cosmos DB Gremlin API 不支援此功能。 由於相同的原因，最新的 Gremlin-JavaScript 驅動程式目前也不受支援。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB Gremlin 線路通訊協定支援](gremlin-support.md)
* 使用[Gremlin 主控台](create-graph-gremlin-console.md)建立、查詢 Azure Cosmos DB 圖形資料庫並進行查詢
