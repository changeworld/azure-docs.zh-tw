---
title: Azure Cache for Redis 監視和疑難排解常見問題
description: 瞭解可協助您監視和疑難排解 Azure Cache for Redis 常見問題的解答
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537519"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Azure Cache for Redis 監視和疑難排解常見問題
本文提供有關如何監視和疑難排解 Azure Cache for Redis 的常見問題解答。

## <a name="common-questions-and-answers"></a>常見問題與答案
本節涵蓋下列常見問題：

* [如何監視快取的健全狀況和效能？](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [為什麼看到逾時？](#why-am-i-seeing-timeouts)
* [我的用戶端為什麼中斷與快取的連線？](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>如何監視快取的健全狀況和效能？
您可以在 [Azure 入口網站](https://portal.azure.com)中監視「Microsoft Azure Redis 快取」執行個體。 您可以檢視度量、將度量圖表釘選到「開始面板」、自訂監視圖表的日期和時間範圍、新增和移除圖表中的度量，以及設定符合特定條件時的警示。 如需詳細資訊，請參閱[監視 Azure Redis 快取](cache-how-to-monitor.md)。

「Azure Redis 快取」 **資源功能表** 也包含數個工具，可對快取進行監控和疑難排解。

* **疑難排解和解決問題** 提供常見問題的相關資訊，以及解決問題的策略。
* **資源健康狀態** 會監看您的資源，並告知您資源是否正如預期般執行。 如需 Azure 資源健康狀態服務的詳細資訊，請參閱 [Azure 資源健康狀態概觀](../service-health/resource-health-overview.md)。
* **新增支援要求** 提供選項來提出快取的支援要求。

這些工具可讓您監視 Azure Cache for Redis 執行個體的健康情況，並協助您管理快取應用程式。 如需詳細資訊，請參閱[如何設定 Azure Redis 快取](cache-configure.md)的＜支援和疑難排解設定＞一節。

### <a name="why-am-i-seeing-timeouts"></a>為什麼看到逾時？
用來與 Redis 溝通的用戶端發生逾時。 將命令傳送到 Redis 伺服器時，會將命令排入佇列，而且 Redis 伺服器最後會挑選並執行命令。 不過，用戶端可能會在此程序期間逾時，而且，如果是這樣，則會在呼叫端引發例外狀況。 如需為逾時問題疑難排解的詳細資訊，請參閱[用戶端疑難排解](cache-troubleshoot-client.md)和 [StackExchange.Redis 逾時例外狀況](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)。

### <a name="why-was-my-client-disconnected-from-the-cache"></a>我的用戶端為什麼中斷與快取的連線？
下面是快取中斷連線的一些常見原因。

* 用戶端原因
  * 已重新部署用戶端應用程式。
  * 用戶端應用程式已執行調整作業。
    * 如果是雲端服務或 Web Apps，這可能是自動調整所造成。
  * 用戶端上的網路層已變更。
  * 在用戶端或用戶端與伺服器之間的網路節點中，發生暫時性錯誤。
  * 已達頻寬閾值限制。
  * CPU 繫結作業用了太多時間才完成。
* 伺服器端原因
  * 在標準快取供應專案中，Azure Cache for Redis 服務起始從主要節點到複本節點的容錯移轉。
  * Azure 會修補已部署快取的執行個體
    * 這可能適用於 Redis 伺服器更新或一般 VM 維護。


## <a name="next-steps"></a>下一步

如需有關監視「Azure Cache for Redis」執行個體和進行疑難排解的詳細資訊，請參閱[如何監視 Azure Cache for Redis](cache-how-to-monitor.md) 和各種疑難排解指南。

瞭解其他 [Azure Cache for Redis 常見問題](cache-faq.md)。