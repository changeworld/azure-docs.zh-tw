---
title: Azure 監視器中的記錄資料擷取時間 | Microsoft Docs
description: 說明會影響 Azure 監視器收集記錄資料延遲度的不同因素。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 99d5594dd3ebe3750cb0a09ea803065e2aeb5ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666632"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Azure 監視器中的記錄資料擷取時間
Azure 監視器是一種大規模的資料服務，服務對象為每月需傳送數 TB 資料 (且不斷成長) 的上千名客戶。 而在收集記錄資料後，資料需要多久時間方能轉為可用狀態，是經常受到詢問的問題。 本文會說明影響這種延遲的不同因素。

## <a name="typical-latency"></a>一般延遲
延遲是指在受監控的系統中建立資料所需的時間，以及轉變為可在 Azure 監視器中進行分析的時間。 內嵌記錄資料的延遲通常介於 2 到 5 分鐘之間。 任何特定資料的某種延遲會因為下列所述的各種因素而有所不同。


## <a name="factors-affecting-latency"></a>影響延遲的因素
特定資料集的擷取時間總計可以細分成下列高階領域。 

- 代理程式時間：探索事件、收集事件，然後將其傳送至 Azure 監視器擷取點作為記錄檔記錄所需的時間。 在大多數情況下，此流程是由代理程式處理。
- 管線時間：擷取管線來處理記錄檔的時間。 包括剖析事件的屬性，以及可能新增計算的資訊。
- 編列索引的時間：將記錄檔記錄擷取至 Azure 監視器巨量資料存放區所花費的時間。

以下說明出現在該流程中不同延遲的詳細資料。

### <a name="agent-collection-latency"></a>代理程式收集延遲
代理程式和管理解決方案使用不同的策略來收集虛擬機器的資料，這可能會影響延遲。 某些特定範例包括以下內容：

- 立即收集 Windows 事件、syslog 事件和效能計量。 Linux 效能計數器每隔 30 秒輪詢一次。
- 時間戳記變更後，會收集 IIS 記錄和自訂記錄。 而 IIS 記錄會因[變換在 IIS 上設定的排程](data-sources-iis-logs.md)而受影響。 
- Active Directory 複寫解決方案每五天執行一次評估，而 Active Directory 評定解決方案每週對 Active Directory 基礎結構執行一次評估。 只有在評估完成後，代理程式才會收集這些記錄。

### <a name="agent-upload-frequency"></a>代理程式上傳頻率
為確保 Log Analytics 代理程式是輕量的，代理程式會緩衝記錄，並定期將其上傳至 Azure 監視器。 上傳頻率在 30 秒到 2 分鐘之間，視資料類型而定。 大部分的資料會在 1 分鐘內上傳。 網路狀況可能會對此資料的延遲產生負面影響，而難以達到 Azure 監視器擷取點。

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Azure 活動日誌、資源日誌和指標
為了在 Log Analytics 內嵌點變成可供處理，Azure 資料會多花費一些時間：

- 資源日誌中的資料需要 2-15 分鐘，具體取決於 Azure 服務。 若要檢查您環境中的這項延遲，請參閱[下方的查詢](#checking-ingestion-time)
- Azure 平台計量需要 3 分鐘的時間，才能傳送到 Log Analytics 內嵌點。
- Azure 記錄資料將需要 10-15 分鐘的時間，才能傳送到 Log Analytics 內嵌點。

資料在內嵌點變成可供使用之後，會再花費 2-5 分鐘的時間，才可供查詢。

### <a name="management-solutions-collection"></a>管理解決方案集合
某些解決方案不會從代理程式收集其資料，而且可能使用會造成額外延遲的收集方法。 某些解決方案會定期收集資料，而不嘗試近乎即時的收集。 特定範例包括以下內容：

- Office 365 解決方案使用 Office 365 管理活動 API 輪詢活動記錄，該 API 目前不提供任何近乎即時的延遲保證。
- 解決方案以每日頻率收集 Windows Analytics 解決方案 (例如，更新合規性) 資料。

請參閱每個解決方案的文件以確定其收集頻率。

### <a name="pipeline-process-time"></a>管理處理程序時間
將日誌記錄引入 Azure 監視器管道（如[_TimeReceived](log-standard-properties.md#_timereceived)屬性中標識）後，這些記錄將寫入臨時存儲，以確保租戶隔離並確保資料不會丟失。 此程序通常會增加 5-15 秒。 某些管理解決方案會實作更繁重的演算法以彙總資料，並在資料流入時獲得見解。 例如，網路效能監控會每 3 分鐘彙總傳入資料，有效地增加 3 分鐘的延遲。 另一個會增加延遲的程序是處理自訂記錄的程序。 在某些情況下，對於代理程式收集自檔案的記錄，此程序可能會增加數分鐘的延遲。

### <a name="new-custom-data-types-provisioning"></a>新的自訂資料類型佈建
從[自訂記錄](data-sources-custom-logs.md)或[資料收集器 API](data-collector-api.md) 建立新類型的自訂資料時，系統會建立專用的儲存體容器。 這是一次性的額外負荷，僅在第一次出現此資料類型時發生。

### <a name="surge-protection"></a>突波保護
Azure 監視器的首要任務是確保不會遺失客戶資料，因此系統具有內建的資料突波保護。 這包括緩衝區，以確保即使在巨大的負載下，系統會保持正常運作。 在正常負載下，這些控制項增加不到一分鐘，但是在極端的條件下和故障時，它們可能會增加大量時間，同時確保資料安全。

### <a name="indexing-time"></a>建立索引的時間
每個巨量資料平台在提供分析和進階搜尋功能之間，會達到內建的平衡，而不是提供對資料的即時存取權。 Azure 監視器可讓您對數百萬筆記錄執行強大的查詢，並在幾秒鐘內獲得結果。 這是可能的，因為基礎結構在擷取過程中會大幅轉換資料，並將其儲存在唯一的壓縮結構中。 系統會緩衝資料，直到有足夠的資料可用於建立這些結構。 必須先完成此操作，記錄檔才會出現在搜尋結果中。

當資料量較少，但高資料傳輸率縮短時間時，此程序目前大約需要 5 分鐘。 這似乎違反直覺，但此流程能讓量生產工作量的延遲達到最佳化。



## <a name="checking-ingestion-time"></a>檢查擷取時間
對於不同的資源，在不同的情況下，擷取時間可能不盡相同。 您可以使用記錄查詢來識別您環境的特定行為。 下表指定如何確定記錄在創建併發送到 Azure 監視器時的不同時間。

| 步驟 | 屬性或功能 | 註解 |
|:---|:---|:---|
| 在資料來源創建的記錄 | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>如果資料來源未設置此值，則它將設置為與_TimeReceived相同的時間。 |
| Azure 監視器引入終結點接收的記錄 | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| 存儲在工作區中並可用於查詢的記錄 | [ingestion_time（）](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>擷取延遲
通過將[ingestion_time（）](/azure/kusto/query/ingestiontimefunction)函數的結果與_TimeResult_屬性進行比較，可以測量特定記錄的延遲。 這項資料可以搭配各種彙總，用來了解延遲的運作方式。 檢查擷取時間的一些百分位數，取得大量資料的深入解析。 

例如，以下查詢將顯示哪些電腦在前 8 小時內的引入時間最高： 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

前面的百分位數檢查有利於查找延遲的一般趨勢。 要識別延遲的短期峰值，使用最大 （`max()`） 可能更有效。

如果要在一段時間內向下切入特定電腦的引入時間，請使用以下查詢，該查詢還會在圖形中視覺化過去一天的資料： 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
使用以下查詢按其所在的 IP 位址顯示電腦攝取時間： 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
來自代理程式的不同資料類型可能有不同的擷取延遲時間，因此先前的查詢無法搭配其他類型使用。 下列查詢可以用來檢查各種 Azure 服務的擷取時間： 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>停止回應的資源 
在某些情況下，資源將會停止傳送資料。 若要了解資源是否正在傳送資料，請查看可以由標準 [TimeGenerated]__ 欄位識別的最新記錄。  

「活動訊號」__ 資料表可以用來檢查 VM 的可用性，因為代理程式活動訊號會每分鐘傳送一次。 可用下列查詢列出最近尚未回報活動訊號的使用中電腦： 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>後續步驟
* 請閱讀 Azure 監視器的[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/)。

