---
title: Azure Cosmos DB 的計價模式
description: 本文說明 Azure Cosmos DB 的計價模式，以及它如何簡化您的成本管理和成本規劃。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: bb47dde8813f31f4a6acceb696a8b7c7eb0095be
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281531"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB 的計價模式

Azure Cosmos DB 的計價模式可簡化成本管理和規劃。 使用 Azure Cosmos DB 時，您需支付針對資料庫所執行的作業，以及資料所耗用的儲存體費用。

- **資料庫作業**：您對資料庫作業收費的方式取決於您所使用的 Azure Cosmos 帳戶類型。

  - 布**建的輸送量**：布建的[輸送量](set-throughput.md) (也稱為保留輸送量，) 提供任何規模的高效能。 您可以指定每秒 [要求單位](request-units.md) (RU/秒) 所需的輸送量，並 Azure Cosmos DB 專用提供所設定輸送量所需的資源。 您可以[在資料庫或容器上佈建輸送量](set-throughput.md)。 根據您的工作負載需求，您可以隨時相應增加/減少輸送量，或使用 [自動](provision-throughput-autoscale.md) 調整 (雖然資料庫或容器需要最小輸送量，才能保證 sla) 。 您須按小時支付指定小時的最大佈建輸送量費用。

   > [!NOTE]
   > 因為布建的輸送量模型會將資源專用至您的容器或資料庫，所以即使您未執行任何工作負載，也會向您收取已布建的輸送量費用。

  - **無伺服器**：在 [無伺服器](serverless.md) 模式中，在 Azure Cosmos 帳戶中建立資源時，您不需要布建任何輸送量。 在您的計費期間結束時，您會收到資料庫作業所耗用的要求單位數量的費用。

- **儲存體**：依您的資料和索引) 在指定的一小時內耗用的總 (儲存體量，以固定費率計費。 儲存體會以耗用量計費，因此您不需要事先保留任何儲存體。 您只須支付所取用的儲存體費用。

Azure Cosmos DB 的計價模式在所有 API 之間都是一致的。 如需詳細資訊，請參閱 [Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)， [瞭解您的 Azure Cosmos DB 帳單](understand-your-bill.md) ，以及 [Azure Cosmos DB 的定價模式對客戶而言是符合成本效益的方式](total-cost-ownership.md)。

如果您將 Azure Cosmos DB 帳戶部署到美國的非政府區域，則在布建的輸送量模式下，資料庫和容器型輸送量都有最低價格。 無伺服器模式沒有最低價格。 定價會依您所使用的區域而有所不同，請參閱 [Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/) 以取得最新的定價資訊。

## <a name="try-azure-cosmos-db-for-free"></a>免費試用 Azure Cosmos DB

Azure Cosmos DB 為開發人員提供許多免費的選項。 這些選項包含︰

* **Azure Cosmos DB 免費層**： Azure Cosmos DB 免費層可讓您輕鬆地開始使用、開發及測試應用程式，或甚至免費執行小型生產工作負載。 在帳戶上啟用免費層時，您會在帳戶的存留期內，免費取得前 400 RU/秒和 5 GB 的儲存體。 每個 Azure 訂用帳戶最多可以有一個免費層帳戶，而且必須在建立帳戶時加入宣告。 若要開始使用，請 [在已啟用免費層的 Azure 入口網站中建立新的帳戶](create-cosmosdb-resources-portal.md) ，或使用 [ARM 範本](manage-sql-with-resource-manager.md#free-tier)。

* **Azure 免費帳戶**： azure 提供 [免費層](https://azure.microsoft.com/free/) ，可讓您在前30天內享有 $200 的 Azure 點數，以及12個月的免費服務數量有限。 如需詳細資訊，請參閱 [Azure 免費帳戶](../cost-management-billing/manage/avoid-charges-free-account.md)。 Azure Cosmos DB 屬於 Azure 免費帳戶。 具體而言，此免費帳戶為 Azure Cosmos DB 提供 5 GB 的儲存空間和 400 RU/秒的布建輸送量供整個年度之用。

* **免費試用 Azure Cosmos DB**： Azure Cosmos DB 使用免費帳戶的試用 Azure Cosmos DB 提供有時間限制的體驗。 您可以透過使用快速入門和教學課程，建立 Azure Cosmos DB 帳戶、建立資料庫和集合及執行範例應用程式。 您可以在不註冊 Azure 帳戶或使用信用卡的情況下執行範例應用程式。 [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 提供一個月的 Azure Cosmos DB，可以任意次數更新您的帳戶。

* **Azure Cosmos DB 模擬器**： Azure Cosmos DB 模擬器會提供一個模擬 Azure Cosmos DB 服務的本機環境，以供開發之用。 模擬器免費提供，且具有雲端服務的高逼真度。 您可以使用 Azure Cosmos DB 模擬器在本機開發及測試應用程式，不需建立 Azure 訂用帳戶，也不會產生任何費用。 您可以在進入生產環境之前，在本機使用模擬器來開發您的應用程式。 若滿意模擬器的應用程式功能，可以改為在雲端中使用 Azure Cosmos DB 帳戶，大幅節省成本。 如需有關模擬器的詳細資訊，請參閱[使用 Azure Cosmos DB 進行開發和測試](local-emulator.md)文章以取得詳細資料。

## <a name="pricing-with-reserved-capacity"></a>使用保留容量的定價

Azure Cosmos DB [保留容量](cosmos-db-reserved-capacity.md) 可協助您透過預先支付一年或三年的 Azure Cosmos DB 資源，來使用布建的輸送量模式來節省成本。 透過一年或三年的預先承諾量，您可以大幅降低成本，相較於一般的定價可節省 20-65% 的折扣。 Azure Cosmos DB 保留容量可協助您透過預先支付一年或三年期間的佈建輸送量 (RU/秒)，並且取得佈建輸送量的折扣，來降低成本。 

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
