---
title: Azure Cosmos DB Gremlin 與 TinkerPop 功能的相容性
description: 參考檔 Graph 引擎相容性問題
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449879"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 相容性
Azure Cosmos DB 圖形引擎密切遵循[Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)遍歷步驟規格，但有一些差異。

## <a name="behavior-differences"></a>行為差異

* Azure Cosmos DB 圖形引擎會執行***廣度優先的***回合，而 TinkerPop Gremlin 是深度優先。 此行為可在水準可擴充的系統（例如 Cosmos DB）中達到更佳的效能。 

## <a name="unsupported-features"></a>不支援的功能

* ***[Gremlin 位元組程式碼](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 是不限程式設計語言的圖形周遊規格。 Cosmos DB Graph 尚不支援。 使用`GremlinClient.SubmitAsync()`並將遍歷傳遞為文字字串。

* ***`property(set, 'xyz', 1)`*** 目前不支援 set 基數。 請改用 `property(list, 'xyz', 1)`。 若要深入瞭解，請參閱[具有 TinkerPop 的頂點屬性](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)。

* ***`atch()`*** 允許使用宣告式模式比對來查詢圖形。 這項功能無法使用。

* 不支援***以物件作為***頂點或邊緣的屬性。 屬性只能是基本類型或陣列。

* 不支援***依陣列屬性*** `order().by(<array property>)`排序。 僅支援依據基本類型排序。

* 不支援***非基本的 JSON 類型***。 使用`string`、 `number` `true` /或`false`類型。 `null`不支援值。 

* 目前不支援***GraphSONv3***序列化程式。 使用`GraphSONv2`連接設定中的序列化程式、讀取器和寫入器類別。 Azure Cosmos DB Gremlin API 傳回的結果與 GraphSON 格式的格式不同。 

* 目前不支援**Lambda 運算式和函數**。 這包括`.map{<expression>}`、 `.by{<expression>}`和`.filter{<expression>}`函數。 若要深入瞭解，並瞭解如何使用 Gremlin 步驟來重寫它們，請參閱[lambda 上的附注](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)。

* 因為系統的分散式本質，所以不支援***交易***。  在 Gremlin 帳戶上設定適當的一致性模型，以「讀取您自己的寫入」，並使用開放式平行存取來解決衝突的寫入。

## <a name="next-steps"></a>後續步驟
* 請造訪[Cosmos DB user voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)頁面來分享意見反應，並協助團隊專注于對您很重要的功能。
