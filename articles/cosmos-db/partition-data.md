---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 瞭解資料分割在 Azure Cosmos DB 中的運作方式、如何設定資料分割和分割區索引鍵，以及如何為您的應用程式選擇正確的分割區索引鍵。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 57417a80ea83005c01b6f2a17206d46e6c049719
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112773"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

本文說明邏輯與實體分割區之間的關聯性。 它也會討論分割的最佳做法，並提供水準調整在 Azure Cosmos DB 中的運作方式深入瞭解。 您不需要瞭解這些內部詳細資料來[選取您的分割](partitioning-overview.md#choose-partitionkey)區索引鍵，但我們已加以討論，讓您清楚瞭解 Azure Cosmos DB 的運作方式。

## <a name="logical-partitions"></a>邏輯分割區

邏輯分割區是由具有相同分割區索引鍵的一組專案所組成。 例如，在包含食物營養相關資料的容器中，所有專案都包含一個 `foodGroup` 屬性。 您可以使用 `foodGroup` 做為容器的分割區索引鍵。 具有特定值的專案群組 `foodGroup` ，例如 `Beef Products` 、 `Baked Products` 和 `Sausages and Luncheon Meats` ，會形成不同的邏輯分割區。 刪除基礎資料時，您不需要擔心刪除邏輯分割區。

邏輯分割區也會定義資料庫交易的範圍。 您可以使用[具有快照集隔離的交易](database-transactions-optimistic-concurrency.md)來更新邏輯分割區內的專案。 將新的專案新增至容器時，系統會以透明的方式建立新的邏輯分割區。

您的容器中的邏輯分割區數目沒有限制。 每個邏輯分割區最多可以儲存 20 gb 的資料。 良好的資料分割索引鍵選擇有範圍廣泛的可能值。 例如，在所有專案都包含屬性的容器中 `foodGroup` ，邏輯分割區內的資料 `Beef Products` 可能會成長到 20 gb。 [選取](partitioning-overview.md#choose-partitionkey)具有各種可能值的分割區索引鍵，可確保容器能夠進行調整。

## <a name="physical-partitions"></a>實體分割區

Azure Cosmos 容器的調整方式是將資料和輸送量分散到實體分割區。 就內部而言，一或多個邏輯分割區會對應至單一實體資料分割。 大部分的小型 Cosmos 容器都有許多邏輯分割區，但只需要單一實體磁碟分割。 不同于邏輯分割區，實體分割區是系統的內部實作為，而且完全受 Azure Cosmos DB 管理。

Cosmos 容器中的實體分割區數目取決於下列各項：

- 布建的輸送量（每個個別實體分割區最多可提供每秒10000個要求單位的輸送量）
- 資料儲存體總計（每個個別實體分割區最多可儲存50GB）

您的容器中的實體分割區總數沒有限制。 當您布建的輸送量或資料大小增加時，Azure Cosmos DB 會藉由分割現有的方式來自動建立新的實體磁碟分割。 實體分割區分割不會影響應用程式的可用性。 實體分割區分割之後，單一邏輯分割區內的所有資料仍會儲存在相同的實體分割區上。 實體分割區只會建立邏輯分割區與實體分割區的新對應。

為容器布建的輸送量會平均分散于實體分割區。 不會平均散發輸送量要求的資料分割索引鍵設計，可能會建立「經常性」的磁碟分割。 熱分割區可能會導致速率限制，並以不有效率的方式使用布建的輸送量，以及更高的成本。

在 Azure 入口網站的 [**計量**] 分頁的 [儲存體] 區段中，您可以看到容器的實體分割**區**：

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="查看實體分割區的數目" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

在此範例容器中，我們已選擇 `/foodGroup` 做為分割區索引鍵，這三個矩形分別代表一個實體分割區。 在映射中，資料**分割索引鍵範圍**與實體分割區相同。 選取的實體磁碟分割包含三個邏輯分割區： `Beef Products` 、 `Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies` 。

如果我們布建每秒18000個要求單位（RU/秒）的輸送量，則這三個實體分割區中的每一個都可以利用1/3 的總布建輸送量。 在選取的實體分割區中，邏輯分割區索引鍵 `Beef Products` `Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies` 可以共同使用實體分割區的6000布建 RU/秒。 由於布建的輸送量會平均分配給您容器的實體分割區，因此請務必選擇[正確的邏輯分割](partitioning-overview.md#choose-partitionkey)區索引鍵，以平均分配輸送量耗用量的分割區索引鍵。 如果您選擇的分割區索引鍵會將輸送量耗用量平均分散到邏輯分割區，您將可確保跨實體分割區的輸送量耗用量已達到平衡。

## <a name="replica-sets"></a>複本集

每個實體分割區都是由一組複本（也稱為[*複本集*](global-dist-under-the-hood.md)）所組成。 每個複本集都裝載 Azure Cosmos 資料庫引擎的實例。 複本集可讓儲存在實體分割區中的資料持久、高可用性且一致。 組成實體分割區的每個複本都會繼承資料分割的儲存配額。 實體分割區的所有複本會共同支援配置給實體分割區的輸送量。 Azure Cosmos DB 會自動管理複本集。

大部分的小型 Cosmos 容器只需要單一實體分割區，但仍然會有至少4個複本。

下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="示範 Azure Cosmos DB 資料分割的影像" border="false":::

## <a name="next-steps"></a>後續步驟

* 瞭解如何[選擇分割](partitioning-overview.md#choose-partitionkey)區索引鍵。
* 瞭解[Azure Cosmos DB 中布建的輸送量](request-units.md)。
* 深入瞭解[Azure Cosmos DB 中的全域散發](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
