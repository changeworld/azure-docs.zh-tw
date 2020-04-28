---
title: 瞭解 Azure Cosmos DB NoSQL 和關係資料庫之間的差異
description: 本文列舉 NoSQL 和關係資料庫之間的差異
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75896619"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>瞭解 NoSQL 和關係資料庫之間的差異

本文將列舉 NoSQL 資料庫在關係資料庫上的一些主要優點。 我們也會討論使用 NoSQL 時的一些挑戰。 如需深入瞭解現有的不同資料存放區，請參閱我們有關[選擇正確資料存放區](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)的文章。

## <a name="high-throughput"></a>高輸送量

維護關係資料庫系統時最明顯的其中一項挑戰是，大部分的關聯式引擎都會套用鎖定和閂鎖來強制執行嚴格的[ACID 語義](https://en.wikipedia.org/wiki/ACID)。 這種方法的優點在於確保資料庫內的資料狀態一致。 不過，對於平行存取、延遲和可用性，會有很大的取捨。 由於這些基本架構限制，高交易式磁片區可能會導致需要手動分區資料。 執行手動分區化可能是一種耗時且棘手的練習。

在這些案例中，[分散式資料庫](https://en.wikipedia.org/wiki/Distributed_database)可以提供更具擴充性的解決方案。 不過，維護仍可能是昂貴且耗時的練習。 管理員可能必須執行額外的工作，以確保系統的分散式本質是透明的。 他們可能也必須考慮資料庫的「中斷連線」本質。

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)藉由在全球所有 Azure 區域中部署，來簡化這些挑戰。 資料分割範圍可以動態地進行細分，以順暢地隨著應用程式成長資料庫，同時維持高可用性。 更細緻的多租使用者和嚴格控制的雲端原生資源治理有助於[驚人延遲保證](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency)和可預測的效能。 資料分割是完全受控的，因此系統管理員不需要撰寫程式碼或管理磁碟分割。

如果您的交易式磁片區達到極端層級（例如，每秒數千筆交易），您應該考慮分散式 NoSQL 資料庫。 請考慮 Azure Cosmos DB 以取得最高效率、簡化維護，以及降低擁有權總成本。

![後端](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>階層式資料

資料庫中的交易可以包含許多父子式關聯性，這是很多的使用案例。 這些關聯性可能會在一段時間內大幅成長，而且很容易管理。 階層式[資料庫](https://en.wikipedia.org/wiki/Hierarchical_database_model)的形式會在1980年代期間出現，但因為儲存空間的效率過低，所以不受歡迎。 它們也失去了一項吸引人，因為[李小明 Codd 的關聯式模型](https://en.wikipedia.org/wiki/Relational_model)成為幾乎所有主流資料庫管理系統所使用的標準。

不過，現今檔樣式資料庫的熱門程度已大幅增加。 這些資料庫可能會被視為階層式資料庫架構的重塑，現在 uninhibited 由考慮將資料儲存在磁片上的成本而定。 因此，相較于新式檔導向方法，在關係資料庫中維護許多複雜的父子式實體關聯性，現在可能會被視為反模式。

面向[物件設計](https://en.wikipedia.org/wiki/Object-oriented_design)的出現，以及將它與關聯式模型結合時所發生的[阻抗不相符](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)，也會針對特定使用案例強調關係資料庫中的反模式。 如此一來，就會產生隱藏的維護成本，但通常是相當重要的。 雖然[ORM 方法](https://en.wikipedia.org/wiki/Object-relational_mapping)已經進化成可減輕這種情況，但檔導向資料庫的結合程度也優於物件導向的方法。 使用這種方法時，開發人員不會被迫認可到 ORM 驅動程式，或定制語言特定的[OO 資料庫引擎](https://en.wikipedia.org/wiki/Object_database)。 如果您的資料包含許多父子式關聯性和階層的深度層級，您可能會想要考慮使用 NoSQL 檔資料庫，例如[AZURE COSMOS DB SQL API](https://docs.microsoft.com/azure/cosmos-db/introduction)。

![OrderDetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>複雜的網路和關聯性

諷刺是，並指定其名稱，關係資料庫呈現的模型不是深度和複雜關聯性的最佳解決方案。 這是因為實體之間的關聯性實際上並不存在於關係資料庫中。 它們需要在執行時間進行計算，而複雜的關聯性需要笛卡爾聯結，才能允許使用查詢進行對應。 因此，隨著關聯性的增加，作業在計算方面會以指數方式變得更昂貴。 在某些情況下，嘗試管理這類實體的關係資料庫將會變成無法使用。

有各種形式的「網路」資料庫在關係資料庫出現時才會出現，但就像使用階層式資料庫一樣，這些系統會非常費力地獲得普及。 採用速度變慢是因為當時沒有使用案例，而且儲存效率不佳。 目前，圖形資料庫引擎可能會被視為重新出現網路資料庫的範例。 這些系統的主要優點是，關聯性會在資料庫中儲存為「第一類公民」。 因此，可以在持續時間內進行關聯性的處理，而不是隨著每個新的聯結或交叉乘積增加時間複雜性。

如果您要在資料庫中維護複雜的關聯性網路，您可能會想要考慮使用圖表資料庫（例如[Azure Cosmos DB GREMLIN API](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) ）來管理這項資料。

![圖形](./media/relational-or-nosql/graph.png)

Azure Cosmos DB 是多模型資料庫服務，可為所有主要的 NoSQL 模型類型提供 API 投射;資料行系列、檔、圖形和索引鍵/值。 [Gremlin （圖形）](https://docs.microsoft.com/azure/cosmos-db/gremlin-support)和 SQL （核心）檔 API 層完全互通。 這對於在可程式性層級的不同模型之間切換有一些好處。 圖形存放區可以查詢複雜的網路周遊，以及在相同存放區中模型化為檔記錄的交易。

## <a name="fluid-schema"></a>流體架構

關係資料庫的另一個特殊特性是必須在設計階段定義架構。 這在參考完整性和資料的符合性方面有其優點。 不過，它也會在應用程式成長時受到限制。 針對共用相同資料表或資料庫定義的邏輯個別模型，回應架構中的變更，可能會隨著時間變得複雜。 這類使用案例通常會從 devolved 至應用程式的架構中獲益，以依據記錄進行管理。 這需要資料庫「架構中立」，並允許記錄在其中包含的資料方面以「自我描述」。

如果您要管理的資料，其結構經常以較高的速率變更，特別是當交易可能來自外部來源，而這很難在資料庫上強制符合性時，您可能會想要使用受控 NoSQL 資料庫服務（如 Azure Cosmos DB）來考慮更不適用架構的方法。

## <a name="microservices"></a>微服務

[微服務](https://en.wikipedia.org/wiki/Microservices)模式在最近幾年已經大幅增加。 此模式在[服務導向架構](https://en.wikipedia.org/wiki/Service-oriented_architecture)中有其根。 在這些現代化的微服務架構中，資料傳輸的標準是[JSON](https://en.wikipedia.org/wiki/JSON)，這也是大多數檔導向 NoSQL 資料庫的儲存媒體。 如此一來，NoSQL 檔存放區可讓您更順暢地配合複雜的微服務實作為持續性和同步處理（使用[事件來源模式](https://en.wikipedia.org/wiki/Event-driven_architecture)）。 較傳統的關係資料庫在這些架構中的維護方式可能更為複雜。 這是因為兩個 Api 之間的狀態和同步處理所需的轉換量較大。 Azure Cosmos DB 特別有許多功能，使其更順暢地符合 JSON 型微服務架構，而不是許多 NoSQL 資料庫：

* 純粹的 JSON 資料類型選擇
* 內建于資料庫中的 JavaScript 引擎和[查詢 API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) 。
* 一種最先進的[變更](https://docs.microsoft.com/azure/cosmos-db/change-feed)摘要，用戶端可以訂閱此摘要，以取得對容器的修改通知。

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 資料庫的一些挑戰

雖然在執行 NoSQL 資料庫時有一些明顯的優點，但您也可能會想要考慮一些挑戰。 使用關聯式模型時，這些可能不會有相同的程度：

* 具有多個關聯指向相同實體的交易。
* 跨整個資料集需要強式一致性的交易。

查看第一項挑戰，NoSQL 資料庫中的規則通常是反正規化的，如先前所述，在分散式系統中產生更有效率的讀取。 不過，這種方法有一些設計上的挑戰。 讓我們來看看與一個類別和多個標記相關的產品範例：

![聯結](./media/relational-or-nosql/many-joins.png)

NoSQL 檔資料庫中的最佳作法方法是直接在「產品檔」中反正規化類別名稱和標記名稱。 不過，為了讓類別、標記和產品保持同步，協助這項作業的設計選項已增加維護複雜度，因為資料會複製到產品中的多筆記錄，而不是「一對多」關聯性中的簡單更新，而是用來抓取資料的聯結。 

取捨是在反正規化記錄中的讀取效率較高，而且在概念上聯結的實體數目增加時，會變得越來越有效率。 不過，如同在反正規化記錄中增加聯結的實體數目而增加讀取效率，這也是讓實體保持同步的維護複雜度。緩和這項取捨的一種方法，就是建立[混合式資料模型](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)。

雖然 NoSQL 資料庫中有更多彈性可處理這些取捨，但增加彈性也會產生更多設計決策。 請參閱[如何在 Azure Cosmos DB 上使用真實世界的範例來建立模型和分割資料](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)的相關文章，其中包括讓未正規化的[使用者資料保持同步](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames)的方法，其中使用者不只位於不同的分割區，而是在不同的容器中。

關於強式一致性，在整個資料集上很少需要這種情況。 不過，在必要的情況下，分散式資料庫可能會是一項挑戰。 為了確保強式一致性，必須在所有複本和區域之間同步處理資料，才能讓用戶端讀取。 這可能會增加讀取的延遲。

同樣地，Azure Cosmos DB 提供比關係資料庫更多的彈性，以符合這裡所述的各種取捨，但針對小規模的執行，這種方法可能會加入更多的設計考慮。 如需有關此主題的詳細資訊，請參閱有關[一致性、可用性和效能取捨](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs)的文章。

## <a name="next-steps"></a>後續步驟

瞭解如何管理您的 Azure Cosmos 帳戶和其他概念：

* [如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [全球發佈](distribute-data-globally.md)
* [一致性層級](consistency-levels.md)
* [使用 Azure Cosmos 容器和項目](databases-containers-items.md)
* [Azure Cosmos 帳戶的 VNET 服務端點](vnet-service-endpoint.md)
* [Azure Cosmos 帳戶的 IP 防火牆](firewall-support.md)
* [如何在 Azure Cosmos 帳戶中新增和移除 Azure 區域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
