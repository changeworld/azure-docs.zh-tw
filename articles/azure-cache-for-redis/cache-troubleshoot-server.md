---
title: 針對 Azure Cache for Redis 伺服器端問題進行疑難排解
description: 瞭解如何使用 Azure Cache for Redis 來解決常見的伺服器端問題，例如記憶體壓力、高 CPU、長時間執行的命令或頻寬限制。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008911"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>針對 Azure Cache for Redis 伺服器端問題進行疑難排解

本節討論在 Azure Cache for Redis 或虛擬 (機) 裝載它的情況下，所發生的疑難排解問題。

- [Redis 伺服器上的記憶體壓力](#memory-pressure-on-redis-server)
- [高 CPU 使用率或伺服器負載](#high-cpu-usage-or-server-load)
- [長時間執行的命令](#long-running-commands)
- [伺服器端的頻寬限制](#server-side-bandwidth-limitation)

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="memory-pressure-on-redis-server"></a>Redis 伺服器上的記憶體壓力

伺服器端上的記憶體壓力會導致各種效能問題，而可能延遲處理要求。 當記憶體壓力達到時，系統可能會將資料分頁至磁片。 此「分頁錯誤」 __ 會使系統明顯變慢。 此記憶體壓力有幾個可能的原因︰

- 快取會以接近其最大容量的資料填滿。
- Redis 看到高記憶體片段。 這種分散最常見的原因是儲存大型物件，因為 Redis 已針對小型物件優化。

Redis 會透過 [INFO](https://redis.io/commands/info) 命令公開兩個統計資料，協助您找出此問題：「used_memory」和「used_memory_rss」。 您可以使用入口網站來 [查看這些計量](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 。

您可以進行幾項可能的變更，以協助讓記憶體使用量保持良好狀況︰

- 設定[記憶體原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，並在您的金鑰上設定到期時間。 如果您的記憶體分散，此原則可能不足以應付。
- [設定 maxmemory 保留值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ，此值夠大以彌補記憶體片段。
- 將大型快取物件分割為較小的相關物件。
- 針對計量[建立警示](cache-how-to-monitor.md#alerts) (例如已使用的記憶體)，以及早獲得關於潛在影響的通知。
- 以較大的記憶體容量[調整](cache-how-to-scale.md)為較大的快取大小。

## <a name="high-cpu-usage-or-server-load"></a>高 CPU 使用率或伺服器負載

高伺服器負載或 CPU 使用方式表示伺服器無法及時處理要求。 伺服器的回應速度可能很慢，而且無法跟上要求率。

[監視](cache-how-to-monitor.md#view-metrics-with-azure-monitor) CPU 或伺服器負載等度量。 請監看與逾時對應的 CPU 使用量暴增。

您可以進行幾項變更來減輕高伺服器負載：

- 調查造成 CPU 尖峰的原因，如下列所示的 [長時間執行命令](#long-running-commands) 或因高記憶體壓力而發生分頁錯誤。
- [建立](cache-how-to-monitor.md#alerts) CPU 或伺服器負載等計量的警示，以便提早收到潛在影響的通知。
- 以較大的 CPU 容量[調整](cache-how-to-scale.md)為較大的快取大小。

## <a name="long-running-commands"></a>長時間執行的命令

某些 Redis 命令的執行成本比其他命令更昂貴。 [Redis 命令檔](https://redis.io/commands)會顯示每個命令的時間複雜度。 由於 Redis 命令處理是單一執行緒，因此需要時間執行的命令將會封鎖其後的所有其他專案。 您應該檢查您發出至 Redis 伺服器的命令，以瞭解其效能影響。 比方說， [金鑰](https://redis.io/commands/keys) 命令通常是在不知道 (N) 作業的情況下使用。 您可以使用 [ [掃描](https://redis.io/commands/scan) ] 來減少 CPU 尖峰，以避免使用金鑰。

您可以使用 [SLOWLOG](https://redis.io/commands/slowlog) 命令來測量針對伺服器執行的昂貴命令。

## <a name="server-side-bandwidth-limitation"></a>伺服器端的頻寬限制

不同的快取大小具有不同的網路頻寬容量。 如果伺服器超過可用的頻寬，則資料不會儘快傳送給用戶端。 用戶端要求可能會超時，因為伺服器無法快速將資料推送至用戶端。

「快取讀取」和「快取寫入」度量可以用來查看使用的伺服器端頻寬有多少。 您可以在入口網站中 [查看這些計量](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 。

若要降低網路頻寬使用量接近最大容量的情況：

- 變更用戶端呼叫行為以降低網路需求。
- 針對快取讀取或快取寫入之類的計量[建立警示](cache-how-to-monitor.md#alerts)，以及早獲得關於潛在影響的通知。
- 以較大的網路頻寬容量[調整](cache-how-to-scale.md)為較大的快取大小。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 用戶端問題進行疑難排解](cache-troubleshoot-client.md)
- [選擇正確的階層](cache-overview.md#choosing-the-right-tier)
- [如何效能評定和測試我快取的效能？](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
- [如何執行 Redis 命令？](cache-development-faq.md#how-can-i-run-redis-commands)
