---
title: 瞭解 Azure Cosmos DB NoSQL 與關係資料庫之間的差異
description: 本文列舉 NoSQL 與關係資料庫之間的差異
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088466"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>瞭解 NoSQL 與關係資料庫之間的差異
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文將列舉 NoSQL 資料庫在關係資料庫上的一些主要優點。 我們也將討論使用 NoSQL 的一些挑戰。 若要深入瞭解存在的不同資料存放區，請參閱我們的文章，以瞭解如何 [選擇正確的資料存放區](/azure/architecture/guide/technology-choices/data-store-overview)。

## <a name="high-throughput"></a>高輸送量

維護關係資料庫系統最明顯的挑戰之一，就是大部分的關聯式引擎都會套用鎖定和閂鎖來強制執行嚴格的 [ACID 語義](https://en.wikipedia.org/wiki/ACID)。 這種方法的優點是確保資料庫內的資料狀態一致。 不過，平行存取、延遲和可用性方面都有很大的取捨。 由於這些基本的架構限制，高交易磁片區可能會導致需要手動分區資料。 實行手動分區化可能會相當耗時且很頭痛。

在這些情況下， [分散式資料庫](https://en.wikipedia.org/wiki/Distributed_database) 可以提供更具擴充性的解決方案。 不過，維護仍可能是昂貴且耗時的練習。 系統管理員可能必須執行額外的工作，以確保系統的分散本質是透明的。 他們可能也必須考慮資料庫的「已中斷連線」性質。

[Azure Cosmos DB](./introduction.md) 將這些挑戰部署到全球所有 Azure 區域，藉此簡化這些挑戰。 分割區範圍能夠動態地進行細分，以順暢地將資料庫與應用程式並行成長，同時維持高可用性。 更細緻的多租使用者和嚴格控制的雲端原生資源管理有助於 [驚人延遲保證](./consistency-levels.md#consistency-levels-and-latency) 和可預測的效能。 資料分割是完全受控的，因此系統管理員不需要撰寫程式碼或管理分割區。

如果您的交易磁片區達到極端層級，例如每秒數以千計的交易，您應該考慮分散式 NoSQL 資料庫。 請考慮 Azure Cosmos DB，以獲得最高效率、簡化維護，以及降低擁有權總成本。

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="後端" border="false":::

## <a name="hierarchical-data"></a>階層式資料

在資料庫中的交易可包含許多父子式關聯性的大量使用案例中，會有大量的使用案例。 這些關聯性可能會在一段時間內大幅增加，並證明很難管理。 階層式 [資料庫](https://en.wikipedia.org/wiki/Hierarchical_database_model) 的形式在1980年代內出現，但由於儲存體的效率很低而不受歡迎。 因為 [李小明 Codd 的關聯式模型](https://en.wikipedia.org/wiki/Relational_model) 成為幾乎所有主流資料庫管理系統所使用的實際標準，所以它們也失去了吸引力。

不過，現今檔樣式資料庫的熱門程度大幅增加。 這些資料庫可能被視為階層式資料庫架構的重塑，現在藉由考慮將資料儲存在磁片上的成本來 uninhibited。 如此一來，相較于新式檔導向的方法，在關係資料庫中維護許多複雜的父子式實體關聯性現在可視為反模式。

面向 [物件設計](https://en.wikipedia.org/wiki/Object-oriented_design)的出現，以及將其與關聯式模型結合時所產生的 [阻抗不相符](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) ，也會在特定使用案例中強調關係資料庫中的反模式。 如此一來，就會發生隱藏但經常發生的維護成本。 雖然 [ORM 方法](https://en.wikipedia.org/wiki/Object-relational_mapping) 已發展成部分緩解這個情況，但檔導向的資料庫與物件導向的方法比較得更好。 使用這個方法時，開發人員不會被迫認可至 ORM 驅動程式，也不會定制語言特定的 [OO 資料庫引擎](https://en.wikipedia.org/wiki/Object_database)。 如果您的資料包含許多父子式關聯性和深層階層，您可能會想要考慮使用 NoSQL 檔資料庫，例如 [AZURE COSMOS DB SQL API](./introduction.md)。

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="後端":::

## <a name="complex-networks-and-relationships"></a>複雜的網路和關聯性

諷刺是，在指定其名稱的情況下，關係資料庫會提供一個低於最佳的解決方案來建立深層和複雜關聯性模型。 這是因為實體之間的關聯性實際上並不存在於關係資料庫中。 它們需要在執行時間進行計算，並有複雜的關聯性需要笛卡兒聯結，才能允許使用查詢進行對應。 如此一來，當關聯性增加時，作業的計算就會以指數方式增加成本。 在某些情況下，嘗試管理這類實體的關係資料庫將會變成無法使用。

許多形式的「網路」資料庫都是在關係資料庫出現時出現，但就像階層式資料庫一樣，這些系統一直都很受歡迎。 採用緩慢的原因是因為當時缺乏使用案例，而且儲存效率不佳。 現在，graph 資料庫引擎可以視為重新出現的網路資料庫架構。 這些系統的主要優點是，關聯性會在資料庫中儲存為「第一類公民」。 因此，您可以使用固定的時間來進行關聯，而不是在每個新的聯結或交叉乘積增加時間複雜性。

如果您要在資料庫中維護複雜的關聯性網路，您可能會想要考慮圖形資料庫（例如 [Azure Cosmos DB GREMLIN API](./graph-introduction.md) ）來管理此資料。

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="後端":::

Azure Cosmos DB 是多模型資料庫服務，可為所有主要的 NoSQL 模型類型提供 API 投射;資料行系列、檔、圖形和索引鍵/值。 [Gremlin (graph) ](./gremlin-support.md)和 SQL (Core) 檔 API 層可完全互通。 這在可程式性層級的不同模型之間切換有其優點。 您可以透過複雜的網路周遊，以及在相同存放區中模型化為檔記錄的交易來查詢圖形存放區。

## <a name="fluid-schema"></a>流體架構

關係資料庫的另一項特殊特性是，架構必須在設計階段定義。 這在參考完整性和資料的符合性方面有其優點。 不過，它也可以在應用程式成長時受到限制。 在邏輯上個別個別共用相同資料表或資料庫定義的模型之間，回應架構的變更可能會隨著時間而變得複雜。 這類使用案例通常受益于 devolved 到應用程式的架構，以每一筆記錄進行管理。 這需要資料庫「架構無從驗證」，並允許記錄中包含的資料「自我描述」。

如果您管理的資料，其結構會不斷地以較高的速率變更，特別是當交易可能來自外部來源，而這種情況很難在資料庫中強制執行一致性時，您可能會想要使用受管理的 NoSQL 資料庫服務（例如 Azure Cosmos DB）來考慮較不限架構的方法。

## <a name="microservices"></a>微服務

在最近幾年來， [微服務](https://en.wikipedia.org/wiki/Microservices) 模式已大幅增加。 此模式具有 [服務導向架構](https://en.wikipedia.org/wiki/Service-oriented_architecture)中的根。 在這些新式微服務架構中，資料傳輸的消除標準是 [JSON](https://en.wikipedia.org/wiki/JSON)，也就是大部分檔導向 NoSQL 資料庫的儲存媒體。 如此一來，NoSQL 檔就能更順暢地儲存持續性和同步處理 (使用 [事件來源模式](https://en.wikipedia.org/wiki/Event-driven_architecture)) 跨複雜的微服務執行。 在這些架構中，較傳統的關係資料庫可能更加複雜。 這是因為每個 Api 的狀態和同步處理需要的轉換量更多。 Azure Cosmos DB 特別具有許多功能，可讓它更順暢地符合以 JSON 為基礎的微服務架構，而非許多 NoSQL 資料庫：

* 純 JSON 資料類型的選擇
* 資料庫內建的 JavaScript 引擎和 [查詢 API](./javascript-query-api.md) 。
* 用戶端可以訂閱的最新狀態 [變更](./change-feed.md) 摘要，以取得對容器的修改通知。

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 資料庫的一些挑戰

雖然在執行 NoSQL 資料庫時有一些明顯的優點，但您也可能想要考慮一些挑戰。 使用關聯式模型時，這些可能不會有相同的程度：

* 具有許多關聯性指向相同實體的交易。
* 跨整個資料集需要強式一致性的交易。

查看第一項挑戰，NoSQL 資料庫中的經驗法則通常是正規化的，如先前所述，在分散式系統中產生更有效率的讀取。 不過，這種方法有一些設計上的挑戰。 讓我們來看一個與某個類別和多個標記相關的產品範例：

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="後端":::

NoSQL 檔資料庫中的最佳作法是在「產品檔」中直接反正規化分類名稱和標記名稱。 不過，為了讓類別、標籤和產品保持同步，簡化這項作業的設計選項已增加維護的複雜度，因為資料會在產品的多筆記錄中重複，而不是「一對多」關聯性中的簡單更新，以及用來取得資料的聯結。 

代價是，讀取在反正規化記錄中的效率較高，而且在以概念聯結的實體數目增加時變得更有效率。 不過，就如同在反正規化記錄中增加聯結的實體數目，讀取效率也會增加，因此也會讓實體保持同步的維護複雜度。減輕這項取捨的其中一種方法是建立 [混合式資料模型](./modeling-data.md#hybrid-data-models)。

雖然 NoSQL 資料庫中有更多彈性可處理這些取捨，但增加的彈性也會產生更多設計決策。 請參閱我們的文章， [以瞭解如何使用真實世界範例來建立 Azure Cosmos DB 的資料模型和資料分割](./how-to-model-partition-example.md)，其中的使用者不只位於不同的分割區，而是在不同的容器中，這包括了一個方法，讓未正規化的 [使用者資料保持同步](./how-to-model-partition-example.md#denormalizing-usernames) 。

至於強式一致性，在整個資料集上都需要這項功能。 不過，在這是必要的情況下，這可能是分散式資料庫中的挑戰。 為了確保強式一致性，必須在所有複本和區域之間同步處理資料，才能讓用戶端讀取。 這可能會增加讀取延遲。

同樣地，Azure Cosmos DB 提供比關係資料庫更多的彈性，以因應各種不同的取捨，但針對小規模的執行，此方法可能會新增更多設計考慮。 如需有關此主題的詳細資訊，請參閱關於 [一致性、可用性和效能取捨](./consistency-levels.md) 的文章。

## <a name="next-steps"></a>下一步

瞭解如何管理您的 Azure Cosmos 帳戶和其他概念：

* [如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [全球發佈](distribute-data-globally.md)
* [一致性層級](consistency-levels.md)
* [使用 Azure Cosmos 容器和項目](account-databases-containers-items.md)
* [Azure Cosmos 帳戶的 VNET 服務端點](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos 帳戶的 IP 防火牆](how-to-configure-firewall.md)
* [如何在 Azure Cosmos 帳戶中新增和移除 Azure 區域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)