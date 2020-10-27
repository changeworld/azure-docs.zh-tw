---
title: Azure Cache for Redis 規劃常見問題
description: 瞭解可協助您規劃 Azure Cache for Redis 之常見問題的解答
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537400"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Azure Cache for Redis 規劃常見問題

本文提供有關如何規劃 Azure Cache for Redis 的常見問題解答。

## <a name="common-questions-and-answers"></a>常見問題與答案
本節涵蓋下列常見問題：

* [Azure Redis 快取效能](#azure-cache-for-redis-performance)
* [我應該在哪個區域找到快取？](#in-what-region-should-i-locate-my-cache)
* [我的快取資料位於何處？](#where-do-my-cached-data-reside)
* [Azure Redis 快取如何收費？](#how-am-i-billed-for-azure-cache-for-redis)
* [我可以搭配 Azure Government 雲端、Azure 中國的世紀雲端或 Microsoft Azure 德國使用 Azure Cache for Redis 嗎？](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure Redis 快取效能
下表顯示從 IaaS VM 使用 `redis-benchmark.exe` 對「Azure Redis 快取」端點測試各種大小的「標準」和「進階」快取時，觀察到的最大頻寬值。 針對 TLS 輸送量，Redis 基準可與 stunnel 搭配使用來連線到「 Azure Cache for Redis」端點。

>[!NOTE] 
>這些值並非保證值，也沒有關於這些數字的 SLA，只代表一般情況。 您應該載入測試自己的應用程式，以判斷應用程式的正確快取大小。
>隨著我們定期公佈較新的結果，這些數字可能會變更。
>

我們可以從這個表格中獲得下列結論：

* 進階層中相同大小快取的輸送量，比標準層的還高。 例如，在有 6 GB 快取的前提下，相較於 C3 的 100,000，P1 每秒是 180,000 個要求 (RPS)。
* 使用 Redis 叢集，當您增加叢集中的分區 (節點) 數目時，輸送量會呈線性增加。 例如，如果您建立具有 10 個分區的 P4 叢集，則可用的輸送量為 400,000 *10 = 400 萬個 RPS。
* 相較於標準層，進階層中的金鑰大小越大，輸送量就越高。

| 定價層 | 大小 | CPU 核心 | 可用的頻寬 | 1 KB 值大小 | 1 KB 值大小 |
| --- | --- | --- | --- | --- | --- |
| **標準快取大小** | | |**每秒 Mb (Mb/s) / 每秒 MB (MB/s)** |**每秒要求數目 (RPS) 非 SSL** |**每秒要求數目 (RPS) SSL** |
| C0 | 250 MB | 共用 | 100 / 12.5  |  15,000 |   7,500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2.5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100,000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60,000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **高階快取大小** | |**每一分區的 CPU 核心數目** | **每秒 Mb (Mb/s) / 每秒 MB (MB/s)** |**每秒要求數目 (RPS) 非 SSL，每個分區** |**每秒要求數目 (RPS) SSL，每個分區** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

如需設定 stunnel 或下載 Redis 工具（例如）的指示 `redis-benchmark.exe` ，請參閱 [如何執行 Redis 命令？](cache-development-faq.md#how-can-i-run-redis-commands)。

### <a name="in-what-region-should-i-locate-my-cache"></a>我應該在哪個區域找到快取？
為獲得最佳效能和最低延遲，請將「Azure Redis 快取」放在與快取用戶端應用程式相同的區域中。

### <a name="where-do-my-cached-data-reside"></a>我的快取資料位於何處？
Azure Cache for Redis 會將您的應用程式資料儲存在 VM 的 RAM 或 vm 中，視裝載快取的層而定。 您的資料會嚴格地存放在您預設選取的 Azure 區域中。 在兩種情況下，您的資料可能會離開區域：
* 當您啟用快取的持續性時，Azure Cache for Redis 會將您的資料備份到您擁有的 Azure 儲存體帳戶。 如果您提供的儲存體帳戶發生在另一個區域中，您的資料複本將會在該處。
* 如果您設定了異地複寫，而且次要快取位於不同的區域（這通常是如此，則您的資料會複寫至該區域）。

您必須明確設定 Azure Cache for Redis，才能使用這些功能。 您也可以完全掌控儲存體帳戶或次要快取所在的區域。

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Azure Redis 快取如何收費？
如需了解「Azure Redis 快取」定價，請參閱[這裡](https://azure.microsoft.com/pricing/details/cache/)。 定價頁面會以每小時和每月費率列出定價。 快取是根據從建立快取到刪除快取的時間，以分鐘為單位來收費。 沒有用於停止或暫停快取收費的選項。

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>我可以搭配 Azure Government 雲端、Azure 中國的世紀雲端或 Microsoft Azure 德國使用 Azure Cache for Redis 嗎？
是，「Azure Government 雲端」、「Azure China 21Vianet 雲端」及「Microsoft Azure 德國」都有提供「Azure Cache for Redis」。 這些雲端中用來存取及管理「Azure Redis 快取」的 URL 與「Azure 公用雲端」不同。

| Cloud   | Redis 的 DNS 尾碼            |
|---------|---------------------------------|
| 公開  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| 德國 | *.redis.cache.cloudapi.de       |
| 中國   | *.redis.cache.chinacloudapi.cn  |

如需深入了解搭配其他雲端使用「Azure Redis 快取」的考量，請參閱下列連結。

- [Azure Government 資料庫 - Azure Redis 快取](../azure-government/compare-azure-government-global-azure.md)
- [Azure China 21Vianet 雲端 - Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure (德國)](https://azure.microsoft.com/overview/clouds/germany/)

如需有關在「Azure Government 雲端」、「Azure China 21Vianet 雲端」及「Microsoft Azure 德國」中搭配 PowerShell 使用「Azure Cache for Redis」的資訊，請參閱[如何連線到其他雲端 - Azure Cache for Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)。

## <a name="next-steps"></a>下一步

瞭解其他 [Azure Cache for Redis 常見問題](cache-faq.md)。