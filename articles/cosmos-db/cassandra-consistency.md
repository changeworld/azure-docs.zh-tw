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
ms.openlocfilehash: a02076c09d038b02c0ab846440ad14e799271733
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339949"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra 和 Azure Cosmos DB 一致性層級
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

不同于 Azure Cosmos DB，Apache Cassandra 本身並不提供精確定義的一致性保證。 取而代之的是，Apache Cassandra 會提供「寫入」一致性層級和「讀取」一致性層級，以實現高可用性、一致性和延遲的取捨。 使用 Azure Cosmos DB 的 Cassandra API 時：

* Apache Cassandra 的寫入一致性層級會對應至 Azure Cosmos 帳戶上設定的預設一致性層級。  (CL) 的寫入作業一致性，無法根據每個要求來變更。

* Azure Cosmos DB 會動態地將 Cassandra 用戶端驅動程式所指定的讀取一致性層級對應到讀取要求上動態設定的其中一個 Azure Cosmos DB 一致性層級。

## <a name="mapping-consistency-levels"></a>對應一致性層級

下表說明使用 Cassandra API 時，原生 Cassandra 一致性層級如何對應至 Azure Cosmos DB 的一致性層級：  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra 一致性模型對應" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

如果您的 Azure Cosmos 帳戶設定了強式一致性以外的一致性層級，您可以藉由查看 *機率限定過期* (PBS) 計量，找出用戶端可能會針對您的工作負載取得強式和一致讀取的可能性。 此計量會在 Azure 入口網站公開，若要深入了解，請參閱[監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

機率性限定過期會顯示您最終一致性的最終結果。 此計量可讓您深入瞭解您在 Azure Cosmos 帳戶上目前設定的一致性層級時，可以獲得更強的一致性的頻率。 換句話說，您可以看到針對寫入和讀取區域的組合取得強式一致性讀取的機率 (以毫秒來測量)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Cosmos DB 的全域散發和一致性層級：

* [全球發佈概觀](distribute-data-globally.md)
* [一致性層級總覽](consistency-levels.md)
* [高可用性](high-availability.md)
