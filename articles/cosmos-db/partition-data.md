---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 瞭解分區在 Azure Cosmos DB 中的工作原理、如何配置分區和分區金鑰以及如何為應用程式選擇正確的分區金鑰。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246613"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

本文說明 Azure Cosmos DB 中的實體和邏輯分割區。 其也會討論調整和分割的最佳做法。 

## <a name="logical-partitions"></a>邏輯分割區

邏輯分區由一組具有相同分區鍵的項組成。 例如，在所有項都包含`City`屬性的容器中，可以使用`City`作為容器的分區鍵。 具有`City`特定值（如`London`）`Paris`和`NYC`的項組形成不同的邏輯分區。 刪除基礎資料時，不必擔心刪除分區。

在 Azure Cosmos DB 中，容器是延展性的基本單位。 添加到容器的資料以及您在容器上預配的輸送量將自動（水準）跨一組邏輯分區進行分區。 資料和輸送量根據為 Azure Cosmos 容器指定的分區鍵進行分區。 有關詳細資訊，請參閱創建[Azure Cosmos 容器](how-to-create-container.md)。

邏輯分區還定義資料庫事務的範圍。 可以使用[具有快照隔離的事務](database-transactions-optimistic-concurrency.md)來更新邏輯分區中的項。 將新專案添加到容器時，系統會透明地創建新的邏輯分區。

## <a name="physical-partitions"></a>實體分割區

通過跨大量邏輯分區分發資料和輸送量，可以縮放 Azure Cosmos 容器。 在內部，一個或多個邏輯分區映射到由一組副本組成的物理分區，也稱為[*複本集*](global-dist-under-the-hood.md)。 每個複本集都承載 Azure Cosmos 資料庫引擎的實例。 複本集使存儲在物理分區中的資料持久、高度可用且一致。 物理分區支援最大數量的存儲和請求單位 （R）。 組成物理分區的每個副本都繼承分區的存儲配額。 物理分區的所有副本都統一支援分配給物理分區的輸送量。 

下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

![演示 Azure 宇宙資料庫分區的圖像](./media/partition-data/logical-partitions.png)

為容器預配的輸送量在物理分區之間平均分配。 不均勻分配輸送量請求的分區金鑰設計可能會創建"熱"分區。 熱分區可能會導致速率限制和預配輸送量的使用效率低下，以及更高的成本。

與邏輯分割區不同，實體分割區是系統的內部實作。 您無法控制物理分區的大小、位置或計數，也不能控制邏輯分區和物理分區之間的映射。 但是，您可以通過[選擇正確的邏輯分區鍵](partitioning-overview.md#choose-partitionkey)來控制邏輯分區的數量以及資料、工作負載和輸送量的分佈。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[選擇分區鍵](partitioning-overview.md#choose-partitionkey)。
* 瞭解[Azure 宇宙 DB 中的預配輸送量](request-units.md)。
* 瞭解[Azure 宇宙 DB 中的全域分發](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
