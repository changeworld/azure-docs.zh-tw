---
title: 什麼是 Azure Redis 快取？
description: 了解 Azure Cache for Redis 以啟用另行快取、內容快取、使用者工作階段快取、作業和訊息佇列，以及分散式交易。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 09a7a28716e437bab71c7386bd332712a4d192dd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196364"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Azure Cache for Redis 會根據開放原始碼軟體 [Redis](https://redis.io/) 提供記憶體中的資料存放區。 用來作為快取時，Redis 可為高度依賴後端資料存放區的系統改善效能及延展性。 藉由將經常存取的資料複製到靠近應用程式的快速儲存體中，進而改善效能。 使用 Azure Cache for Redis，此快速儲存體會位於記憶體中，而不會由資料庫從磁碟載入。

Azure Cache for Redis 可作為分散式資料快取、工作階段存放區和訊息代理程式。 利用 Redis 引擎的低延遲與高輸送量效能可提升應用程式效能。

Azure Cache for Redis 可讓您存取安全、專用的 Redis 快取。 Microsoft 會加以管理並將其裝載於 Azure 上，您可從 Azure 內部或外部的任何應用程式加以存取。

## <a name="using-azure-cache-for-redis"></a>使用 Azure Cache for Redis

Azure Cache for Redis 藉由支援常見的應用程式架構模式來改善應用程式效能。 最常見的一些模式包括：

| 模式      | 描述                                        |
| ------------ | -------------------------------------------------- |
| [另行快取](cache-web-app-cache-aside-leaderboard.md) | 資料庫通常太大，無法直接載入快取中。 常見的方法是使用[另行快取](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)模式，只在需要時，將資料載入至快取。 系統變更資料時，也會同時更新快取，並散發到其他用戶端。 此外，系統可以在資料上設定到期日，或使用收回原則，觸發資料更新進入快取。|
| [內容快取](cache-aspnet-output-cache-provider.md) | 許多網頁都是從使用靜態內容 (例如標題、頁尾、橫幅) 的範本所產生。 這些靜態項目不應該經常變更。 相較於後端資料存放區，使用記憶體內部快取可讓您快速存取靜態內容。 此模式可減少處理時間和伺服器負載，讓網頁伺服器更具回應能力， 並可以讓您減少處理負載所需的伺服器數目。 Azure Cache for Redis 會提供 Redis 輸出快取提供者，以使用 ASP.NET 來支援此模式。|
| [使用者工作階段快取](cache-aspnet-session-state-provider.md) | 此模式通常會與購物車和其他使用者記錄資料這類資訊搭配使用，而 Web 應用程式則會讓使用者 Cookie 與這些資訊產生關聯。 在 cookie 中儲存太多資料可能會對效能產生負面影響，因為 cookie 的大小會增加，且會與每個要求一起傳遞和驗證。 一般解決方案是使用 Cookie 作為索引鍵，來查詢資料庫中的資料。 使用 Azure Cache for Redis 之類的記憶體中快取來將資訊關聯至使用者，速度會比與完整的關聯式資料庫互動快很多。 |
| 作業與訊息佇列 | 當與要求關聯的工作需要時間執行時，應用程式通常會將工作新增到佇列。 較長的執行作業會排入佇列，而且通常是由另一部伺服器依序處理。  此延後工作的方法稱為工作佇列。 Azure Cache for Redis 提供分散式佇列，以在您的應用程式中啟用此模式。|
| 分散式交易 | 應用程式有時需要一系列針對後端資料存放區的命令，以執行單一不可部分完成的作業。 所有命令都必須都成功，或所有命令必須回復為初始狀態。 Azure Cache for Redis 支援以單次[交易](https://redis.io/topics/transactions)執行命令批次。 |

## <a name="azure-cache-for-redis-offerings"></a>Azure Cache for Redis 供應項目

Azure Cache for Redis 可在以下層級使用：

| 層 | 描述 |
|---|---|
基本 | 單一節點快取。 這一層支援多個記憶體大小 (250 MB - 53 GB)，非常適用於開發/測試和非關鍵工作負載。 基本層沒有服務等級協定 (SLA) |
| 標準 | 複寫的快取是雙節點 (主要/次要) 組態，由 Azure 管理且具高可用性 SLA (99.9%) |
| Premium | 進階層是可供企業使用的層級。 進階層快取支援更多功能，而且具有較高的輸送量和較低的延遲。 進階層中的快取是部署在更強大的硬體上，因此效能優於基本或標準層。 這項優勢表示，針對大小相同的快取，其輸送量在進階層中會比在標準層中高。 |

> [!TIP]
> 如需進階快取的大小、輸送量和頻寬的詳細資訊，請參閱 [Azure Cache for Redis 常見問題集](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)。
>

快取建立好之後，您可以將快取調整至更高的層級。 但不支援向下調整至較低層級。 如需調整階層的逐步指示，請參閱[如何調整 Azure Cache for Redis](cache-how-to-scale.md) 和[如何將調整作業自動化](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

### <a name="feature-comparison"></a>功能比較

[Azure Cache for Redis 價格](https://azure.microsoft.com/pricing/details/cache/)頁面會提供每一層的詳細比較。 下表可協助說明階層所支援的一些功能：

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

## <a name="next-steps"></a>後續步驟

* [ASP.NET Web 應用程式快速入門](cache-web-app-howto.md)：建立可使用 Azure Cache for Redis 的簡單 ASP.NET Web 應用程式。
* [.NET 快速入門](cache-dotnet-how-to-use-azure-redis-cache.md)：建立可使用 Azure Cache for Redis 的 .NET 應用程式。
* [.NET Core 快速入門](cache-dotnet-core-quickstart.md)：建立可使用 Azure Cache for Redis 的 .NET Core 應用程式。
* [Node.js 快速入門](cache-nodejs-get-started.md)：建立可使用 Azure Cache for Redis 的簡單 Node.js 應用程式。
* [Java 快速入門](cache-java-get-started.md)：建立可使用 Azure Cache for Redis 的簡單 Java 應用程式。
* [Python 快速入門](cache-python-get-started.md)：建立可使用 Azure Cache for Redis 的 Python 應用程式。
