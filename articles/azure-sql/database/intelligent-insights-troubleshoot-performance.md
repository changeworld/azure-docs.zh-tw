---
title: 使用 Intelligent Insights 針對效能問題進行疑難排解
description: Intelligent Insights 可協助您針對 Azure SQL Database 和 Azure SQL 受控執行個體效能問題進行疑難排解。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 80f5d6033429c40f468d525a088bcc72bdc3375b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450293"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>針對 Azure SQL Database 和 Azure SQL 受控執行個體 Intelligent Insights 的效能問題進行疑難排解
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

此頁面提供透過 [Intelligent Insights](intelligent-insights-overview.md) 資源記錄檔偵測到的 Azure SQL Database 和 Azure SQL 受控執行個體效能問題的相關資訊。 您可以將計量和資源記錄串流至 [Azure 監視器記錄](../../azure-monitor/insights/azure-sql.md)、 [Azure 事件中樞](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)、 [Azure 儲存體](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)或協力廠商解決方案，以取得自訂 DevOps 警示和報告功能。

> [!NOTE]
> 如需使用 Intelligent Insights 的快速效能疑難排解指南，請參閱本檔中的 [建議疑難排解流程](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) 流程圖。
>
> 智慧型深入解析是預覽功能，無法在下欄區域使用：西歐、北歐、美國西部1和美國東部1。

## <a name="detectable-database-performance-patterns"></a>可偵測的資料庫效能模式

Intelligent Insights 會根據查詢執行等候時間、錯誤或超時時間，自動偵測效能問題。 Intelligent Insights 會將偵測到的效能模式輸出到資源記錄檔。 下表摘要說明可偵測的效能模式。

| 可偵測的效能模式 | Azure SQL Database | Azure SQL 受控執行個體 |
| :------------------- | ------------------- | ------------------- |
| [達到資源限制](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | 可用資源 (Dtu) 、資料庫背景工作執行緒或受監視訂用帳戶上可用之資料庫登入會話的使用量已達到其資源限制。 這會影響效能。 | CPU 資源的耗用量即將達到其資源限制。 這會影響資料庫效能。 |
| [工作負載增加](intelligent-insights-troubleshoot-performance.md#workload-increase) | 偵測到工作負載增加或資料庫上工作負載的持續累積。 這會影響效能。 | 偵測到工作負載增加。 這會影響資料庫效能。 |
| [記憶體壓力](intelligent-insights-troubleshoot-performance.md#memory-pressure) | 要求記憶體授與的背景工作角色必須等待統計的大量時間，或是要求的記憶體授與之背景工作的累積增加。 這會影響效能。 | 要求記憶體授與的背景工作角色，在統計上來說花了很長的時間等待記憶體配置。 這會影響資料庫效能。 |
| [鎖定](intelligent-insights-troubleshoot-performance.md#locking) | 偵測到過多的資料庫鎖定會影響效能。 | 偵測到過多的資料庫鎖定，這會影響資料庫效能。 |
| [MAXDOP 增加](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | 平行處理原則的最大程度 (MAXDOP) 選項已變更，因而影響查詢執行效率。 這會影響效能。 | 平行處理原則的最大程度 (MAXDOP) 選項已變更，因而影響查詢執行效率。 這會影響效能。 |
| [頁面閂鎖爭用](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | 多個執行緒同時嘗試存取相同的記憶體內資料緩衝區分頁，因而導致等候時間增加，且造成分頁閂鎖爭用。 這會影響效能。 | 多個執行緒同時嘗試存取相同的記憶體內資料緩衝區分頁，因而導致等候時間增加，且造成分頁閂鎖爭用。 這會影響資料庫的效能。 |
| [遺漏索引](intelligent-insights-troubleshoot-performance.md#missing-index) | 偵測到遺漏索引會影響效能。 | 偵測到遺漏索引，這會影響資料庫效能。 |
| [新增查詢](intelligent-insights-troubleshoot-performance.md#new-query) | 偵測到新查詢，因而影響整體效能。 | 偵測到新查詢，這會影響整體資料庫效能。 |
| [增加的等候統計資料](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | 偵測到增加的資料庫等候時間會影響效能。 | 偵測到增加的資料庫等候時間，這會影響資料庫效能。 |
| [TempDB 爭用](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | 多個執行緒嘗試存取相同的 TempDB 資源，因而造成瓶頸。 這會影響效能。 | 多個執行緒嘗試存取相同的 TempDB 資源，因而造成瓶頸。 這會影響資料庫效能。 |
| [彈性集區 DTU 不足](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | 彈性集區中的可用 Edtu 不足，會影響效能。 | 因為 Azure SQL 受控執行個體使用 vCore 模型，所以無法使用。 |
| [計畫回歸](intelligent-insights-troubleshoot-performance.md#plan-regression) | 偵測到新的計畫或現有計畫中的工作負載變更。 這會影響效能。 | 偵測到新的計畫或現有計畫中的工作負載變更。 這會影響資料庫效能。 |
| [資料庫範圍組態值變更](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | 偵測到資料庫上的設定變更，這會影響資料庫效能。 | 偵測到資料庫上的設定變更，這會影響資料庫效能。 |
| [用戶端執行速度太慢](intelligent-insights-troubleshoot-performance.md#slow-client) | 緩慢的應用程式用戶端無法以夠快的速度取用來自資料庫的輸出。 這會影響效能。 | 緩慢的應用程式用戶端無法以夠快的速度取用來自資料庫的輸出。 這會影響資料庫效能。 |
| [定價層降級](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | 定價層降級動作減少了可用資源。 這會影響效能。 | 定價層降級動作減少了可用資源。 這會影響資料庫效能。 |

> [!TIP]
> 針對資料庫的持續效能優化，請啟用 [自動調整](automatic-tuning-overview.md)。 此內建智慧功能會持續監視您的資料庫、自動調整索引，並套用查詢執行計畫更正。
>

下一節詳細說明可偵測的效能模式。

## <a name="reaching-resource-limits"></a>達到資源限制

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式結合了與達到可用資源限制、背景工作角色限制及工作階段限制有關的效能問題。 偵測到此效能問題之後，診斷記錄的描述欄位就會指出效能問題是否與資源、背景工作角色，或是工作階段限制相關。

Azure SQL Database 上的資源通常稱為 [DTU](service-tiers-dtu.md) 或 [vCore](service-tiers-vcore.md) 資源，而 Azure SQL 受控執行個體上的資源則稱為 vCore 資源。 當偵測到導致查詢效能降低的原因是達到所測量的任何資源限制時，就會認定達到資源限制的模式。

會話限制資源代表資料庫的可用並行登入數目。 當連接至資料庫的應用程式已達到資料庫的可用並行登入數目時，就會辨識此效能模式。 當應用程式嘗試使用的工作階段數超出資料庫上可用的工作階段數時，就會影響查詢效能。

達到背景工作角色限制是達到資源限制的特定案例，因為 DTU 或 vCore 使用量中並未計入可用的背景工作角色數。 達到資料庫上的背景工作角色限制會造成資源特定的等候時間增加，因而降低查詢效能。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出影響效能和資源耗用量百分比之查詢的查詢雜湊。 您可以使用此資訊作為將資料庫工作負載最佳化的起點。 特別是，您可以新增索引來對影響效能降低的查詢進行最佳化。 或者，您可以透過更加平均的工作負載分佈來對應用程式進行最佳化。 如果您無法減少工作負載或進行優化，請考慮增加資料庫訂用帳戶的定價層，以增加可用的資源數量。

如果您已達到可用工作階段限制，則可以透過減少資料庫的登入次數來對應用程式進行最佳化。 如果您無法減少從應用程式到資料庫的登入次數，請考慮增加資料庫訂用帳戶的定價層。 或者，您可以將資料庫分割並移至多個資料庫，以取得更加平衡的工作負載分佈。

如需有關解決會話限制的詳細資訊，請參閱 [如何處理最大登入的限制](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/)。 如需有關伺服器和訂閱層級之限制的詳細資訊，請參閱 [伺服器上的資源限制總覽](resource-limits-logical-server.md) 。

## <a name="workload-increase"></a>工作負載增加

### <a name="what-is-happening"></a>發生的情況

這個效能模式所識別的是工作負載增加或其更嚴重形式 (即工作負載堆積) 所造成的問題。

這個偵測是透過組合數個計量來進行的。 所測量的基本計量會偵測與過去的工作負載基準相比，工作負載是否增加。 另一個偵測形式根據的是測量作用中背景工作執行緒是否大幅增加，亦即是否大到足以影響查詢效能。

在其更嚴重的形式中，工作負載可能會因為資料庫無法處理工作負載而持續堆積。 結果就是工作負載大小持續成長，也就是工作負載堆積的情況。 因為此情況，工作負載等候執行的時間將會增加。 此情況為其中一個最嚴重的資料庫效能問題。 透過監視中止的背景工作執行緒數目即可偵測到這個問題。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出執行時間增加的查詢數目，以及對工作負載增加影響最大之查詢的查詢雜湊。 您可以使用此資訊作為將工作負載最佳化的起點。 將識別對工作負載增加影響最大的查詢作為起點，將會特別有幫助。

您也可以考慮將工作負載更平均地分配給資料庫。 請考慮新增索引以針對影響效能的查詢進行最佳化。 您也可以將工作負載散發至多個資料庫。 如果無法使用這些解決方案，請考慮增加資料庫訂用帳戶的定價層，以增加可用的資源數量。

## <a name="memory-pressure"></a>記憶體壓力

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去七天的效能基準相比，目前的資料庫效能降低，原因是記憶體壓力或其更嚴重的形式，亦即有記憶體堆積的情況。

記憶體壓力代表一種效能情況，其中有大量的背景工作執行緒要求記憶體授與。 大量的磁片區會造成高記憶體使用量的情況，其中資料庫無法有效率地將記憶體配置給要求它的所有背景工作。 此問題最常見的原因之一，是與資料庫的可用記憶體數量有關。 另一方面的因素，則是因工作負載增加而造成的背景工作執行緒及記憶體壓力提升。

記憶體壓力的更嚴重形式，則是記憶體堆積的情況。 此情況指出要求記憶體授與的背景工作執行緒數目，比釋放記憶體的查詢還要多。 要求記憶體授與的背景工作執行緒數目，也可能會持續增加 (堆積) ，因為資料庫引擎無法有效率地配置記憶體來滿足需求。 記憶體堆積情況為其中一個最嚴重的資料庫效能問題。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出記憶體物件存放區詳細資料，其中會將 Clerk (即背景工作執行緒) 標示為高記憶體使用量的最大原因，也會輸出相關的時間戳記。 您可以使用此資訊作為疑難排解的基礎。

您可以將與具有最高記憶體使用量之 Clerk 相關的查詢最佳化或移除。 您也可以確定自己不會查詢沒有計畫使用的資料。 理想的做法是在您的查詢中一律使用 WHERE 子句。 此外，建議您建立非叢集索引來搜尋資料，而不要掃描它。

您也可以對工作負載進行最佳化，或將它散發至多個資料庫來減少工作負載。 或者，您可以將工作負載散發至多個資料庫。 如果這些解決方案都不可行，請考慮提高資料庫的定價層，以增加資料庫可用的記憶體資源數量。

如需其他疑難排解建議，請參閱[記憶體授與深思：有許多名稱的神秘 SQL Server 記憶體取用者](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994) \(英文\)。

## <a name="locking"></a>鎖定

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去七天的效能基準相比，目前的資料庫效能降低，其中偵測到過多的資料庫鎖定。

在現代化的 RDBMS 中，對於實作多執行緒的系統 (其中會透過儘可能執行多個同時背景工作角色和平行資料庫交易來發揮最大效能) 來說，鎖定是不可或缺的。 此內容中的鎖定係指內建的存取機制，其中只有單一交易能夠以獨佔方式存取所需的資料列、頁面、資料表及檔案，而不會與另一個交易競爭資源。 當鎖定資源來使用的交易已經使用完資源時，就會釋出那些資源上的鎖定，這能允許其他交易存取所需的資源。 如需有關鎖定的詳細資訊，請參閱[資料庫引擎中的鎖定](https://msdn.microsoft.com/library/ms190615.aspx)。

當 SQL 引擎所執行的交易存取已鎖定使用的資源時，如果等候時間較長，這個等候時間將會導致工作負載執行效能變差。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出鎖定詳細資料，料可用來作為疑難排解的基礎。 您可以分析系統回報的鎖定查詢 (即導致鎖定效能降低的查詢)，然後將它們移除。 在某些情況下，您可能可以順利將鎖定查詢最佳化。

此問題的最簡單且最安全緩和方式就是讓交易簡短，以及減少最耗用資源之查詢的鎖定使用量。 您可以將大型的作業批次分成較小的作業。 理想的做法是儘可能提升查詢效率，以減少查詢鎖定使用量。 請減少大型掃描，因為它們會增加死結的機率，並對整體資料庫效能帶來負面影響。 針對已識別為會造成鎖定的查詢，您可以建立新的索引，或是將資料行新增至現有索引中來避免資料表掃描。

如需更多建議，請參閱[如何解決 SQL Server 中鎖定擴大所造成的鎖定問題](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in) (英文)。

## <a name="increased-maxdop"></a>MAXDOP 增加

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示所選查詢執行計畫的平行處理程度超出其應有程度的情況。 查詢最佳化工具可以平行執行查詢來提高工作負載效能，並盡可能加速處理。 在某些情況下，相較於使用較少平行背景工作角色 (或在某些情況下相較於單一背景工作處理序) 來執行相同的查詢，處理查詢的平行背景工作角色會花費更多的時間等候彼此進行同步並合併結果。

專家系統會以基準期間作為比較，對目前的資料庫效能進行分析。 它會判斷先前執行的查詢是否因為查詢執行計畫的平行處理程度超出其應有程度，而使其執行的速度比先前還慢。

MAXDOP 伺服器設定選項可用來控制可使用多少 CPU 核心來平行執行相同的查詢。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出執行持續時間因平行處理程度大於應有程度而增加之查詢的相關查詢雜湊。 此記錄也會輸出 CXP 等候時間。 此時間代表單一組合管理/協調者執行緒 (執行緒 0) 在合併結果並繼續進行之前，等候所有其他執行緒完成工作的時間。 此外，診斷記錄也會輸出效能不佳之查詢整體等候執行的等候時間。 您可以使用此資訊作為疑難排解的基礎。

首先，請將複雜的查詢最佳化或簡化。 理想的做法是將大批作業拆解成較小的作業。 此外，請確定您已建立支援查詢的索引。 您也可以針對已標示為效能不佳的查詢，手動強制執行平行處理原則的最大程度 (MAXDOP)。 若要使用 T-SQL 設定此作業，請參閱[設定 MAXDOP 伺服器設定選項](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)。

將 MAXDOP 伺服器設定選項設定為零 (0) 作為預設值，表示資料庫可以使用所有可用的 CPU 核心來平行處理執行緒，以執行單一查詢。 將 MAXDOP 設定為一 (1) 表示針對單一查詢執行只能使用一個核心。 實際上，這代表平行處理已關閉。 您可以視每一案例情況、資料庫可用的核心及診斷記錄資訊而定，將 MAXDOP 選項調整成可能可以解決您案例中問題的平行查詢執行核心數。

## <a name="pagelatch-contention"></a>頁面閂鎖爭用

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去七天的工作負載基準相比，目前的資料庫工作負載效能降低，原因是發生頁面閂鎖爭用。

閂鎖是用來啟用多執行緒的輕量同步處理機制。 它們能保證記憶體內部結構 (包括索引、資料頁面及其他內部結構) 的一致性。

有許多類型的閂鎖可用。 為了簡單起見，系統會使用緩衝區閂鎖來保護緩衝集區中的記憶體內部頁面。 系統會使用 IO 閂鎖來保護尚未載入緩衝集區的頁面。 每次將資料寫入到緩衝集區中的頁面或從該頁面讀取資料時，背景工作執行緒都必須先取得該頁面的緩衝區閂鎖。 每次背景工作執行緒嘗試存取記憶體內緩衝集區中尚未提供的頁面時，都會發出 IO 要求以從儲存體載入所需的資訊。 這些事件代表的是一種更嚴重的效能降低情況。

當多個執行緒同時嘗試在相同的記憶體內結構上取得閂鎖時，就會發生頁面閂鎖爭用，因而導致等候執行查詢的時間增加。 當必須從儲存體存取資料時，如果發生頁面閂鎖 IO 爭用，這個等候時間甚至會更長。 它可能會大幅影響工作負載效能。 對於等候彼此並競爭多個 CPU 系統上資源的執行緒來說，頁面閂鎖爭用是最常見的情況。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出頁面閂鎖爭用的詳細資料。 您可以使用此資訊作為疑難排解的基礎。

由於頁面閂鎖是內部控制機制，因此它會自動判斷何時使用它們。 因為閂鎖具有決定性行為，所以應用程式決策 (包括結構描述設計) 會影響頁面閂鎖行為。

其中一個處理閂鎖爭用的方法是使用非循序索引鍵來取代循序索引鍵，以將插入平均分散到整個索引範圍。 通常，在索引中建立前置資料行將可以按比例分配工作負載。 另一個可考慮的方法是資料表分割。 使用資料分割資料表上的計算資料行來建立雜湊分割配置，是一個緩和過多閂鎖爭用的常見方法。 當發生頁面閂鎖 IO 爭用時，導入索引可協助緩和這個效能問題。

如需詳細資訊，請參閱[診斷和解決 SQL Server 上的閂鎖爭用](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) \(英文\) (PDF 下載)。

## <a name="missing-index"></a>遺漏索引

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示與過去七天的基準相比，目前的資料庫工作負載效能降低，原因是遺漏索引。

索引可用來提升查詢的效能。 它可藉由減少所需瀏覽或掃描的資料集頁數，加快存取資料表資料的速度。

透過這項偵測可識別出造成效能降低的特定查詢，針對這些查詢，建立索引將有助於提升效能。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出已識別為影響工作負載效能之查詢的查詢雜湊。 您可以針對這些查詢建立索引。 您也可以將索引最佳化，或是在不需要的情況下將它們移除。 理想的效能做法是避免查詢您不使用的資料。

> [!TIP]
> 您知道內建的智慧功能可以自動為資料庫管理效能最佳的索引嗎？
>
> 針對持續效能優化，建議您啟用 [自動調整](automatic-tuning-overview.md)。 這項獨特的內建智慧功能會持續監視您的資料庫，並自動為資料庫調整及建立索引。
>

## <a name="new-query"></a>新增查詢

### <a name="what-is-happening"></a>發生的情況

這個效能模式表示偵測到新查詢，而與過去七天效能基準相比，此查詢的效能不佳且影響工作負載效能。

撰寫效能良好的查詢有時會是相當具有挑戰性的工作。 如需撰寫查詢的詳細資訊，請參閱[撰寫 SQL 查詢](https://msdn.microsoft.com/library/bb264565.aspx) \(英文\)。 若要對現有查詢效能進行最佳化，請參閱[查詢微調](https://msdn.microsoft.com/library/ms176005.aspx)。

### <a name="troubleshooting"></a>疑難排解

診斷記錄最多會輸出兩個耗用最多 CPU 資源之查詢的資訊，包括其查詢雜湊。 因為偵測到的查詢會影響工作負載效能，您可以將查詢最佳化。 理想的做法是僅擷取所需的資料。 我們也建議使用搭配 WHERE 子句的查詢。 我們也建議您將複雜的查詢簡化，並將它們拆解成較小的查詢。 另一個理想的做法是將大批的查詢拆解成較小批的查詢。 通常，緩和此效能問題的理想做法是為新查詢導入索引。

在 Azure SQL Database 中，請考慮使用 [查詢效能深入解析](query-performance-insight-use.md)。

## <a name="increased-wait-statistic"></a>增加的等候統計資料

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示與過去七天的工作負載基準相比，工作負載效能降低，其中發現有效能不佳的查詢。

在此情況中，系統無法將效能不佳的查詢歸類至任何其他標準的可偵測效能類別，但它偵測到等候統計資料是造成迴歸的原因。 因此，系統會將那些查詢判斷為具有「增加的等候統計資料」**，並同時公開造成迴歸的等候統計資料。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出有關增加之等候時間的詳細資料和受影響查詢之查詢雜湊的資訊。

由於系統無法順利識別出效能不佳查詢的根本原因，因此診斷資訊是進行手動疑難排解的理想起點。 您可以對這些查詢的效能進行最佳化。 理想的做法是僅擷取所需的資料，然後將複雜的查詢簡化並拆解成較小的查詢。

如需有關將查詢效能優化的詳細資訊，請參閱 [查詢微調](https://msdn.microsoft.com/library/ms176005.aspx)。

## <a name="tempdb-contention"></a>TempDB 爭用

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種資料庫效能情況，亦即嘗試存取 tempDB 資源的執行緒發生瓶頸。  (這種情況並不是 IO 相關。 ) 此效能問題的一般案例為數百個並行查詢，全都會建立、使用，然後卸載小型 tempDB 資料表。 系統已偵測到使用相同 tempDB 資料表的並行查詢數目就統計而言明顯增加，以致與過去七天效能基準相比，影響資料庫效能。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出 tempDB 爭用的詳細資料。 您可以使用此資訊作為疑難排解的起點。 若要減輕此競爭情形，並提升整體工作負載的輸送量，您可以嘗試兩種方法：您可以停止使用暫存資料表。 您也可以使用經記憶體最佳化的資料表。

如需詳細資訊，請參閱 [經記憶體優化的資料表簡介](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)。

## <a name="elastic-pool-dtu-shortage"></a>彈性集區 DTU 不足

### <a name="what-is-happening"></a>發生的情況

這個偵測的效能模式表示與過去七天的基準相比，目前的資料庫工作負載效能降低。 原因是訂用帳戶彈性集區中的可用 DTU 不足。

[Azure 彈性集區資源](elastic-pool-overview.md) 可用來當做多個資料庫之間共用的可用資源集區，以供調整之用。 當您彈性集區中的可用 eDTU 資源並未大到足以支援集區中的所有資料庫時，系統就會偵測到彈性集區 DTU 不足的效能問題。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出彈性集區的相關資訊、列出 DTU 取用量最高的資料庫，以及提供取用量最高之資料庫所使用的集區 DTU 百分比。

由於這個效能情況與使用彈性集區中相同 eDTU 集區的多個資料庫相關，因此疑難排解步驟會著重在 DTU 取用量最高的資料庫上。 您可以降低取用量最高之資料庫上的工作負載，包括將那些資料庫上取用量最高的查詢最佳化。 您也可以確定自己不會查詢不需要使用的資料。 另一個方法是將使用 DTU 取用量最高之資料庫的應用程式最佳化，然後將工作負載重新分散到多個資料庫。

如果您無法將 DTU 取用量最高之資料庫上的目前工作負載降低和最佳化，請考慮提升彈性集區定價層。 這個提升將可增加彈性集區中的可用的 DTU。

## <a name="plan-regression"></a>計畫迴歸

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示資料庫利用次佳查詢執行計畫的條件。 次佳的計畫通常會造成查詢的執行時間增加，這會導致目前及其他查詢的等候時間變長。

資料庫引擎會決定查詢執行成本最低的查詢執行計畫。 當查詢類型和工作負載變更時，有時現有的計畫已不再有效，或可能是資料庫引擎未進行良好的評量。 為了更正這種狀況，您可以手動強制執行查詢執行計畫。

這個可偵測的效能模式結合了三種不同的計畫迴歸案例：新計畫迴歸、舊計畫迴歸，以及現有計畫變更工作負載。 所發生之計畫迴歸的特定類型，會在診斷記錄的 [詳細資料]** 屬性中提供。

新的計畫回歸條件指的是資料庫引擎開始執行新查詢執行計畫的狀態，其效率不如舊計畫。 當資料庫引擎從使用新的、更有效率的計畫切換至舊計畫，而不像新的計畫一樣有效率時，舊的計畫回歸狀況就是指狀態。 現有計畫變更工作負載迴歸所指的狀態是新計畫和舊計畫會不斷交替，並逐漸朝向效能不佳的計畫方向發展。

如需有關計畫迴歸的詳細資訊，請參閱[什麼是 SQL Server 中的計畫迴歸？](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/)\(英文\)。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出查詢雜湊、良好計畫識別碼、不良計畫識別碼，以及查詢識別碼。 您可以使用此資訊作為疑難排解的基礎。

您可以分析對特定的查詢 (可以藉由系統提供的查詢雜湊來識別) 而言哪個計畫的效能較佳。 判斷出哪個計畫較適用於您的查詢之後，您即可手動強制執行該計畫。

如需詳細資訊，請參閱[了解 SQL Server 如何防止計畫迴歸](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/) \(英文\)。

> [!TIP]
> 您知道內建的智慧功能可以自動為資料庫管理效能最佳的查詢執行計畫嗎？
>
> 針對持續效能優化，建議您啟用 [自動調整](automatic-tuning-overview.md)。 此內建智慧功能會持續監視您的資料庫，並自動為您的資料庫調整及建立效能最佳的查詢執行計畫。

## <a name="database-scoped-configuration-value-change"></a>資料庫範圍組態值變更

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種情況，亦即資料庫範圍設定已發生變更，而此情況導致偵測到與過去七天的資料庫工作負載行為相比，效能呈現衰退。 此模式代表最近對資料庫範圍設定所做的變更似乎對資料庫效能沒有幫助。

您可以為每一個個別的資料庫設定資料庫範圍設定變更。 這個設定是依個案使用，以將您資料庫的個別效能最佳化。 以下是可針對每一個個別資料庫設定的選項：MAXDOP、LEGACY_CARDINALITY_ESTIMATION、PARAMETER_SNIFFING、QUERY_OPTIMIZER_HOTFIXES，以及 CLEAR PROCEDURE_CACHE。

### <a name="troubleshooting"></a>疑難排解

診斷記錄會輸出最近所進行且與過去七天的工作負載行為相比，導致效能降低的資料庫範圍設定變更。 您可以還原對先前值的設定變更。 您也可以逐值調整，直到取得所需的效能層級。 您可以從具有滿意效能的類似資料庫複製資料庫範圍設定值。 如果您無法針對效能進行疑難排解，請還原成預設值，然後嘗試從此基準開始進行微調。

如有關將資料庫範圍設定最佳化，以及變更設定之 T-SQL 語法的詳細資料，請參閱[變更資料庫範圍設定 (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx) \(機器翻譯\)。

## <a name="slow-client"></a>用戶端執行速度太慢

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示一種情況，亦即，使用資料庫的用戶端無法在資料庫傳送結果的情況下快速取用資料庫的輸出。 由於資料庫不會將所執行查詢的結果儲存在緩衝區中，因此它會減緩並等候用戶端取用傳送的查詢輸出，然後再繼續進行。 這種情況也可能與網路的速度不夠快，無法將資料庫的輸出傳輸到取用的用戶端。

只有當偵測到效能與過去七天的資料庫工作負載行為相比出現降低時，才會產生此情況。 只有在統計上與先前的效能行為相比出現明顯降低時才會偵測到此效能問題。

### <a name="troubleshooting"></a>疑難排解

這個可偵測的效能模式表示一種用戶端的情況。 需針對用戶端應用程式或用戶端網路進行疑難排解。 診斷記錄會輸出查詢雜湊，以及過去兩小時內等候用戶端取用它們等得最久的等候時間。 您可以使用此資訊作為疑難排解的基礎。

您可以將應用程式取用這些查詢的效能最佳化。 您也可以考慮可能的網路延遲問題。 因為效能降低問題是以過去七天的效能基準為基礎，您可以調查此效能降低事件是否是由最近的應用程式或網路狀況變更所造成。

## <a name="pricing-tier-downgrade"></a>定價層降級

### <a name="what-is-happening"></a>發生的情況

這個可偵測的效能模式表示資料庫訂用帳戶的定價層降級的條件。 由於資料庫可用的資源 (DTU) 減少，因此系統偵測到目前的資料庫與過去七天的基準相比出現效能降低。

此外，您的資料庫訂用帳戶的定價層可能會在短時間內降級，然後升級至較高的層級。 對此暫時性效能降低的偵測，會在診斷記錄的 [詳細資料] 區段中會輸出成定價層降級和升級。

### <a name="troubleshooting"></a>疑難排解

如果您已降低定價層，因此可供使用，而且您對效能感到滿意，就不需要執行任何動作。 如果您降低定價層，且您不滿意資料庫效能，請減少資料庫工作負載，或考慮將定價層提升到較高的層級。

## <a name="recommended-troubleshooting-flow"></a>建議的疑難排解流程

 請依照下方流程圖所提供的建議方法，使用 Intelligent Insights 來針對效能問題進行疑難排解。

透過 Azure 入口網站瀏覽至 [Azure SQL 分析] 來存取 Intelligent Insights。 嘗試找出傳入的效能警示，然後選取該警示。 在 [偵測] 頁面上識別發生的情況。 觀察所提供的問題根本原因分析、查詢文字、查詢時間趨勢，以及事件演進情況。 使用 Intelligent Insights 針對緩和效能問題所提出的建議來嘗試解決該問題。

[![疑難排解流程圖](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> 選取流程圖以下載 PDF 版本。

Intelligent Insights 通常需要一小時的時間來執行效能問題的根本原因分析。 如果無法在 Intelligent Insights 中找到您的問題，且此問題對您而言很嚴重，請使用查詢存放區以手動識別效能問題的根本原因。  (，這些問題通常不到一小時的時間。 ) 如需詳細資訊，請參閱 [使用查詢存放區監視效能](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。

## <a name="next-steps"></a>後續步驟

- 瞭解 [Intelligent Insights](intelligent-insights-overview.md) 概念。
- 使用 [Intelligent Insights 效能診斷記錄](intelligent-insights-use-diagnostics-log.md)。
- 使用 [Azure SQL 分析](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)進行監視。
- 了解如何[收集並取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/platform-logs-overview.md)。
