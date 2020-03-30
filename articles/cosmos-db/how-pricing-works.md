---
title: Azure Cosmos DB 的計價模式
description: 本文說明 Azure Cosmos DB 的計價模式，以及它如何簡化您的成本管理和成本規劃。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977545"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB 的計價模式 

Azure Cosmos DB 的計價模式可簡化成本管理和規劃。 使用 Azure Cosmos DB，您需要支付所佈建的輸送量及所取用的儲存體。

* **預配輸送量**：預配輸送量（也稱為預留輸送量）可保證任何規模的高性能。 您指定需要的輸送量 (RU/秒)，Azure Cosmos DB 會致力於提供保證已設定輸送量所需的資源。 您須按小時支付指定小時的最大佈建輸送量費用。

   > [!NOTE]
   > 因為佈建的輸送量模型會致力於提供容器或資料庫的資源，即使您未執行任何工作負載，仍須支付佈建的輸送量費用。

* **消耗的存儲**：對於為給定小時消耗的存儲總量 （GB） 和索引，您需要收取統一費率。

佈建的輸送量指定為每秒[要求單位](request-units.md) (RU/秒)，可讓您從容器或資料庫讀取資料或將資料寫入其中。 您可以[在資料庫或容器上佈建輸送量](set-throughput.md)。 根據工作負載需求，您可以隨時相應增加/相應減少輸送量。 Azure Cosmos DB 定價具有彈性，且與您在資料庫或容器上設定的輸送量成正比。 輸送量和儲存體值下限與縮放增量為所有階層的客戶，提供從小型到大型容器的完整價格與彈性範圍。 每個資料庫或容器是按小時以 100 RU/秒為單位計費，最少 400 RU/秒，取用的儲存體以 GB 為單位。 與佈建的輸送量不同，儲存體是按取用量來計費。 也就是說，您不必事先保留任何儲存體。 您只須支付所取用的儲存體費用。

如需詳細資訊，請參閱 [Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)和[了解您的 Azure Cosmos DB 計費](understand-your-bill.md)。

Azure Cosmos DB 的計價模式在所有 API 之間都是一致的。 若要深入了解，請參閱 [Azure Cosmos DB 計價模式如何為客戶提供符合成本效益的解決方案](total-cost-ownership.md)。 資料庫或容器有必要的最小輸送量，以確保 SLA 和您可以每 100 RU/秒 6 元的價格，增加或減少佈建的輸送量。

目前資料庫和容器型輸送量的最低價格是 24 元/月，如需最新資訊，請參閱 [Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。 如果您的工作負載使用多個容器，可以藉由使用資料庫層級輸送量，針對成本進行最佳化，因為資料庫層級輸送量可讓您在資料庫中具有任意數量的容器，在容器之間共用輸送量。 下表摘要說明不同實體的佈建的輸送量和成本：

|**實體**  | **輸送量下限和成本** |**縮放增量和成本** |**佈建範圍** |
|---------|---------|---------|-------|
|資料庫    | 400 RU/秒 (24 元/月)    | 100 RU/秒 (6 元/月)   |輸送量是針對資料庫保留，可供資料庫內的容器共用 |
|容器     | 400 RU/秒 (24 元/月)    | 100 RU/秒 (6 元/月)  |輸送量是針對特定容器保留 |

如上表所示，Azure Cosmos DB 中的輸送量下限從 24 元/月價格起算。 如果您是從輸送量下限開始，隨著時間相應增加以支援您的生產環境工作負載，您的成本會平順地增加，增量為 6 元/月。 Azure Cosmos DB 的計價模式具有彈性，當您相應增加或減少時，價格會平順地增減。

## <a name="try-azure-cosmos-db-for-free"></a>免費試用 Azure Cosmos DB 

Azure Cosmos DB 為開發人員提供數個免費選項。 這些選項包含︰

* **Azure 免費帳戶**：Azure 提供了[一個免費套餐](https://azure.microsoft.com/free/)，在前 30 天為您提供 200 美元的 Azure 積分，並在 12 個月內提供數量有限的免費服務。 如需詳細資訊，請參閱 [Azure 免費帳戶](../cost-management-billing/manage/avoid-charges-free-account.md)。 Azure Cosmos DB 屬於 Azure 免費帳戶。 此免費帳戶會特別針對 Azure Cosmos DB，在為期一年的時間內提供 5-GB 的儲存體，以及 400 RU 的已佈建輸送量。 

* **免費試用 Azure 宇宙 DB：Azure**宇宙 DB 通過使用嘗試 Azure Cosmos DB 進行免費帳戶，從而提供有時間限制的體驗。 您可以透過使用快速入門和教學課程，建立 Azure Cosmos DB 帳戶、建立資料庫和集合及執行範例應用程式。 您可以在不註冊 Azure 帳戶或使用信用卡的情況下執行範例應用程式。 [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 提供一個月的 Azure Cosmos DB，可以任意次數更新您的帳戶。

* **Azure 宇宙 DB 模擬器**：Azure Cosmos DB 模擬器提供了一個本地環境，用於類比 Azure Cosmos DB 服務以進行開發。 模擬器免費提供，且具有雲端服務的高逼真度。 您可以使用 Azure Cosmos DB 模擬器在本機開發及測試應用程式，不需建立 Azure 訂用帳戶，也不會產生任何費用。 您可以在進入生產環境之前，在本機使用模擬器來開發您的應用程式。 若滿意模擬器的應用程式功能，可以改為在雲端中使用 Azure Cosmos DB 帳戶，大幅節省成本。 如需有關模擬器的詳細資訊，請參閱[使用 Azure Cosmos DB 進行開發和測試](local-emulator.md)文章以取得詳細資料。

## <a name="pricing-with-reserved-capacity"></a>使用保留容量的定價

Azure Cosmos DB [保留容量](cosmos-db-reserved-capacity.md)有助於您透過預付一年或三年的 Azure Cosmos DB 資源來節省成本。 透過一年或三年的預先承諾量，您可以大幅降低成本，相較於一般的定價可節省 20-65% 的折扣。 Azure Cosmos DB 保留容量可協助您透過預先支付一年或三年期間的佈建輸送量 (RU/秒)，並且取得佈建輸送量的折扣，來降低成本。 

保留容量提供帳單折扣，而且不會影響 Azure Cosmos DB 資源的執行階段狀態。 保留容量持續適用於所有 API，包括 MongoDB、Cassandra、SQL、Gremlin 和 Azure 資料表與全世界所有區域。 您可以在[預付 Azure Cosmos DB 資源與保留容量](cosmos-db-reserved-capacity.md)一文中深入了解保留容量，並且從 [Azure 入口網站](https://portal.azure.com/)購買保留容量。

## <a name="next-steps"></a>後續步驟

您可以在下列文章中深入了解最佳化 Azure Cosmos DB 資源的成本：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](optimize-cost-queries.md)
* 深入了解[最佳化多重區域 Cosmos 帳戶的成本](optimize-cost-regions.md)
* 深入了解 [Azure Cosmos DB 保留容量](cosmos-db-reserved-capacity.md)
* 了解 [Azure Cosmos DB 模擬器](local-emulator.md)
