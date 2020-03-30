---
title: 瞭解 Azure 宇宙 DB NoSQL 和關係資料庫之間的差異
description: 本文列舉了 NoSQL 和關係資料庫之間的差異
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896619"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>瞭解 NoSQL 和關係資料庫之間的差異

本文將列舉 NoSQL 資料庫相對於關係資料庫的一些關鍵優勢。 我們還將討論使用 NoSQL 時面臨的一些挑戰。 有關深入瞭解存在的不同資料存儲，請查看我們關於[選擇正確資料存儲](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)的文章。

## <a name="high-throughput"></a>高輸送量

維護關係資料庫系統時最明顯的挑戰之一是，大多數關係引擎都應用鎖和閂鎖來強制實施嚴格的[ACID語義](https://en.wikipedia.org/wiki/ACID)。 此方法在確保資料庫中一致的資料狀態方面具有優勢。 但是，在併發性、延遲和可用性方面存在重大權衡。 由於這些基本的體系結構限制，高事務量可能會導致需要手動分片資料。 實施手動分片可能是一項耗時且痛苦的練習。

在這些情況下，[分散式資料庫](https://en.wikipedia.org/wiki/Distributed_database)可以提供更具可擴充性的解決方案。 但是，維護仍然是一項昂貴且耗時的工作。 管理員可能必須做額外的工作，以確保系統的分散式性質是透明的。 它們可能還必須考慮到資料庫的"斷開連接"性質。

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)通過跨所有 Azure 區域部署來簡化這些挑戰。 分區範圍能夠動態細分，以便根據應用程式無縫擴展資料庫，同時保持高可用性。 基於雲的原生資源治理具有細細微性的多租戶和嚴格控制，有助於[提供驚人的延遲保證](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency)和可預測的性能。 分區是完全託管的，因此管理員不必編寫代碼或管理分區。

如果事務卷達到極端級別（例如每秒數千個事務，則應考慮分散式 NoSQL 資料庫）。 考慮 Azure Cosmos DB 以實現最高的效率、易於維護並降低擁有權總成本。

![後端](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>階層式資料

存在大量用例，資料庫中的事務可以包含許多父子關係。 隨著時間的推移，這些關係可能會顯著增長，而且很難管理。 [分層資料庫](https://en.wikipedia.org/wiki/Hierarchical_database_model)的形式在20世紀80年代確實出現，但由於存儲效率低而不受歡迎。 隨著[Ted Codd的關係模型](https://en.wikipedia.org/wiki/Relational_model)成為幾乎所有主流資料庫管理系統使用的實際標準，它們也失去了吸引力。

然而，如今文檔式資料庫的普及程度已顯著增強。 這些資料庫可能被視為對分層資料庫范式的重新塑造，現在不受有關磁片上存儲資料成本的擔憂的影響。 因此，與面向檔的現代方法相比，在關係資料庫中維護許多複雜的父子實體關聯現在可被視為反模式。

[物件導向設計](https://en.wikipedia.org/wiki/Object-oriented_design)的出現，以及將其與關係模型結合時出現的[阻抗不匹配](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)，也突出了某些用例的關係資料庫中的反模式。 因此，可能會產生隱藏但通常重要的維護成本。 儘管[ORM方法](https://en.wikipedia.org/wiki/Object-relational_mapping)已經發展到部分緩解這一點，但面向文檔的資料庫仍更好地與物件導向的方法結合在一起。 使用這種方法，開發人員不必被迫致力於 ORM 驅動程式或自訂語言特定的[OO 資料庫引擎](https://en.wikipedia.org/wiki/Object_database)。 如果資料包含許多父子關係和深層層次結構，則可能需要考慮使用 NoSQL 文檔資料庫，如[Azure Cosmos DB SQL API](https://docs.microsoft.com/azure/cosmos-db/introduction)。

![訂單詳情](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>複雜的網路和關係

具有諷刺意味的是，以它們的名字命名，關係資料庫為類比深層和複雜關係提供了一個不太理想的解決方案。 原因是實體之間的關係實際上不存在在關係資料庫中。 它們需要在運行時進行計算，複雜的關係需要點菜聯接才能使用查詢進行映射。 因此，隨著關係的增加，操作在計算方面變得成倍增長。 在某些情況下，嘗試管理此類實體的關係資料庫將不可用。

在關係資料庫出現期間，確實出現了各種形式的"網路"資料庫，但與分層資料庫一樣，這些系統很難獲得普及。 採用緩慢是由於當時缺少用例和存儲效率低下。 今天，圖形資料庫引擎可以被認為是網路資料庫范式的重新出現。 這些系統的主要好處是，關係在資料庫中存儲為"一流公民"。 因此，遍歷關係可以在恒定的時間內完成，而不是增加每個新聯接或交叉產品的時間複雜性。

如果要在資料庫中維護複雜的關係網絡，則可能需要考慮圖形資料庫，如用於管理此資料的[Azure Cosmos DB Gremlin API。](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)

![圖形](./media/relational-or-nosql/graph.png)

Azure Cosmos DB 是一種多模型資料庫服務，它為所有主要的 NoSQL 模型類型提供 API 投影;列系列、文檔、圖形和鍵值。 [Gremlin（圖形）](https://docs.microsoft.com/azure/cosmos-db/gremlin-support)和 SQL（核心）文檔 API 層是完全可交互操作的。 這在可程式設計性級別的不同模型之間切換具有優勢。 圖形存儲可以同時查詢複雜的網路遍歷以及在同一存儲中建模為文檔記錄的事務。

## <a name="fluid-schema"></a>流體架構

關係資料庫的另一個特殊特徵是，架構需要在設計時定義。 這在參考完整性和資料一致性方面具有優勢。 但是，隨著應用程式的增長，它也可能受到限制。 隨著時間的推移，跨邏輯上獨立的模型對共用同一表或資料庫定義的更改回應可能會變得複雜。 此類用例通常受益于將架構移交給應用程式，以便按記錄進行管理。 這要求資料庫具有"不可知的架構"，並允許記錄在包含的資料方面"自我描述"。

如果要管理結構不斷以高速率變化的資料，特別是如果事務可能來自外部源，而外部源很難在整個資料庫中強制實施一致性，則可能需要考慮一種與架構無關的方法使用託管的 NoSQL 資料庫服務（如 Azure Cosmos DB）。

## <a name="microservices"></a>微服務

近年來，[微服務](https://en.wikipedia.org/wiki/Microservices)模式有了顯著增長。 這種模式起源于[面向服務的體系結構](https://en.wikipedia.org/wiki/Service-oriented_architecture)。 這些現代微服務體系結構中資料傳輸的實際標準是[JSON，JSON](https://en.wikipedia.org/wiki/JSON)恰好也是絕大多數面向文檔的 NoSQL 資料庫的存儲介質。 這使得 NoSQL 文檔存儲更加無縫地適用于複雜微服務實現的持久性和同步（使用[事件源模式](https://en.wikipedia.org/wiki/Event-driven_architecture)）。 在這些體系結構中，更傳統的關係資料庫可以維護得多。 這是因為跨 API 的狀態和同步所需的轉換量更大。 Azure Cosmos DB 尤其具有許多功能，使其比許多 NoSQL 資料庫更無縫地適合基於 JSON 的微服務體系結構：

* 純 JSON 資料類型的選擇
* 內置於資料庫中的 JavaScript 引擎和[查詢 API。](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api)
* 用戶端可以訂閱的最先進的[更改源](https://docs.microsoft.com/azure/cosmos-db/change-feed)，以便收到對容器的修改通知。

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 資料庫的一些挑戰

儘管實現 NoSQL 資料庫時有一些明顯的優勢，但您可能還需要考慮一些挑戰。 在使用關係模型時，這些可能不存在到相同的程度：

* 具有許多關係指向同一實體的事務。
* 需要整個資料集的強一致性的事務。

看第一個挑戰，NoSQL 資料庫中的拇指規則通常是非正常化的，如前面所述，在分散式系統中生成更有效的讀取。 但是，這種方法帶來了一些設計挑戰。 讓我們舉一個與一個類別和多個標籤相關的產品示例：

![聯結](./media/relational-or-nosql/many-joins.png)

NoSQL 文檔資料庫中的一個最佳實踐方法是直接在"產品文檔中"中取消類別名稱和標記名稱的正常化。 但是，為了保持類別、標籤和產品同步，便於實現的設計選項增加了維護複雜性，因為資料在產品中的多個記錄中重複，而不是在"一對多"中進行簡單的更新關係和用於檢索資料的聯接。 

權衡是，讀取在非正常化記錄中更有效率，並且隨著概念聯接實體數量的增加而變得越來越高效。 但是，正如讀取效率隨著在非正常化記錄中聯接的實體數量的增加而增加一樣，保持實體同步的維護複雜性也也在增加。緩解這種權衡的一個方法是創建一個[混合資料模型](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)。

雖然 NoSQL 資料庫中具有更大的靈活性來處理這些權衡，但更大的靈活性也可以產生更多的設計決策。 請參閱我們的文章[，如何使用實際示例對 Azure Cosmos DB 上的資料進行建模和分區](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)，其中包括一種使[非正常化使用者資料保持同步](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames)的方法，其中使用者不僅位於不同的分區中，而且位於不同的容器中。

對於強一致性，在整個資料集中很少需要這樣做。 但是，在必要的情況下，在分散式資料庫中可能是一個挑戰。 為了確保強一致性，資料需要在所有副本和區域之間同步，然後才能允許用戶端讀取它。 這會增加讀取的延遲。

同樣，Azure Cosmos DB 為此處相關的各種權衡提供了比關係資料庫更大的靈活性，但對於小規模實現，此方法可能會添加更多設計注意事項。 有關本主題的更多詳細資訊，請參閱我們關於[一致性、可用性和性能權衡](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs)的文章。

## <a name="next-steps"></a>後續步驟

瞭解如何管理 Azure Cosmos 帳戶和其他概念：

* [如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [全球分銷](distribute-data-globally.md)
* [一致性層級](consistency-levels.md)
* [使用 Azure Cosmos 容器和項目](databases-containers-items.md)
* [Azure Cosmos 帳戶的 VNET 服務端點](vnet-service-endpoint.md)
* [Azure Cosmos 帳戶的 IP 防火牆](firewall-support.md)
* [如何在 Azure Cosmos 帳戶中新增和移除 Azure 區域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
