---
title: Apache Cassandra 和 Azure Cosmos DB 一致性層級
description: Apache Cassandra 和 Azure Cosmos DB 一致性層級。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516813"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra 和 Azure Cosmos DB Cassandra API 一致性層級
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

不同于 Azure Cosmos DB，Apache Cassandra 本身並不提供精確定義的一致性保證。 取而代之的是，Apache Cassandra 會提供「寫入」一致性層級和「讀取」一致性層級，以實現高可用性、一致性和延遲的取捨。 使用 Azure Cosmos DB 的 Cassandra API 時：

* Apache Cassandra 的寫入一致性層級會對應至 Azure Cosmos 帳戶上設定的預設一致性層級。  (CL) 的寫入作業一致性，無法根據每個要求來變更。

* Azure Cosmos DB 會動態地將 Cassandra 用戶端驅動程式所指定的讀取一致性層級對應到讀取要求上動態設定的其中一個 Azure Cosmos DB 一致性層級。

## <a name="multi-region-writes-vs-single-region-writes"></a>多重區域寫入與單一區域寫入

Apache Cassandra 資料庫預設為多宿主系統，且不會提供現成的選項，可用於讀取的多重區域複寫的單一區域寫入。 不過，Azure Cosmos DB 提供了單一區域或 [多重區域](how-to-multi-master.md) 寫入設定的內部功能。 能夠在多個區域之間選擇單一區域寫入設定的優點之一，就是規避跨區域衝突案例，以及在多個區域之間維護強式一致性的選項。 

透過單一區域寫入，您可以維持強式一致性，同時維持跨區域的高可用性，並具備 [自動容錯移轉](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)。 在這項設定中，您仍然可以利用資料位置，藉由根據要求降級為最終一致性，來降低讀取延遲。 除了這些功能之外，Azure Cosmos DB 平臺也提供在選取區域時啟用 [區域冗余](high-availability.md#availability-zone-support) 的功能。 因此，不同于原生 Apache Cassandra，Azure Cosmos DB 可讓 [您以更細微的方式流覽](consistency-levels.md#rto) CAP 定理的範圍。

## <a name="mapping-consistency-levels"></a>對應一致性層級

Azure Cosmos DB 平臺提供一組五個定義完善、以商務使用案例為導向的一致性設定，並與 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem) 和 [PACLC 定理](https://en.wikipedia.org/wiki/PACELC_theorem)所定義的取捨相關。 因為此方法與 Apache Cassandra 明顯不同，所以建議您花時間複習和瞭解 [檔](consistency-levels.md)中 Azure Cosmos DB 一致性設定，或觀賞 [這段短片](https://www.youtube.com/watch?v=t1--kZjrG-o) ，瞭解 Azure Cosmos DB 平臺的一致性設定。

下表說明使用 Cassandra API 時，Apache Cassandra 和 Azure Cosmos DB 一致性層級之間的可能對應。 這會顯示單一區域的設定、單一區域寫入的多重區域讀取，以及多重區域寫入。

> [!NOTE]
> 這些都不是完全對應。 相反地，我們已提供最接近 Apache Cassandra 的雷同，並清楚說明最右邊的資料行中的任何定性差異。 如前所述，我們建議您檢查 Azure Cosmos DB 的 [一致性設定](consistency-levels.md)。 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Cassandra 一致性帳戶層級對應" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Cassandra 一致性動態對應" lightbox="./media/cassandra-consistency/dynamic.png" :::

如果您的 Azure Cosmos 帳戶設定了強式一致性以外的一致性層級，您可以藉由查看 *機率限定過期* (PBS) 計量，找出用戶端可能會針對您的工作負載取得強式和一致讀取的可能性。 此計量會在 Azure 入口網站公開，若要深入了解，請參閱[監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

機率性限定過期會顯示您最終一致性的最終結果。 此計量可讓您深入瞭解您在 Azure Cosmos 帳戶上目前設定的一致性層級時，可以獲得更強的一致性的頻率。 換句話說，您可以看到針對寫入和讀取區域的組合取得強式一致性讀取的機率 (以毫秒來測量)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Cosmos DB 的全域散發和一致性層級：

* [全球發佈概觀](distribute-data-globally.md)
* [一致性層級總覽](consistency-levels.md)
* [高可用性](high-availability.md)
