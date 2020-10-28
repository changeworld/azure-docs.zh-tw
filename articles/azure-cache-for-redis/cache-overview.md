---
title: 什麼是 Azure Redis 快取？
description: 了解 Azure Cache for Redis 以啟用另行快取、內容快取、使用者工作階段快取、作業和訊息佇列，以及分散式交易。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 5a665b2f2aeb41ee55ee41287d2800ebdbf4ec08
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537439"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis 會根據開放原始碼軟體 [Redis](https://redis.io/) 提供記憶體中的資料存放區。 針對在後端資料存放區上重度使用的應用程式，Redis 可改善其效能和可擴縮性。 其可以處理大量的應用程式要求，方法是將經常存取的資料保留在伺服器記憶體中，以供快速地寫入和讀取。 Redis 將重要的低延遲和高輸送量資料儲存解決方案帶入到現代化的應用程式。

Azure Cache for Redis 會以受控服務的形式來提供 Redis。 其提供安全且專用的 Redis 伺服器執行個體，以及完整的 Redis API 相容性。 此服務由 Microsoft 運作並裝載於 Azure 上，可供 Azure 內部或外部的任何應用程式存取。

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

Azure Cache for Redis 支援 Redis 4.x 版和 6.0 版 (作為預覽)。 我們已決定跳過 Redis 5.0，將您帶到最新版本。 先前，Azure Cache for Redis 只會保持單一 Redis 版本。 其會提供更新的主要版本升級，以及至少一個更舊的穩定版本繼續進行。 您可以[選擇哪一個版本](cache-how-to-version.md)最適合您的應用程式。

> [!NOTE]
> Redis 6.0 目前為預覽狀態 - 如果您有興趣，請[與我們聯絡](mailto:azurecache@microsoft.com)。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="service-tiers"></a>服務層
Azure Cache for Redis 可在以下層級使用：

| 層 | 描述 |
|---|---|
| 基本 | 單一節點快取。 這一層支援多個記憶體大小 (250 MB - 53 GB)，非常適用於開發/測試和非關鍵工作負載。 基本層沒有服務等級協定 (SLA)。 |
| 標準 | 複寫的快取是雙節點 (主要/複本) 組態，由 Azure 管理且具高可用性 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)。 |
| Premium | 進階層是可供企業使用的層級。 進階層快取支援更多功能，而且具有較高的輸送量和較低的延遲。 進階層中的快取是部署在更強大的硬體上，因此效能優於基本或標準層。 這項優勢表示，針對大小相同的快取，其輸送量在進階層中會比在標準層中高。 |

### <a name="feature-comparison"></a>功能比較
[Azure Cache for Redis 價格](https://azure.microsoft.com/pricing/details/cache/)會提供每一層的詳細比較。 下表可協助說明階層所支援的一些功能：

| 功能描述 | Premium | 標準 | 基本 |
| ------------------- | :-----: | :------: | :---: |
| [服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis 資料永續性](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis 叢集](cache-how-to-premium-clustering.md) |✔|-|-|
| [透過防火牆規則保護的安全性](cache-configure.md#firewall) |✔|✔|✔|
| 傳輸中加密 |✔|✔|✔|
| [透過 VNet 增強的安全性和隔離](cache-how-to-premium-vnet.md) |✔|-|-|
| [匯入/匯出](cache-how-to-import-export-data.md) |✔|-|-|
| [排程的更新](cache-administration.md#schedule-updates) |✔|✔|✔|
| [異地複寫](cache-how-to-geo-replication.md) |✔|-|-|
| [重新啟動](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>選擇正確的階層
在選擇 Azure Cache for Redis 服務層級時，您應該考慮下列各項。

* **記憶體** ：「基本」層和「標準」層提供 250 MB – 53 GB。 進階層最多可提供 1.2 TB (作為叢集) 或 120 GB (非叢集)。 如需詳細資訊，請參閱 [Azure Redis 快取價格](https://azure.microsoft.com/pricing/details/cache/)。
* **網路效能** ：如果您的工作負載需要高輸送量，與「標準」層或「基本」層相比，「進階」層可提供更大的頻寬。 此外，因為每一層內有裝載快取的基礎 VM，較大型快取還有更大頻寬。 如需詳細資訊，請參閱 [Azure Cache for Redis 效能](cache-planning-faq.md#azure-cache-for-redis-performance)。
* **輸送量** ：「進階」層提供最大的可用輸送量。 如果快取伺服器或用戶端達到頻寬限制，您在用戶端可能會收到逾時。 如需詳細資訊，請參閱下列表格。
* **高可用性** ：Azure Cache for Redis 提供多個 [高可用性](cache-high-availability.md)選項。 根據我們的 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)，其保證標準/進階快取的可用性。 SLA 的範圍僅涵蓋與快取端點的連線。 SLA 未涵蓋資料遺失防護。 建議您使用高階層中的 Redis 資料永續性功能，以增加資料遺失時的復原能力。
* **Redis 資料永續性** ：高階層可讓您將快取資料保存在 Azure 儲存體帳戶中。 在基本/標準快取中，所有資料都只儲存在記憶體中。 基礎結構發生問題，可能會導致資料遺失。 建議您使用高階層中的 Redis 資料永續性功能，以增加資料遺失時的復原能力。 「Azure Redis 快取」在 Redis 持續性中提供 RDB 和 AOF (預覽) 選項。 如需詳細資訊，請參閱[如何設定進階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md)。
* **Redis 叢集** ：若要建立大於 120 GB 的快取，或要跨多個 Redis 節點將資料分區，您可以使用「進階」層所提供的 Redis 叢集功能。 每個節點均包含一個主要/複本快取組以提供高可用性。 如需詳細資訊，請參閱 [如何設定進階 Azure Redis 快取的叢集功能](cache-how-to-premium-clustering.md)。
* **增強的安全性與網路隔離** ：「Azure 虛擬網路」(VNET) 部署除了為「Azure Redis 快取」提供子網路、存取控制原則及其他可進一步限制存取的功能之外，也提供增強的安全性與隔離環境。 如需詳細資訊，請參閱[如何設定進階 Azure Cache for Redis 的虛擬網路支援](cache-how-to-premium-vnet.md)。
* **設定 Redis** ：不論是在「標準」層還是「進階」層中，您都可以設定 Redis 以接收 Keyspace 通知。
* **用戶端連線數上限** ：「進階」層提供可連線至 Redis 的最大用戶端數目，針對較大型的快取可提供較高的連線數。 叢集化不會增加叢集快取的可用連線數目。 如需詳細資訊，請參閱 [Azure Cache for Redis 價格](https://azure.microsoft.com/pricing/details/cache/)。
* **Redis 伺服器的專用核心** ：在「進階」層中，所有快取大小都有 Redis 專用核心。 在基本/標準層中，C1 以上的大小有 Redis 伺服器專用核心。
* **單一執行緒處理** ：根據設計，Redis 只會使用一個執行緒來處理命令。 Azure Cache for Redis 也會利用額外的核心來處理 I/O。 擁有更多核心會改善輸送量效能，即使其可能不會產生線性擴增。 此外，較大的 VM 大小一般會比小一點的大小有更高的頻寬限制。 這可協助您避免網路飽和，這會導致您的應用程式發生逾時。
* **效能改進** ：「進階」層中的快取是部署在處理器較快的硬體上，因此效能優於「基本」層或「標準」層。 高階層快取的輸送量較高，延遲性較低。 如需詳細資訊，請參閱 [Azure Cache for Redis 效能](cache-planning-faq.md#azure-cache-for-redis-performance)

快取建立好之後，您可以將快取調整至更高的層級。 但不支援向下調整至較低層級。 如需調整階層的逐步指示，請參閱[如何調整 Azure Cache for Redis](cache-how-to-scale.md) 和[如何將調整作業自動化](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

## <a name="next-steps"></a>後續步驟
* [ASP.NET Web 應用程式快速入門](cache-web-app-howto.md)：建立可使用 Azure Cache for Redis 的簡單 ASP.NET Web 應用程式。
* [.NET 快速入門](cache-dotnet-how-to-use-azure-redis-cache.md)：建立可使用 Azure Cache for Redis 的 .NET 應用程式。
* [.NET Core 快速入門](cache-dotnet-core-quickstart.md)：建立可使用 Azure Cache for Redis 的 .NET Core 應用程式。
* [Node.js 快速入門](cache-nodejs-get-started.md)：建立可使用 Azure Cache for Redis 的簡單 Node.js 應用程式。
* [Java 快速入門](cache-java-get-started.md)：建立可使用 Azure Cache for Redis 的簡單 Java 應用程式。
* [Python 快速入門](cache-python-get-started.md)：建立可使用 Azure Cache for Redis 的 Python 應用程式。