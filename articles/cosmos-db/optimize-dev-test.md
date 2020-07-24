---
title: 在 Azure Cosmos DB 中最佳化開發與測試
description: 本文說明 Azure Cosmos DB 如何免費提供多個對服務進行開發與測試的選項。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ea5d975b73afcf03ad97bafd1c6f68f2b55263c2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084716"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化開發與測試成本

本文說明使用 Azure Cosmos DB 進行開發和測試的不同選項，以免費取得成本，以及將開發或測試帳戶中的成本優化的技術。

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 模擬器 (可下載的本機版)

[Azure Cosmos DB 模擬器](local-emulator.md)是能模擬 Azure Cosmos DB 雲端服務的可下載本機版。 即使沒有網路連線，您也可以撰寫及測試使用 Azure Cosmos DB API 的程式碼，且不會產生任何費用。 Azure Cosmos DB 模擬器能提供可供開發之用，且能夠精確模擬雲端服務的本機環境。 您可以在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶。 當您準備好將應用程式部署到雲端時，只需要更新連接字串來連線到雲端中的 Azure Cosmos DB 端點，而不需要做其他修改。 您也可以使用 Azure DevOps 中的 [Azure Cosmos DB 模擬器建置工作來設定 CI/CD 管線](tutorial-setup-ci-cd.md)以執行測試。 若要開始使用，請參閱 [Azure Cosmos DB 模擬器](local-emulator.md)一文。

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB 免費層

Azure Cosmos DB 免費層可讓您輕鬆地開始使用、開發和測試您的應用程式，甚至免費執行小型生產工作負載。 在帳戶上啟用免費層時，您會在免費帳戶中取得前 400 RU/秒和 5 GB 的儲存體。 您也可以在資料庫層級建立一個共用輸送量資料庫，其中包含25個400共用/s 的容器，全都由免費層所涵蓋（在免費層帳戶中限制5個共用輸送量資料庫）。 使用免費層時，如果您布建的共用資料庫的最小輸送量為 400 RU/秒，則該資料庫內的所有容器都可以共用輸送量。 任何具有共用輸送量或具有專用輸送量之容器的新資料庫，都會以一般價格計費。

免費層會無限期地持續在帳戶的存留期間內運作，並隨附一般 Azure Cosmos DB 帳戶的所有[優點和功能](introduction.md#key-benefits)，包括無限制的儲存體和輸送量（RU/秒）、sla、高可用性、所有 Azure 區域中的全域散發功能等等。 您的每個 Azure 訂用帳戶最多可以有一個免費層帳戶，而且在建立帳戶時必須加入宣告。 若要開始，請[在已啟用免費層的 Azure 入口網站中建立新帳戶](create-cosmosdb-resources-portal.md)，或使用[ARM 範本](manage-sql-with-resource-manager.md#free-tier)。 請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)，以取得詳細資料。

## <a name="try-azure-cosmos-db-for-free"></a>免費試用 Azure Cosmos DB

免費[試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)是免費的免費體驗，可讓您在雲端中進行 Azure Cosmos DB 試驗，而不需要註冊 Azure 帳戶或使用您的信用卡。 試用 Azure Cosmos DB 帳戶在有限的時間內可供使用，目前為30天。 您可以隨時更新它們。 試用 Azure Cosmos DB 帳戶可讓您輕鬆評估 Azure Cosmos DB、建立和測試應用程式，或使用快速入門或教學課程。 您也可以建立示範、執行單元測試，或甚至建立多區域帳戶並在其上執行應用程式，而不會產生任何費用。 在試用 Azure Cosmos DB 帳戶中，您可以有一個共用輸送量資料庫，最多可有25個容器和 20000 RU/秒的輸送量，或一個最多有 5000 RU/秒的容器。 若要開始使用，請參閱[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 頁面。

## <a name="azure-free-account"></a>Azure 免費帳戶

Azure Cosmos DB 已包含在 [Azure 免費帳戶](https://azure.microsoft.com/free)中，此帳戶會在特定期間內提供 Azure 點數和免費資源。 此免費帳戶會特別針對 Azure Cosmos DB，在為期一年的時間內提供 5-GB 的儲存體，以及 400 RU 的已佈建輸送量。 此體驗能讓任何開發人員在不產生任何成本的情況下輕鬆測試 Azure Cosmos DB 的功能，或將它與其他 Azure 服務進行整合。 透過 Azure 免費帳戶，您可以取得美金 $200 元的點數，可供您在前 30 天內使用。 即使您開始使用服務也不會向您收費，直到您選擇升級為止。 若要開始使用，請造訪 [Azure 免費帳戶](https://azure.microsoft.com/free)頁面。

## <a name="use-shared-throughput-databases"></a>使用共用輸送量資料庫

在[共用輸送量資料庫](set-throughput.md#set-throughput-on-a-database)中，資料庫內的所有容器都會共用資料庫的布建輸送量（RU/秒）。 例如，如果您布建了 400 RU/秒的資料庫，而且有四個容器，則所有四個容器都會共用 400 RU/秒。 在開發或測試環境中，每個容器的存取頻率較低，因此需要低於最低 400 RU/秒，將容器放在共用輸送量資料庫中可協助將成本優化。

例如，假設您的開發或測試帳戶有四個容器。 如果您建立具有專用輸送量的四個容器（最低為 400 RU/秒），您的總 RU/秒將會是 1600 RU/秒。 相反地，如果您建立共用輸送量資料庫（最低為 400 RU/秒），並將您的容器放在該處，則 RU/秒的總計將會是 400 RU/秒。 一般而言，共用輸送量資料庫很適合您在任何個別容器上都不需要保證輸送量的情況。  深入瞭解[共用輸送量資料庫。](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>後續步驟

您可以透過下列文章來開始使用模擬器或免費的 Azure Cosmos DB 帳戶：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](optimize-cost-queries.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)
