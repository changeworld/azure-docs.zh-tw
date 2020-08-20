---
title: Azure Cosmos DB 中以耗用量為基礎的無伺服器供應專案
description: 深入瞭解 Azure Cosmos DB 的以耗用量為基礎的無伺服器供應專案。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: d99deea8271a3f9e630a7d2e8cd9f73bd41645a2
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608744"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB 無伺服器 (預覽) 

> [!IMPORTANT]
> Azure Cosmos DB 無伺服器目前為預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 無伺服器，可讓您以耗用量方式使用您的 Azure Cosmos 帳戶，您只需支付資料庫作業所耗用的要求單位，以及資料所耗用的儲存體。 在無伺服器模式中使用 Azure Cosmos DB 時，不會產生最低費用。

> [!IMPORTANT] 
> 您對無伺服器有任何意見嗎？ 我們想要聽聽看！ 您可以隨意將訊息放到 Azure Cosmos DB 無伺服器團隊： [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) 。

使用 Azure Cosmos DB 時，每個資料庫作業都會有以 [要求單位](request-units.md)表示的成本。 此成本的計費方式，取決於您所使用的 Azure Cosmos 帳戶類型：

- 在布 [建的輸送量](set-throughput.md) 模式中，您必須認可特定數量的輸送量， (以您的資料庫和容器上布建的每) 秒要求單位來表示。 然後，您可以從每秒可用的要求單位數扣除資料庫作業的成本。 在您的計費期間結束時，您需要支付已布建的輸送量量。
- 在無伺服器模式中，在 Azure Cosmos 帳戶中建立容器時，您不需要布建任何輸送量。 在您的計費期間結束時，您會收到資料庫作業所耗用的要求單位數目的費用。

## <a name="use-cases"></a>使用案例

Azure Cosmos DB 無伺服器最適合您預期的案例：

- **輕量流量**：由於在這種情況下不需要布建容量，而且可能是成本高昂的
- **適中 burstability**：由於無伺服器容器最多可傳遞每秒5000個要求單位
- **適中效能**：由於無伺服器容器具有 [特定的效能特性](#performance)

基於這些理由，Azure Cosmos DB 無伺服器應考慮下列類型的工作負載：

- 部署
- 測試
- 原型設計
- 概念證明
- 具有輕量流量的非關鍵性應用程式

請參閱 [如何選擇布建的輸送量和無伺服器](throughput-serverless.md) 文章，以取得如何選擇最適合您使用案例的供應專案的詳細指引。

## <a name="using-serverless-resources"></a>使用無伺服器資源

無伺服器是新的 Azure Cosmos 帳戶類型，這表示在建立新帳戶時，您必須在布 **建的輸送量** 和 **無伺服器** 之間進行選擇。 您必須建立新的無伺服器帳戶，才能開始使用無伺服器。 在預覽版本中，唯一支援的方法是 [使用 Azure 入口網站](create-cosmosdb-resources-portal.md)來建立新的無伺服器帳戶。 目前不支援從無伺服器模式遷移現有的帳戶。

> [!NOTE]
> Azure Cosmos DB Core (SQL) API 目前僅支援無伺服器。

在無伺服器帳戶中建立的任何容器都是無伺服器容器。 無伺服器容器會公開與在布建的輸送量模式中建立之容器相同的功能，因此您可以用完全相同的方式讀取、寫入及查詢您的資料。 但是無伺服器帳戶和容器也有特定特性：

> [!IMPORTANT]
> 當無伺服器正式推出，且 **您的意見** 反應將協助我們進行決定時，可能會分階段減緩或移除其中的一些限制。 請與我們分享您無伺服器體驗的詳細資訊： [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) 。

- 無伺服器帳戶只能在單一 Azure 區域中執行。 您無法在建立其他 Azure 區域之後，將其新增至無伺服器帳戶。
- 不可能在無伺服器帳戶上啟用 [Synapse 連結預覽功能](synapse-link.md) 。
- 無伺服器容器上不需要布建輸送量，因此下列陳述適用：
    - 您無法在建立無伺服器容器時傳遞任何輸送量，因此會傳回錯誤。
    - 您無法讀取或更新無伺服器容器的輸送量，因此會傳回錯誤。
    - 您無法在無伺服器帳戶中建立共用輸送量資料庫，而且這麼做會傳回錯誤。
- 無伺服器容器可提供每秒5000要求單位的最大輸送量 burstability。
- 無伺服器容器最多可以儲存 50 GB 的資料和索引。

## <a name="monitoring-your-consumption"></a>監視您的耗用量

如果您之前已在布建的輸送量模式中使用 Azure Cosmos DB，您會發現當您的流量不會證明布建的容量時，無伺服器會更符合成本效益。 權衡代價是，您的成本將會變得比較不容易預測，因為您是根據資料庫處理的要求數來計費。 因此，請務必留意您目前的耗用量。

流覽您帳戶的 [**計量**] 窗格時，您會在 [**總覽**] 索引標籤底下找到名為「**要求單位**」的圖表。此圖表會顯示您的帳戶已取用的要求單位數目：

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="顯示已使用要求單位的圖表" border="false":::

使用 Azure 監視器時，您可以找到相同的圖表，如 [這裡](monitor-request-unit-usage.md)所述。 請注意，Azure 監視器可讓您設定 [警示](../azure-monitor/platform/alerts-metric-overview.md)，以在您的要求單位耗用量超過特定閾值時用來通知您。

## <a name="performance"></a><a id="performance"></a>效能

無伺服器資源會產生與布建的輸送量資源所提供不同的特定效能特性：

- **可用性**：當無伺服器供應專案正式推出之後，如果未使用可用性區域 (區域冗余) ，則服務等級協定 (SLA) 99.9%，將會涵蓋無伺服器容器的可用性。 使用可用性區域時，SLA 為99.99%。
- **延遲**：在無伺服器供應專案正式推出之後，無伺服器容器的延遲將會由服務等級目標（ (SLO) 10 毫秒或更低的時間點讀取，以及30毫秒或更少的寫入）所涵蓋。 點讀取作業包含依識別碼和分割區索引鍵值來提取單一專案。
- **Burstability**：在無伺服器供應專案正式推出之後，服務等級目標將會涵蓋無伺服器容器的 BURSTABILITY (SLO) 95%。 這表示最大 burstability 可達至少95% 的時間。

> [!NOTE]
> 如同任何 Azure 預覽版，Azure Cosmos DB 無伺服器的服務等級協定 (SLA) 。 上述的效能特性可作為此供應專案在正式推出時將提供的內容預覽。

## <a name="next-steps"></a>後續步驟

透過下列文章開始使用無伺服器：

- [Azure Cosmos DB 中的要求單位](request-units.md)
- [選擇布建的輸送量和無伺服器](throughput-serverless.md)
- [Azure Cosmos DB 中的計價模式](how-pricing-works.md)
