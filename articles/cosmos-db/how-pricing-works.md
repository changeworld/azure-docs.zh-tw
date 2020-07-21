---
title: Azure Cosmos DB 的計價模式
description: 本文說明 Azure Cosmos DB 的計價模式，以及它如何簡化您的成本管理和成本規劃。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: d36b4fd433af716ebd97d88d05922d94bd74c309
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523531"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB 的計價模式

Azure Cosmos DB 的計價模式可簡化成本管理和規劃。 使用 Azure Cosmos DB，您需要支付所佈建的輸送量及所取用的儲存體。

* 布**建的輸送量**：布建的[輸送量](how-to-choose-offer.md)（也稱為保留的輸送量）可確保任何規模的高效能。 您指定需要的輸送量 (RU/秒)，Azure Cosmos DB 會致力於提供保證已設定輸送量所需的資源。 您須按小時支付指定小時的最大佈建輸送量費用。 您可以手動布建輸送量或使用[自動](provision-throughput-autoscale.md)調整。

   > [!NOTE]
   > 由於布建的輸送量模型會將資源專用到您的容器或資料庫，因此即使您未執行任何工作負載，也會向您收取布建輸送量的費用。

* 已取用的**儲存體**：針對資料使用的總儲存量（gb），以及指定小時內的索引，以固定費率計費。

佈建的輸送量指定為每秒[要求單位](request-units.md) (RU/秒)，可讓您從容器或資料庫讀取資料或將資料寫入其中。 您可以[在資料庫或容器上佈建輸送量](set-throughput.md)。 根據工作負載需求，您可以隨時相應增加/相應減少輸送量。 Azure Cosmos DB 定價具有彈性，且與您在資料庫或容器上設定的輸送量成正比。 輸送量和儲存體值下限與縮放增量為所有階層的客戶，提供從小型到大型容器的完整價格與彈性範圍。 每個資料庫或容器是按小時以 100 RU/秒為單位計費，最少 400 RU/秒，取用的儲存體以 GB 為單位。 與佈建的輸送量不同，儲存體是按取用量來計費。 也就是說，您不需要事先保留任何儲存體。 您只須支付所取用的儲存體費用。

如需詳細資訊，請參閱 [Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)和[了解您的 Azure Cosmos DB 計費](understand-your-bill.md)。

Azure Cosmos DB 的計價模式在所有 API 之間都是一致的。 若要深入了解，請參閱 [Azure Cosmos DB 計價模式如何為客戶提供符合成本效益的解決方案](total-cost-ownership.md)。 資料庫或容器需要最低輸送量，以確保 Sla，而且您可以增加或減少每個 100 RU/秒的布建輸送量。

如果您將 Azure Cosmos DB 帳戶部署到美國的非政府區域，目前資料庫和容器型輸送量的最小價格大約是 $ 24/月。 定價會依您使用的區域而有所不同，請參閱[Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)以取得最新的定價資訊。 如果您的工作負載使用多個容器，可以藉由使用資料庫層級輸送量，針對成本進行最佳化，因為資料庫層級輸送量可讓您在資料庫中具有任意數量的容器，在容器之間共用輸送量。 下表摘要說明不同實體的佈建的輸送量和成本：

|**實體**  | **最小輸送量** |**調整增量** |**佈建範圍** |
|---------|---------|---------|-------|
|資料庫    | 400 RU/秒    | 100 RU/秒   |輸送量是針對資料庫保留，可供資料庫內的容器共用 |
|容器     | 400 RU/秒   | 100 RU/秒  |輸送量是針對特定容器保留 |

如上表所示，Azure Cosmos DB 的最小輸送量以大約 $ 24/月的價格開始。 如果您從最小輸送量開始，並隨著時間相應增加以支援您的生產工作負載，您的成本將會以大約 $ 6/月的增量逐漸順暢地增加。 定價會依您使用的區域而有所不同，請參閱[Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)以取得最新的定價資訊。 Azure Cosmos DB 的計價模式具有彈性，當您相應增加或減少時，價格會平順地增減。

## <a name="try-azure-cosmos-db-for-free"></a>免費試用 Azure Cosmos DB

Azure Cosmos DB 為開發人員提供許多免費的選項。 這些選項包含︰

* **Azure Cosmos DB 免費層**： Azure Cosmos DB 免費層可讓您輕鬆地開始使用、開發和測試您的應用程式，甚至免費執行小型生產工作負載。 在帳戶上啟用免費層時，您會在帳戶的存留期內，免費取得帳戶中的前 400 RU/秒和 5 GB 的儲存體。 您的每個 Azure 訂用帳戶最多可以有一個免費層帳戶，而且在建立帳戶時必須加入宣告。 若要開始，請[在已啟用免費層的 Azure 入口網站中建立新帳戶](create-cosmosdb-resources-portal.md)，或使用[ARM 範本](manage-sql-with-resource-manager.md#free-tier)。

* **Azure 免費帳戶**： azure 提供[免費層](https://azure.microsoft.com/free/)，$200 可提供您前30天的 Azure 點數，以及12個月有限的免費服務數量。 如需詳細資訊，請參閱 [Azure 免費帳戶](../cost-management-billing/manage/avoid-charges-free-account.md)。 Azure Cosmos DB 屬於 Azure 免費帳戶。 特別針對 Azure Cosmos DB，此免費帳戶提供了 5 GB 的儲存體，以及一年所布建的輸送量 400 RU/秒。

* **免費試用 Azure Cosmos DB**： Azure Cosmos DB 使用免費帳戶的試用 Azure Cosmos DB，提供限時的體驗。 您可以透過使用快速入門和教學課程，建立 Azure Cosmos DB 帳戶、建立資料庫和集合及執行範例應用程式。 您可以在不註冊 Azure 帳戶或使用信用卡的情況下執行範例應用程式。 [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 提供一個月的 Azure Cosmos DB，可以任意次數更新您的帳戶。

* **Azure Cosmos DB 模擬器**： Azure Cosmos DB 模擬器會提供一個模擬 Azure Cosmos DB 服務的本機環境，以供開發之用。 模擬器免費提供，且具有雲端服務的高逼真度。 您可以使用 Azure Cosmos DB 模擬器在本機開發及測試應用程式，不需建立 Azure 訂用帳戶，也不會產生任何費用。 您可以在進入生產環境之前，在本機使用模擬器來開發您的應用程式。 若滿意模擬器的應用程式功能，可以改為在雲端中使用 Azure Cosmos DB 帳戶，大幅節省成本。 如需有關模擬器的詳細資訊，請參閱[使用 Azure Cosmos DB 進行開發和測試](local-emulator.md)文章以取得詳細資料。

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
