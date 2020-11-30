---
title: 如何監視 Azure Cache for Redis
description: 了解如何監視 Azure Cache for Redis 執行個體的健全狀況和效能
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 3d19d8f1b6a44f32e92f82e861471ca9b5c8fa41
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327333"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>如何監視 Azure Cache for Redis

Azure Cache for Redis 使用 [Azure 監視器](../azure-monitor/index.yml)提供數個選項來監視您的快取執行個體。 您可以檢視度量、將度量圖表釘選到「開始面板」、自訂監視圖表的日期和時間範圍、新增和移除圖表中的度量，以及設定符合特定條件時的警示。 這些工具可讓您監視 Azure Cache for Redis 執行個體的健康情況，並協助您管理快取應用程式。

Azure Cache for Redis 執行個體的計量使用 Redis [INFO](https://redis.io/commands/info) 命令每分鐘收集兩次，自動儲存 30 天 (請參閱[匯出快取計量](#export-cache-metrics)來設定不同的保留原則)，以便其顯示在計量圖表中並依警示規則評估。 如需用於每個快取度量的不同 INFO 值詳細資訊，請參閱 [可用度量和報告間隔](#available-metrics-and-reporting-intervals)。

<a name="view-cache-metrics"></a>

若要檢視快取度量，請[瀏覽](cache-configure.md#configure-azure-cache-for-redis-settings)至您在 [Azure 入口網站](https://portal.azure.com)中的快取執行個體。  Azure Cache for Redis 會在 [概觀] 刀鋒視窗和 [Redis 計量] 刀鋒視窗中提供一些內建圖表。 新增或移除度量，以及變更報告間隔，即可自訂每個圖表。

![顯示六個圖形。 其中一個是快取命中和過去一小時的快取遺漏。](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>檢視預先設定的計量圖表

[概觀] 刀鋒視窗具有下列預先設定的監視圖表。

* [監視圖表](#monitoring-charts)
* [使用量圖表](#usage-charts)

### <a name="monitoring-charts"></a>監視圖表

[概觀] 刀鋒視窗的 [監視] 區段具有 [點擊和遺漏]、[取得和設定]、[連線] 和 [總命令數] 圖表。

![監視圖表](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>使用量圖表

[概觀] 刀鋒視窗的 [使用量] 區段有 [Redis 伺服器負載]、[記憶體使用量]、[網路頻寬] 和 [CPU 使用量] 圖表，也會顯示快取執行個的 [定價層]。

![使用量圖表](./media/cache-how-to-monitor/redis-cache-usage-part.png)

[定價層]  會顯示快取定價層，而且可以用來將快取 [調整](cache-how-to-scale.md) 為不同的定價層。

## <a name="view-metrics-with-azure-monitor"></a>使用 Azure 監視器檢視計量

若要檢視 Redis 計量，並使用 Azure 監視器建立自訂圖表，請按一下 [資源] 功能表的 [計量]，使用所需的計量、報告間隔、圖表類型等來自訂圖表。

![在 contoso55 的左側流覽窗格中，[計量] 是 [監視] 底下的選項，並且會反白顯示。 計量上有計量清單。 已選取快取命中和快取遺漏。](./media/cache-how-to-monitor/redis-cache-monitor.png)

如需使用 Azure 監視器處理計量的詳細資訊，請參閱 [Microsoft Azure 的計量概觀](../azure-monitor/platform/data-platform.md)。

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>匯出快取計量

根據預設，Azure 監視器中的快取計量會[儲存 30 天](../azure-monitor/platform/data-platform-metrics.md)，而後刪除。 若要保留您的快取計量超過 30 天，您可以[指定儲存體帳戶](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)，並指定快取計量的 [保留天數] 原則。 

設定快取計量的儲存體帳戶：

1. 在 [ **Azure Cache for Redis** ] 頁面的 [ **監視** ] 標題底下，選取 [ **診斷**]。
2. 選取 [+新增診斷設定]。
3. 為設定命名。
4. 勾選 [封存至儲存體帳戶]  。 將診斷傳送至儲存體帳戶時，您將需要支付儲存和交易的一般數據傳輸費用。
4. 選取 [ **設定** ] 以選擇要在其中儲存快取計量的儲存體帳戶。
5. 在 [資料表標題 **度量**] 下，選取您要儲存的明細專案旁的核取方塊，例如 [ **AllMetrics**]。 指定 **保留 (天)** 原則。 您可以指定的保留天數上限為 **365 天**。 但是，如果您想要永久保留計量資料，請將 **保留期 (天)** 設定為 **0**。
6. 按一下 [儲存]。


![Redis 診斷](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>除了將您的快取計量封存至儲存體，您也可以將 [它們串流至事件中樞，或將它們傳送至 Azure 監視器記錄](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values)檔。
>

若要存取計量，您可以如本文先前所述，在 Azure 入口網站中進行檢視，也可以使用 [Azure 監視器計量 REST API](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) 存取計量。

> [!NOTE]
> 如果您變更儲存體帳戶，則先前設定之儲存體帳戶中的資料仍然可供下載，但不會顯示在 Azure 入口網站中。  
> 

## <a name="available-metrics-and-reporting-intervals"></a>可用度量和報告間隔

快取度量會報告使用數個報告的間隔，包括 **過去小時**，**今天**，**上星期**，和 **自訂**。 **度量** 刀鋒伺服器針對每個度量圖表顯示在圖表中，每個度量的平均值、 最小值和最大值和某些度量資訊的報告的時間間隔顯示總計。 

每個度量都包含兩個版本。 有一個度量會測量整個快取的效能，以及使用[叢集](cache-how-to-premium-clustering.md)之快取的效能，名稱中包含 `(Shard 0-9)` 的第二個度量版本則會測量快取中單一分區的效能。 例如，如果快取有四個分區， `Cache Hits` 則是整個快取的點擊總數，而只是該快取 `Cache Hits (Shard 3)` 分區的點擊數。

> [!NOTE]
> 即使快取閒置而沒有連線的作用中用戶端應用程式，您可能還是會看到某個快取活動 (例如連線的用戶端、記憶體使用量，以及正在執行的作業)。 在 Azure Cache for Redis 執行個體的作業期間，此活動正常。
> 
> 

| Metric | 描述 |
| --- | --- |
| 快取點擊 |所指定報告間隔期間的成功金鑰查閱數目。 此數位會對應至 `keyspace_hits` Redis [INFO](https://redis.io/commands/info) 命令。 |
| 快取延遲 (預覽) | 根據快取的內部節點延遲計算的快取延遲。 此計量會以微秒為單位來測量，並有三個維度： `Avg` 、 `Min` 和 `Max` ，分別代表快取在指定報告間隔期間的平均、最小值和最大延遲。 |
| 快取遺漏 |所指定報告間隔期間的失敗金鑰查閱數目。 此數位會對應至 `keyspace_misses` REDIS INFO 命令。 快取遺漏不一定表示快取發生問題。 例如，使用另行快取程式設計模式時，應用程式會先在快取中尋找項目。 如果項目不存在 (快取遺漏)，項目會從資料庫中擷取，並在下次新增至快取中。 快取遺漏是另行快取程式設計模式的正常行為。 如果快取遺漏數目高於預期，請檢查可填入且讀取自快取的應用程式邏輯。 如果因為記憶體不足的壓力而從快取中收回專案，則可能會發生快取遺漏，但要監視記憶體壓力的較佳計量是 `Used Memory` 或 `Evicted Keys` 。 |
| 快取讀取 |所指定報告間隔期間，從快取讀取的資料量 (以 MB/s 為單位)。 這個值衍生自網路介面卡，而網路介面卡支援裝載快取且非 Redis 特有的虛擬機器。 **此值對應于此快取所使用的網路頻寬。如果您想要設定伺服器端網路頻寬限制的警示，請使用此計數器來建立警示 `Cache Read` 。請參閱 [此表格](cache-planning-faq.md#azure-cache-for-redis-performance) ，以瞭解各種快取定價層和大小的觀察頻寬限制。** |
| 快取寫入 |所指定報告間隔期間，寫入至快取的資料量 (以 MB/s 為單位)。 這個值衍生自網路介面卡，而網路介面卡支援裝載快取且非 Redis 特有的虛擬機器。 此值對應從用戶端傳送給快取之資料的網路頻寬。 |
| 連線的用戶端 |所指定報告間隔期間的快取用戶端連線數目。 此數位會對應至 `connected_clients` REDIS INFO 命令。 一旦達到連線 [限制](cache-configure.md#default-redis-server-configuration) ，後續的快取連接嘗試將會失敗。 即使沒有作用中的用戶端應用程式，仍可能有幾個連接的用戶端實例，因為內部進程和連線。 |
| CPU |所指定報告間隔期間的 Azure Cache for Redis 伺服器 CPU 使用率 (百分比)。 這個值會對應至作業系統 `\Processor(_Total)\% Processor Time` 效能計數器。 |
| Errors | 在指定的報告間隔期間，快取可能遇到的特定失敗和效能問題。 此計量有八個代表不同錯誤類型的維度，但未來可能新增更多。 現在代表的錯誤類型如下所示： <br/><ul><li>**容錯移轉** –當快取容錯移轉 (從屬升級為主要) 時</li><li>**資料遺失** –快取上的資料遺失時</li><li>**UnresponsiveClients** – 當用戶端無法從速度夠快的伺服器讀取資料時</li><li>**AOF** – 有 AOF 持續性相關的問題時</li><li>**RDB** – 有 RDB 持續性相關的問題時</li><li>**匯入**– 有匯入 RDB 相關的問題時</li><li>**匯出**– 有匯出 RDB 相關的問題時</li></ul> |
| 收回的金鑰 |因 `maxmemory` 限制，在所指定報告間隔期間從快取收回的項目數。 此數位會對應至 `evicted_keys` REDIS INFO 命令。 |
| 到期的金鑰 |所指定報告間隔期間的快取到期項目數。 這個值會對應至 Redis INFO 命令的 `expired_keys` 。|
| 取得 |所指定報告間隔期間的快取 get 作業數目。 這個值是 Redis INFO all 命令的下列值總和：`cmdstat_get`、`cmdstat_hget``cmdstat_hgetall`、`cmdstat_hmget``cmdstat_mget`、`cmdstat_getbit` 和 `cmdstat_getrange`，而且等於報告期間的快取點擊和遺漏。 |
| 每秒的作業數 | 在指定的報告間隔期間，快取伺服器每秒所處理的命令總數。  這個值會對應至 Redis INFO 命令中的 "instantaneous_ops_per_sec"。 |
| Redis 伺服器負載 |Redis 伺服器忙著處理訊息且非訊息等候閒置之循環的百分比。 如果此計數器達到100，這表示 Redis 伺服器已達到效能上限，而且 CPU 無法以更快的速度處理工作。 如果您看到高 Redis 伺服器負載，您將會在用戶端看到超時例外狀況。 在此情況下，您應該考慮將資料擴大或分割成多個快取。 |
| 集合 |所指定報告間隔期間的快取 set 作業數目。 這個值是 Redis INFO all 命令的下列值總和：`cmdstat_set`、`cmdstat_hset``cmdstat_hmset`、`cmdstat_hsetnx`、`cmdstat_lset`、`cmdstat_mset`、`cmdstat_msetnx`、`cmdstat_setbit`、`cmdstat_setex`、`cmdstat_setrange` 和 `cmdstat_setnx` |
| 索引鍵總計  | 快取中的索引鍵在過去報告時段內的數目上限。 此數位會對應至 `keyspace` REDIS INFO 命令。 因為基礎計量系統的限制，凡是啟用叢集化的快取，[索引鍵總計] 都會傳回分區的索引鍵數目上限，該分區於報告間隔期間曾有過索引鍵的最大數目。  |
| 總作業數 |所指定報告間隔期間，快取伺服器所處理命令的總數。 這個值會對應至 Redis INFO 命令的 `total_commands_processed` 。 當 Azure Cache for Redis 純粹用於 pub/sub 時，不會有 `Cache Hits` 、、或的計量， `Cache Misses` `Gets` `Sets` 但會有 `Total Operations` 反映 pub/sub 作業的快取使用量的計量。 |
| 已使用的記憶體 |在指定的報告間隔期間，針對快取中金鑰/值組使用的快取記憶體數量 (MB)。 這個值會對應至 Redis INFO 命令的 `used_memory` 。 此值不包括中繼資料或片段。 |
| 已用的記憶體百分比 | 在指定的報告間隔期間，正在使用的總記憶體百分比。  此值會參考 `used_memory` REDIS INFO 命令的值來計算百分比。 |
| 已用的記憶體 RSS |在指定的報告間隔期間使用的快取記憶體數量 (MB)，包括片段和中繼資料。 這個值會對應至 Redis INFO 命令的 `used_memory_rss` 。 |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>警示

您可以進行設定來收到以計量和活動記錄為基礎的警示。 Azure 監視器可讓您將警示設定為在觸發時執行下列動作︰

* 傳送電子郵件通知
* 呼叫 Webhook
* 叫用 Azure 邏輯應用程式

若要設定快取警示規則，請按一下 [資源] 功能表的 [警示規則]。

![監視](./media/cache-how-to-monitor/redis-cache-monitoring.png)

如需設定和使用警示的詳細資訊，請參閱[警示概觀](../azure-monitor/platform/alerts-classic-portal.md)。

## <a name="activity-logs"></a>活動記錄
活動記錄可讓您深入了解 Azure Cache for Redis 執行個體上執行的作業。 此記錄以前稱為「稽核記錄」或「作業記錄」。 您可以使用活動記錄來判斷 Azure Cache for Redis 執行個體上所執行之任何寫入作業 (PUT、POST、DELETE) 的「內容、對象和時間」。 

> [!NOTE]
> 活動記錄不包含讀取 (GET) 作業。
>

若要檢視快取的活動記錄，請按一下 [資源] 功能表的 [活動記錄]。

如需活動記錄的詳細資訊，請參閱 [Azure 活動記錄概觀](../azure-monitor/platform/platform-logs-overview.md)。