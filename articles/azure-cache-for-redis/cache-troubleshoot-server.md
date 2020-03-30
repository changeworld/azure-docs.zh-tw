---
title: 針對 Azure Cache for Redis 伺服器端問題進行疑難排解
description: 瞭解如何使用 Redis 的 Azure 緩存解決常見的伺服器端問題，例如記憶體壓力、CPU 高、長時間運行的命令或頻寬限制。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277930"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>針對 Azure Cache for Redis 伺服器端問題進行疑難排解

本節討論由於 Redis 的 Azure 緩存或託管它的虛擬機器上出現的條件而出現的疑難排解問題。

- [Redis 伺服器上的記憶體壓力](#memory-pressure-on-redis-server)
- [CPU 使用率高或伺服器負載高](#high-cpu-usage-or-server-load)
- [長時間執行的命令](#long-running-commands)
- [伺服器端的頻寬限制](#server-side-bandwidth-limitation)

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="memory-pressure-on-redis-server"></a>Redis 伺服器上的記憶體壓力

伺服器端上的記憶體壓力會導致各種效能問題，而可能延遲處理要求。 當記憶體壓力來襲時，系統可能會將資料頁到磁片。 此「分頁錯誤」 __ 會使系統明顯變慢。 此記憶體壓力有幾個可能的原因︰

- 緩存中填充的資料接近其最大容量。
- 雷迪斯看到高記憶體碎片。 由於 Redis 針對小物件進行了優化，因此這種碎片通常是由存儲大型物件引起的。

Redis 通過[INFO](https://redis.io/commands/info)命令公開兩個統計資訊，可説明您識別此問題："used_memory"和"used_memory_rss"。 您可以使用門戶[查看這些指標](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

您可以進行一些可能的更改，以説明保持記憶體使用正常：

- [設定記憶體原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 並在金鑰上設定到期時間。 如果具有碎片，則此策略可能是不夠的。
- [設定 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ，讓此值大到足以彌補記憶體分散情形。
- 將大型快取物件分割為較小的相關物件。
- 在指標（如使用記憶體）上[創建警報](cache-how-to-monitor.md#alerts)，以便及早通知潛在影響。
- [以](cache-how-to-scale.md)更大的記憶體容量縮放到更大的緩存大小。

## <a name="high-cpu-usage-or-server-load"></a>CPU 使用率高或伺服器負載高

伺服器負載或 CPU 使用率高意味著伺服器無法及時處理請求。 伺服器的回應速度可能很慢，無法跟上請求速率。

[監視 CPU](cache-how-to-monitor.md#view-metrics-with-azure-monitor)或伺服器負載等指標。 請監看與逾時對應的 CPU 使用量暴增。

您可以進行以下幾個更改來緩解高伺服器負載：

- 調查導致 CPU 峰值的原因，例如下面注明[的長運行命令](#long-running-commands)或由於記憶體壓力高而導致頁面故障。
- 在 CPU 或伺服器負載等指標上[創建警報](cache-how-to-monitor.md#alerts)，以便及早通知有關潛在影響。
- [以](cache-how-to-scale.md)更大的 CPU 容量擴展到更大的緩存大小。

## <a name="long-running-commands"></a>長時間執行的命令

某些 Redis 命令的執行成本比其他命令要昂貴。 [Redis 命令文檔](https://redis.io/commands)顯示每個命令的時間複雜性。 由於 Redis 命令處理是單線程的，因此需要時間運行的命令將阻止它之後的所有其他命令。 您應該查看要向 Redis 伺服器發出的命令，以瞭解這些命令對性能的影響。 例如[，KEYS](https://redis.io/commands/keys)命令在不知道它是 O（N） 操作的情況下經常使用。 您可以使用[SCAN](https://redis.io/commands/scan)來減少 CPU 峰值來避免金鑰。

使用[SLOWLOG](https://redis.io/commands/slowlog)命令，可以測量針對伺服器執行的昂貴命令。

## <a name="server-side-bandwidth-limitation"></a>伺服器端的頻寬限制

不同的緩存大小具有不同的網路頻寬容量。 如果伺服器超過可用頻寬，則資料不會以較快的速度發送到用戶端。 用戶端請求可能會超時，因為伺服器無法以足夠快的速度將資料推送到用戶端。

"緩存讀取"和"緩存寫入"指標可用於查看正在使用的伺服器端頻寬量。 您可以在門戶中[查看這些指標](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

要緩解網路頻寬使用量接近最大容量的情況，：

- 更改用戶端調用行為以減少網路需求。
- 在緩存讀取或緩存寫入等指標上[創建警報](cache-how-to-monitor.md#alerts)，以便及早通知有關潛在影響。
- [以](cache-how-to-scale.md)更大的網路頻寬容量擴展到更大的緩存大小。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 用戶端問題進行疑難排解](cache-troubleshoot-client.md)
- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
