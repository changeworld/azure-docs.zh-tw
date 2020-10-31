---
title: 對應適用于 MongoDB 的 Azure Cosmos DB API 一致性層級
description: 對應 Azure Cosmos DB API for MongoDB 的一致性層級。
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 37c128a42ca68134b770b32c940d59834261ce44
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096575"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>適用于 MongoDB 的 Azure Cosmos DB 和 API 的一致性層級
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

不同于 Azure Cosmos DB，原生 MongoDB 不提供精確定義的一致性保證。 相反地，原生 MongoDB 可讓使用者設定下列一致性保證：寫入考慮、讀取考慮和 isMaster 指示詞，以將讀取作業導向至主要或次要複本，以達成所需的一致性層級。

使用 Azure Cosmos DB 適用于 MongoDB 的 API 時，MongoDB 驅動程式會將您的寫入區域視為主要複本，而所有其他區域都是讀取複本。 您可以選擇與您的 Azure Cosmos 帳戶相關聯的區域做為主要複本。

> [!NOTE]
> Azure Cosmos DB 的預設一致性模型為 Session。 Session 是以用戶端為中心的一致性模型，不是 Cassandra 或 MongoDB 的原生支援。 如需有關要選擇的一致性模型的詳細資訊，請參閱 [Azure Cosmos DB 中的一致性層級](consistency-levels.md)

使用 Azure Cosmos DB 適用于 MongoDB 的 API 時：

* 寫入考慮會對應至 Azure Cosmos 帳戶上設定的預設一致性層級。

* Azure Cosmos DB 會將 MongoDB 用戶端驅動程式所指定的讀取考慮動態對應至讀取要求上動態設定的其中一個 Azure Cosmos DB 一致性層級。  

* 您可以將區域設為第一個可寫入的區域，以將與您的 Azure Cosmos 帳戶相關聯的特定區域標注為「主要」。 

## <a name="mapping-consistency-levels"></a>對應一致性層級

下表說明使用 Azure Cosmos DB 的 MongoDB API 時，原生 MongoDB 寫入/讀取的考慮如何對應至 Azure Cosmos 一致性層級：

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB 一致性模型對應" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

如果您的 Azure Cosmos 帳戶設定了強式一致性以外的一致性層級，您可以藉由查看 *機率限定過期* (PBS) 計量，找出用戶端可能會針對您的工作負載取得強式和一致讀取的可能性。 此計量會在 Azure 入口網站公開，若要深入了解，請參閱[監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

機率性限定過期會顯示您最終一致性的最終結果。 此計量可讓您深入瞭解您在 Azure Cosmos 帳戶上目前設定的一致性層級時，可以獲得更強的一致性的頻率。 換句話說，您可以看到針對寫入和讀取區域的組合取得強式一致性讀取的機率 (以毫秒來測量)。

## <a name="next-steps"></a>下一步

深入瞭解 Azure Cosmos DB 的全域散發和一致性層級：

* [全球發佈概觀](distribute-data-globally.md)
* [一致性層級總覽](consistency-levels.md)
* [高可用性](high-availability.md)
