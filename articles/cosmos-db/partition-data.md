---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 瞭解資料分割在 Azure Cosmos DB 中的運作方式、如何設定資料分割和分割區索引鍵，以及如何為您的應用程式選擇正確的分割區索引鍵。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246613"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

本文說明 Azure Cosmos DB 中的實體和邏輯分割區。 其也會討論調整和分割的最佳做法。 

## <a name="logical-partitions"></a>邏輯分割區

邏輯分割區是由具有相同分割區索引鍵的一組專案所組成。 例如，在所有專案都包含 `City` 屬性的容器中，您可以使用 `City` 做為容器的分割區索引鍵。 具有 `City`之特定值的專案群組，例如 `London`、`Paris`和 `NYC`，會形成不同的邏輯分割區。 刪除基礎資料時，您不必擔心刪除資料分割。

在 Azure Cosmos DB 中，容器是延展性的基本單位。 新增至容器的資料，以及您在容器上布建的輸送量，會自動（水準）分割在一組邏輯分割區上。 資料和輸送量會根據您為 Azure Cosmos 容器指定的分割區索引鍵進行分割。 如需詳細資訊，請參閱[建立 Azure Cosmos 容器](how-to-create-container.md)。

邏輯分割區也會定義資料庫交易的範圍。 您可以使用[具有快照集隔離的交易](database-transactions-optimistic-concurrency.md)來更新邏輯分割區內的專案。 將新的專案新增至容器時，系統會以透明的方式建立新的邏輯分割區。

## <a name="physical-partitions"></a>實體分割區

Azure Cosmos 容器的調整方式是將資料和輸送量分散到大量的邏輯分割區。 就內部而言，一或多個邏輯分割區會對應至由一組複本（也稱為[*複本集*](global-dist-under-the-hood.md)）所組成的實體分割區。 每個複本集都裝載 Azure Cosmos 資料庫引擎的實例。 複本集可讓儲存在實體分割區中的資料持久、高可用性且一致。 實體分割區支援儲存體和要求單位（ru）的最大數量。 組成實體分割區的每個複本都會繼承資料分割的儲存配額。 實體分割區的所有複本會共同支援配置給實體分割區的輸送量。 

下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

![示範 Azure Cosmos DB 資料分割的影像](./media/partition-data/logical-partitions.png)

為容器布建的輸送量會平均分散于實體分割區。 不會平均散發輸送量要求的資料分割索引鍵設計，可能會建立「經常性」的磁碟分割。 熱分割區可能會導致速率限制，並以不有效率的方式使用布建的輸送量，以及更高的成本。

與邏輯分割區不同，實體分割區是系統的內部實作。 您無法控制實體分割區的大小、位置或計數，也無法控制邏輯分割區與實體分割區之間的對應。 不過，您可以[選擇正確的邏輯分割](partitioning-overview.md#choose-partitionkey)區索引鍵，以控制邏輯分區的數目，以及資料、工作負載和輸送量的散發。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[選擇分割](partitioning-overview.md#choose-partitionkey)區索引鍵。
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
