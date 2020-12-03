---
title: 什麼是 Azure Redis 快取？
description: 了解 Azure Cache for Redis 以啟用另行快取、內容快取、使用者工作階段快取、作業和訊息佇列，以及分散式交易。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 225df0dc53a0386bb53576970a1b6330351f4545
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184122"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis 會根據 [Redis](https://redis.io/) 軟體提供記憶體中的資料存放區。 針對在後端資料存放區上重度使用的應用程式，Redis 可改善其效能和可擴縮性。 其可以處理大量的應用程式要求，方法是將經常存取的資料保留在伺服器記憶體中，以供快速地寫入和讀取。 Redis 將重要的低延遲和高輸送量資料儲存解決方案帶入到現代化的應用程式。

Azure Cache for Redis 提供來自 Redis Labs 的 Redis 開放原始碼和商業產品，作為受控服務。 其提供安全且專用的 Redis 伺服器執行個體，以及完整的 Redis API 相容性。 此服務由 Microsoft 運作並裝載於 Azure 上，可供 Azure 內部或外部的任何應用程式存取。

Azure Cache for Redis 可作為分散式資料或內容快取、工作階段存放區和訊息代理程式等等。 其可以部署為獨立服務，也可以和其他 Azure 資料庫服務一起部署，例如 Azure SQL 或 Cosmos DB。

## <a name="key-scenarios"></a>主要案例
Azure Cache for Redis 藉由支援常見的應用程式架構模式來改善應用程式效能。 最常見的一些模式包括：

| 模式      | 描述                                        |
| ------------ | -------------------------------------------------- |
| [資料快取](cache-web-app-cache-aside-leaderboard.md) | 資料庫通常太大，無法直接載入快取中。 常見的方法是使用[另行快取](/azure/architecture/patterns/cache-aside)模式，只在需要時，將資料載入至快取。 系統變更資料時，也會同時更新快取，並散發到其他用戶端。 此外，系統可以在資料上設定到期日，或使用收回原則，觸發資料更新進入快取。|
| [內容快取](cache-aspnet-output-cache-provider.md) | 許多網頁都是從使用靜態內容 (例如標題、頁尾、橫幅) 的範本所產生。 這些靜態項目不應該經常變更。 相較於後端資料存放區，使用記憶體內部快取可讓您快速存取靜態內容。 此模式可減少處理時間和伺服器負載，讓網頁伺服器更具回應能力， 並可以讓您減少處理負載所需的伺服器數目。 Azure Cache for Redis 會提供 Redis 輸出快取提供者，以使用 ASP.NET 來支援此模式。|
| [工作階段存放區](cache-aspnet-session-state-provider.md) | 此模式通常會與購物車和其他使用者記錄資料這類資訊搭配使用，而 Web 應用程式則會讓使用者 Cookie 與這些資訊產生關聯。 在 cookie 中儲存太多資料可能會對效能產生負面影響，因為 cookie 的大小會增加，且會與每個要求一起傳遞和驗證。 一般解決方案是使用 Cookie 作為索引鍵，來查詢資料庫中的資料。 使用 Azure Cache for Redis 之類的記憶體中快取來將資訊關聯至使用者，速度會比與完整的關聯式資料庫互動快很多。 |
| 作業與訊息佇列 | 當與要求關聯的工作需要時間執行時，應用程式通常會將工作新增到佇列。 較長的執行作業會排入佇列，而且通常是由另一部伺服器依序處理。  此延後工作的方法稱為工作佇列。 Azure Cache for Redis 提供分散式佇列，以在您的應用程式中啟用此模式。|
| 分散式交易 | 應用程式有時需要一系列針對後端資料存放區的命令，以執行單一不可部分完成的作業。 所有命令都必須都成功，或所有命令必須回復為初始狀態。 Azure Cache for Redis 支援以單次[交易](https://redis.io/topics/transactions)執行命令批次。 |

## <a name="redis-versions"></a>Redis 版本

Azure Cache for Redis 支援 OSS Redis 4.x 版，和作為預覽的 6.0 版。 我們已決定跳過 Redis 5.0，將您帶到最新版本。 先前，Azure Cache for Redis 只會保持單一 Redis 版本。 其會提供更新的主要版本升級，以及至少一個更舊的穩定版本繼續進行。 您可以[選擇哪一個版本](cache-how-to-version.md)最適合您的應用程式。


## <a name="service-tiers"></a>服務層
Azure Cache for Redis 可在以下層級使用：

| 層 | 描述 |
|---|---|
| 基本 | 在單一 VM 上執行的 OSS Redis 快取。 這一層沒有服務等級協定 (SLA)，適用於開發/測試和非關鍵工作負載。 |
| 標準 | 在複寫組態的兩個 VM 上執行的 OSS Redis 快取。 |
| Premium | 高效能的 OSS Redis 快取。 這一層提供較高的輸送量、較低的延遲、更佳的可用性，以及更多功能。 相較於基本或標準快取，進階快取會部署在功能更強大的 VM 上。 |
| Enterprise | Redis Labs 的 Redis Enterprise 軟體提供高效能快取。 這一層支援 Redis 模組，包括 RediSearch、RedisBloom 和 RedisTimeSeries。 此外也提供比進階層級更高的可用性。 |
| Enterprise Flash | 符合成本效益的大型快取，由 Redis Labs 的 Redis Enterprise 軟體提供技術支援。 這一層會將 Redis 資料儲存體擴充至 VM 上的非動態記憶體，其價格會比 DRAM 低。 Enterprise Flash 可降低每 GB 記憶體的整體成本。 |

### <a name="feature-comparison"></a>功能比較
[Azure Cache for Redis 價格](https://azure.microsoft.com/pricing/details/cache/)會提供每一層的詳細比較。 下表可協助說明階層所支援的一些功能：

| 功能描述 | 基本 | 標準 | Premium | Enterprise | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| 資料加密 |✔|✔|✔|✔|✔|
| [網路隔離](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [調整大小](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [區域備援](cache-how-to-zone-redundancy.md) |-|-|✔|✔|✔|
| [異地複寫](cache-how-to-geo-replication.md) |-|-|✔|-|-|
| [資料持續性](cache-how-to-premium-persistence.md) |-|-|✔|-|-|
| [OSS 叢集](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [單元](https://redis.io/modules) |-|-|-|✔|-|
| [匯入/匯出](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [排程的更新](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>選擇正確的階層
在選擇 Azure Cache for Redis 服務層級時，您應該考慮下列各項：

* **記憶體**：基本和標準層提供 250 MB - 53 GB；進階層則提供 6 GB - 1.2 TB；Enterprise 層則為 12 GB - 14 TB。  若要建立 120 GB 以上的進階層快取，您可以使用 Redis OSS 叢集。 如需詳細資訊，請參閱 [Azure Redis 快取價格](https://azure.microsoft.com/pricing/details/cache/)。 如需詳細資訊，請參閱 [如何設定進階 Azure Redis 快取的叢集功能](cache-how-to-premium-clustering.md)。
* **網路效能**：如果您的工作負載需要高輸送量，與「基本」層或「標準」層相比，「進階」層或 Enterprise 層可提供更大的頻寬。 此外，因為每一層內有裝載快取的基礎 VM，較大型快取還有更大頻寬。 如需詳細資訊，請參閱 [Azure Cache for Redis 效能](cache-planning-faq.md#azure-cache-for-redis-performance)。
* **輸送量**：「進階」層提供最大的可用輸送量。 如果快取伺服器或用戶端達到頻寬限制，您在用戶端可能會收到逾時。 如需詳細資訊，請參閱下列表格。
* **高可用性**：Azure Cache for Redis 提供多個 [高可用性](cache-high-availability.md)選項。 我們的 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) 保證標準、進階或 Enterprise 快取的可用性。 SLA 的範圍僅涵蓋與快取端點的連線。 SLA 未涵蓋資料遺失防護。 建議您使用高階層中的 Redis 資料永續性功能，以增加資料遺失時的復原能力。
* **資料持續性**：高階層可讓您將快取資料保存在 Azure 儲存體帳戶中。 在其他層中，資料只會儲存在記憶體中。 基礎結構發生問題，可能會導致資料遺失。 建議您使用高階層中的 Redis 資料永續性功能，以增加資料遺失時的復原能力。 「Azure Redis 快取」在 Redis 持續性中提供 RDB 和 AOF (預覽) 選項。 如需詳細資訊，請參閱[如何設定進階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md)。
* **網路隔離**：Azure Private Link 和虛擬網路 (VNET) 部署可為您的 Azure Cache for Redis 提供增強的安全性和流量隔離。 VNET 可讓您透過網路存取控制原則進一步限制存取。 如需詳細資訊，請參閱[具備 Azure Private Link 的 Azure Cache for Redis](cache-private-link.md) 以及[如何設定進階 Azure Cache for Redis 的虛擬網路支援](cache-how-to-premium-vnet.md)。
* **用戶端連線數上限**：「進階」層提供可連線至 Redis 的最大用戶端數目，針對較大型的快取可提供較高的連線數。 叢集化不會增加叢集快取的可用連線數目。 如需詳細資訊，請參閱 [Azure Cache for Redis 價格](https://azure.microsoft.com/pricing/details/cache/)。
* **Redis 伺服器的專用核心**：除了 C0 的所有快取都會執行專用 VM 核心。
* **單一執行緒處理**：根據設計，Redis 只會使用一個執行緒來處理命令。 Azure Cache for Redis 也會利用額外的核心來處理 I/O。 擁有更多核心會改善輸送量效能，即使其可能不會產生線性擴增。 此外，較大的 VM 大小一般會比小一點的大小有更高的頻寬限制。 這可協助您避免網路飽和，這會導致您的應用程式發生逾時。
* **效能改進**：「進階」層和 Enterprise 層中的快取是部署在處理器較快的硬體上，因此效能優於「基本」層或「標準」層。 高階層快取的輸送量較高，延遲性較低。 如需詳細資訊，請參閱 [Azure Cache for Redis 效能](cache-planning-faq.md#azure-cache-for-redis-performance)。

建立後，您可以將快取從基本層擴充至進階層。 但不支援向下調整至較低層級。 如需調整階層的逐步指示，請參閱[如何調整 Azure Cache for Redis](cache-how-to-scale.md) 和[如何將調整作業自動化](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

### <a name="enterprise-tier-requirements"></a>Enterprise 層需求

Enterprise 層依賴 Redis Enterprise，這是來自 Redis Labs 的 Redis 商業版本。 客戶會透過 Azure Marketplace 供應項目取得並支付此軟體的授權。 Azure Cache for Redis 可協助取得授權，因此您不需要個別執行此動作。 您必須具備以下先決條件，才能在 Azure Marketplace 中購買：
* 您的 Azure 訂用帳戶具有有效的付款條件。 不支援 Azure 點數或免費 MSDN 訂用帳戶。
* 您是訂用帳戶的擁有者或參與者。
* 您的組織允許 [Azure Marketplace 購買](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)。
* 如果您使用私人 Marketplace，其必須包含 Redis Labs 企業版供應項目。

## <a name="next-steps"></a>後續步驟
* [建立 Azure Cache for Redis 執行個體](quickstart-create-redis.md)
* [建立企業層快取](quickstart-create-redis-enterprise.md)
* [在 ASP.NET Web 應用程式中使用 Azure Cache for Redis](cache-web-app-howto.md)
* [在 .NET Core 中使用 Azure Cache for Redis](cache-dotnet-core-quickstart.md)
* [在 .NET Framework 中使用 Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md)
* [在 Node.js 中使用 Azure Cache for Redis](cache-nodejs-get-started.md)
* [在 Java 中使用 Azure Cache for Redis](cache-java-get-started.md)
* [在 Python 中使用 Azure Cache for Redis](cache-python-get-started.md)