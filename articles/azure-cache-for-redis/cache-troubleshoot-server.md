---
title: 針對 Azure Cache for Redis 伺服器端問題進行疑難排解
description: 瞭解如何解決 Azure Cache for Redis 的一般伺服器端問題，例如記憶體壓力、高 CPU、長時間執行的命令或頻寬限制。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372162"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>針對 Azure Cache for Redis 伺服器端問題進行疑難排解

本節討論因 Azure Cache for Redis 或裝載它的虛擬機器上的條件而發生的疑難排解問題。

- [Redis 伺服器上的記憶體壓力](#memory-pressure-on-redis-server)
- [高 CPU 使用率或伺服器負載](#high-cpu-usage-or-server-load)
- [長時間執行的命令](#long-running-commands)
- [伺服器端的頻寬限制](#server-side-bandwidth-limitation)

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="memory-pressure-on-redis-server"></a>Redis 伺服器上的記憶體壓力

伺服器端上的記憶體壓力會導致各種效能問題，而可能延遲處理要求。 當記憶體壓力達到時，系統可能會將資料分頁到磁片。 此「分頁錯誤」 會使系統明顯變慢。 此記憶體壓力有幾個可能的原因︰

- 快取會填入接近其最大容量的資料。
- Redis 看到較高的記憶體片段。 此片段最常見的原因是儲存大型物件，因為 Redis 已針對小型物件進行優化。

Redis 會透過[INFO](https://redis.io/commands/info)命令公開兩個統計資料，協助您找出此問題：「used_memory」和「used_memory_rss」。 您可以使用入口網站來[查看這些計量](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

您可以進行幾項可能的變更，以協助保持記憶體使用量良好：

- [設定記憶體原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 並在金鑰上設定到期時間。 如果您有片段，此原則可能不足。
- [設定 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ，讓此值大到足以彌補記憶體分散情形。
- 將大型快取物件分割為較小的相關物件。
- 針對計量（例如已使用的記憶體）[建立警示](cache-how-to-monitor.md#alerts)，以在早期通知潛在的影響。
- [調整](cache-how-to-scale.md)為具有更多記憶體容量的較大快取大小。

## <a name="high-cpu-usage-or-server-load"></a>高 CPU 使用率或伺服器負載

伺服器負載過高或 CPU 使用率表示伺服器無法及時處理要求。 伺服器可能回應速度很慢，而且無法跟上要求率。

[監視](cache-how-to-monitor.md#view-metrics-with-azure-monitor)CPU 或伺服器負載之類的計量。 請監看與逾時對應的 CPU 使用量暴增。

您可以進行幾項變更，以減少伺服器負載的高：

- 調查導致 CPU 尖峰的原因，例如，下面所述的[長時間執行命令](#long-running-commands)，或因記憶體不足的壓力而發生分頁錯誤。
- 針對 CPU 或伺服器負載之類的計量[建立警示](cache-how-to-monitor.md#alerts)，以在早期通知潛在的影響。
- [調整](cache-how-to-scale.md)為具有更多 CPU 容量的較大快取大小。

## <a name="long-running-commands"></a>長時間執行的命令

某些 Redis 命令的執行成本高於其他命令。 [Redis 命令檔](https://redis.io/commands)會顯示每個命令的時間複雜性。 由於 Redis 命令處理是單一執行緒，因此需要時間執行的命令將會封鎖它後面的所有其他專案。 您應該檢查您要向 Redis 伺服器發出的命令，以瞭解它們的效能影響。 例如，通常會使用[KEYS](https://redis.io/commands/keys)命令，而不知道它是 O （N）運算。 您可以使用 [[掃描](https://redis.io/commands/scan)] 來減少 CPU 尖峰，以避免金鑰。

您可以使用[SLOWLOG](https://redis.io/commands/slowlog)命令來測量針對伺服器執行的昂貴命令。

## <a name="server-side-bandwidth-limitation"></a>伺服器端頻寬限制

不同的快取大小有不同的網路頻寬容量。 如果伺服器超過可用的頻寬，則資料不會快速傳送到用戶端。 用戶端要求可能會超時，因為伺服器無法快速將資料推送至用戶端。

「快取讀取」和「快取寫入」計量可以用來查看使用的伺服器端頻寬數量。 您可以在入口網站中[查看這些計量](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

若要減少網路頻寬使用量接近最大容量的情況：

- 變更用戶端呼叫行為以減少網路需求。
- 針對「快取讀取」或「快取寫入」等計量[建立警示](cache-how-to-monitor.md#alerts)，以在早期通知潛在的影響。
- [調整](cache-how-to-scale.md)為具有更多網路頻寬容量的較大快取大小。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 用戶端問題進行疑難排解](cache-troubleshoot-client.md)
- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
