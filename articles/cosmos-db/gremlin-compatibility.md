---
title: Azure 宇宙 DB Gremlin 與 TinkerPop 功能的相容性
description: 參考文件圖形引擎相容性問題
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449879"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure 宇宙 DB 格雷姆林相容性
Azure Cosmos DB 圖形引擎緊隨[Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)遍歷步驟規範,但存在差異。

## <a name="behavior-differences"></a>行為差異

* Azure 宇宙 DB 圖形引擎運行***廣度優先***遍歷,而 TinkerPop Gremlin 是深度優先。 此行為在橫向可擴展的系統中(如Cosmos DB)中實現了更好的性能。 

## <a name="unsupported-features"></a>不支援的功能

* ***[Gremlin 位元組程式碼](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 是不限程式設計語言的圖形周遊規格。 宇宙 DB 圖形還不支援它。 使用`GremlinClient.SubmitAsync()`和傳遞遍曆作為文本字串。

* ***`property(set, 'xyz', 1)`*** 設置基數今天不受支援。 請改用 `property(list, 'xyz', 1)`。 要瞭解更多資訊,請參閱[帶有 TinkerPop 的頂點屬性](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)。

* ***`atch()`*** 允許使用聲明性模式匹配查詢圖形。 此功能不可用。

* 不支援***物件作為***頂點或邊上的屬性。 屬性只能是基本類型或陣列。

* 不支援***按陣列屬性***`order().by(<array property>)`排序。 僅支援依據基本類型排序。

* 不支援***非原始 JSON 類型***。 使用`string``number`、`true`/或`false`類型。 `null`不支援值。 

* ***圖形SONv3***序列化器當前不受支援。 連接`GraphSONv2`配置中使用序列化器、讀取器和編寫器類。 Azure 宇宙 DB Gremlin API 傳回的結果與 GraphSON 格式的格式不同。 

* **Lambda 運算式和函數**當前不受支援。 這包括`.map{<expression>}`、`.by{<expression>}``.filter{<expression>}`函數。 要瞭解更多資訊,並瞭解如何使用 Gremlin 步驟重寫它們,請參閱關於[Lambdas 的說明](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)。

* 由於系統的分散式特性,不支援***事務***。  在 Gremlin 帳戶上配置適當的一致性模型以「讀取您自己的寫入」,並使用樂觀併發來解決衝突寫入。

## <a name="next-steps"></a>後續步驟
* 訪問[Cosmos DB 使用者語音](https://feedback.azure.com/forums/263030-azure-cosmos-db)頁面,分享反饋,並幫助團隊專注於對您重要的功能。
