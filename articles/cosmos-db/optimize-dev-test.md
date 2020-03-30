---
title: 在 Azure Cosmos DB 中最佳化開發與測試
description: 本文說明 Azure Cosmos DB 如何免費提供多個對服務進行開發與測試的選項。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246678"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化開發與測試成本

本文介紹了使用 Azure Cosmos DB 進行免費開發和測試的不同選項，以及優化開發或測試帳戶成本的技術。

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 模擬器 (可下載的本機版)

[Azure Cosmos DB 模擬器](local-emulator.md)是能模擬 Azure Cosmos DB 雲端服務的可下載本機版。 即使沒有網路連線，您也可以撰寫及測試使用 Azure Cosmos DB API 的程式碼，且不會產生任何費用。 Azure Cosmos DB 模擬器能提供可供開發之用，且能夠精確模擬雲端服務的本機環境。 您可以在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶。 當您準備好將應用程式部署到雲端時，只需要更新連接字串來連線到雲端中的 Azure Cosmos DB 端點，而不需要做其他修改。 您也可以使用 Azure DevOps 中的 [Azure Cosmos DB 模擬器建置工作來設定 CI/CD 管線](tutorial-setup-ci-cd.md)以執行測試。 若要開始使用，請參閱 [Azure Cosmos DB 模擬器](local-emulator.md)一文。

## <a name="azure-cosmos-db-free-tier"></a>Azure 宇宙 DB 免費層 
Azure Cosmos DB 免費層便於開始、開發和測試應用程式，甚至免費運行小型生產工作負載。 當在帳戶上啟用免費套餐時，您將在帳戶中免費獲得前 400 RU/s 和 5 GB 的存儲空間。 您還可以創建一個共用輸送量資料庫，該資料庫包含 25 個容器，這些容器在資料庫級別共用 400 RU/s，所有容器都由免費層覆蓋（在免費層帳戶中限制 5 個共用輸送量資料庫）。 免費套餐在帳戶的存留期內無限期持續，並附帶常規 Azure Cosmos DB 帳戶的所有[優點和功能](introduction.md#key-benefits)，包括無限制的存儲和輸送量 （RU/s）、SL、高可用性、所有 Azure 區域中的交鑰匙全域分發等。 每個 Azure 訂閱最多隻能有一個免費層帳戶，並且在創建帳戶時必須加入宣告。 要開始，[請創建一個啟用免費套餐的新帳戶](create-cosmosdb-resources-portal.md)。 請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)，以取得詳細資料。 

## <a name="try-azure-cosmos-db-for-free"></a>免費試用 Azure Cosmos DB

[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)是免費的體驗，允許您在雲中試用 Azure Cosmos DB，而無需註冊 Azure 帳戶或使用信用卡。 嘗試 Azure 宇宙資料庫帳戶在有限的時間內可用，目前為 30 天。 您可以隨時更新它們。 嘗試 Azure Cosmos DB 帳戶可以輕鬆評估 Azure Cosmos DB、生成和測試應用程式或使用快速入門或教程。 您還可以創建演示、執行單元測試，甚至創建多區域帳戶並在其上運行應用，而不會產生任何成本。 在 Try Azure Cosmos DB 帳戶中，可以有一個最多 25 個容器和 20，000 RU/s 輸送量的共用輸送量資料庫，或者一個最多 5000 RU/s 的容器。 若要開始使用，請參閱[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 頁面。

## <a name="azure-free-account"></a>Azure 免費帳戶

Azure Cosmos DB 已包含在 [Azure 免費帳戶](https://azure.microsoft.com/free)中，此帳戶會在特定期間內提供 Azure 點數和免費資源。 此免費帳戶會特別針對 Azure Cosmos DB，在為期一年的時間內提供 5-GB 的儲存體，以及 400 RU 的已佈建輸送量。 此體驗能讓任何開發人員在不產生任何成本的情況下輕鬆測試 Azure Cosmos DB 的功能，或將它與其他 Azure 服務進行整合。 透過 Azure 免費帳戶，您可以取得美金 $200 元的點數，可供您在前 30 天內使用。 即使您開始使用服務也不會向您收費，直到您選擇升級為止。 若要開始使用，請造訪 [Azure 免費帳戶](https://azure.microsoft.com/free)頁面。

## <a name="use-shared-throughput-databases"></a>使用共用輸送量資料庫

在[共用輸送量資料庫中](set-throughput.md#set-throughput-on-a-database)，資料庫中的所有容器共用資料庫的預配輸送量 （RU/s）。 例如，如果預配了 400 RU/s 的資料庫，並且有四個容器，則所有四個容器都將共用 400 RU/s。 在開發或測試環境中，每個容器的訪問頻率可能較低，因此需要低於至少 400 RU/s，將容器放入共用輸送量資料庫中有助於優化成本。 

例如，假設您的開發或測試帳戶有四個容器。 如果創建四個具有專用輸送量（最小 400 RU/s）的容器，則總 RU/s 將為 1600 RU/s。 相反，如果您創建一個共用輸送量資料庫（最小 400 RU/s），並將容器放在那裡，則總 RU/s 將僅為 400 RU/s。 通常，共用輸送量資料庫非常適合不需要保證任何容器輸送量的情況。  瞭解有關[共用輸送量資料庫的資訊。](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>後續步驟

您可以透過下列文章來開始使用模擬器或免費的 Azure Cosmos DB 帳戶：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](optimize-cost-queries.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)

