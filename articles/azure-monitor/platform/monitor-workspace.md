---
title: 在 Azure 監視器中監視 Log Analytics 工作區的健康情況
description: 說明如何使用作業資料表中的資料來監視 Log Analytics 工作區的健康情況。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 9a70dcbabea9bc55703a5e9875df05b534eb372a
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674744"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>在 Azure 監視器中監視 Log Analytics 工作區的健康情況
若要在 Azure 監視器中維護 Log Analytics 工作區的效能和可用性，您必須能夠主動偵測出發生的任何問題。 本文說明如何使用 [作業資料表中的資料](https://docs.microsoft.com/azure/azure-monitor/reference/tables/operation) 來監視 Log Analytics 工作區的健康情況。 此資料表包含在每個 Log Analytics 工作區中，並包含在工作區中發生的錯誤和警告。 您應定期檢查此資料並建立警示，以在您的工作區中有任何重要事件時主動收到通知。

## <a name="_logoperation-function"></a>_LogOperation 函式

Azure 監視器記錄會將任何問題的詳細資料傳送至發生問題之工作區中的[作業資料表。](https://docs.microsoft.com/azure/azure-monitor/reference/tables/operation) **_LogOperation** 系統函數是以 **作業資料表為** 基礎，並提供一組簡化的資訊供分析和警示之用。

## <a name="columns"></a>資料行

**_LogOperation** 函數會傳回下表中的資料行。

| 資料行 | 描述 |
|:---|:---|
| TimeGenerated | 事件發生的時間（UTC）。 |
| 類別  | 作業類別目錄群組。 可以用來篩選作業的類型，並協助建立更精確的系統審核和警示。 請參閱下一節以取得類別清單。 |
| 作業  | 作業類型的描述。 這可能表示其中一個 Log Analytics 限制、作業類型或部分處理常式。 |
| 層級 | 問題的嚴重性層級：<br>-Info：不需要特別注意。<br>-警告：進程未如預期般完成，而且需要注意。<br>-錯誤：進程失敗，而且需要緊急注意。 
| 詳細資料 | 作業的詳細描述包含特定的錯誤訊息（如果有的話）。 |
| _ResourceId | 與作業相關之 Azure 資源的資源識別碼。  |
| 電腦 | 如果作業與 Azure 監視器代理程式相關，則為電腦名稱稱。 |
| CorrelationId | 用來分組連續相關的作業。 |


## <a name="categories"></a>類別

下表說明 _LogOperation 函式中的分類。 

| 類別 | 描述 |
|:---|:---|
| 擷取           | 屬於資料內嵌進程的作業。 詳細資訊請見下文。 |
| 代理程式               | 表示代理程式安裝發生問題。 |
| 資料集合     | 與資料集合進程相關的作業。 |
| 解決方案目標  | *ConfigurationScope* 類型的作業已處理。 |
| 評量解決方案 | 已執行評量程式。 |


### <a name="ingestion"></a>擷取
內嵌作業是資料內嵌期間發生的問題，包括有關達到 Azure Log Analytics 工作區限制的通知。 此類別中的錯誤狀況可能會建議資料遺失，因此對監視來說特別重要。 下表提供這些作業的詳細資料。 請參閱 Log Analytics 工作區服務限制 [Azure 監視器服務限制](../service-limits.md#log-analytics-workspaces) 。


| 作業 | 層級 | 詳細資料 | 相關文章 |
|:---|:---|:---|:---|
| 自訂記錄檔 | Error   | 已達到自訂欄位的資料行限制。 | [Azure 監視器服務限制](../service-limits.md#log-analytics-workspaces) |
| 自訂記錄檔 | Error   | 自訂記錄內嵌失敗。 | |
| 中繼資料。 | Error | 偵測到設定錯誤。 | |
| 資料集合 | Error   | 因為建立的要求早于設定的天數，所以已卸載資料。 | [使用 Azure 監視器記錄來管理使用量和成本](manage-cost-storage.md#alert-when-daily-cap-reached)
| 資料集合 | Info    | 偵測到收集電腦設定。| |
| 資料集合 | Info    | 因為新的日期，所以已開始資料收集。 | [使用 Azure 監視器記錄來管理使用量和成本](/manage-cost-storage.md#alert-when-daily-cap-reached) |
| 資料集合 | 警告 | 因為達到每日限制，所以資料收集已停止。| [使用 Azure 監視器記錄來管理使用量和成本](/manage-cost-storage.md#alert-when-daily-cap-reached) |
| 資料處理 | Error   | 不正確 JSON 格式。 | [使用 HTTP 資料收集器 API 將記錄資料傳送給 Azure 監視器 (公開預覽)](data-collector-api.md#request-body) | 
| 資料處理 | 警告 | 值已修剪為允許的最大大小。 | [Azure 監視器服務限制](../service-limits.md#log-analytics-workspaces) |
| 資料處理 | 警告 | 已達到大小限制而修剪的域值。 | [Azure 監視器服務限制](../service-limits.md#log-analytics-workspaces) | 
| 內嵌速率 | Info | 內嵌速率限制接近70%。 | [Azure 監視器服務限制](../service-limits.md#log-analytics-workspaces) |
| 內嵌速率 | 警告 | 接近限制的內嵌速率限制。 | [Azure 監視器服務限制](../service-limits.md#log-analytics-workspaces) |
| 內嵌速率 | Error   | 達到速率限制。 | [Azure 監視器服務限制](../service-limits.md#log-analytics-workspaces) |
| 儲存體 | Error   | 因為使用的認證無效，所以無法存取儲存體帳戶。  |



   

## <a name="alert-rules"></a>警示規則
當您的 Log Analytics 工作區中偵測到問題時，請使用 Azure 監視器中的 [記錄查詢警示](../platform/alerts-log-query.md) 來主動收到通知。 您應使用可讓您及時回應問題的策略，同時將成本降至最低。 您的訂用帳戶會以每個警示規則的費用計費，其成本取決於其評估的頻率。

建議的策略是根據問題層級的兩個警示規則來開始。 使用短暫的頻率，例如每5分鐘的錯誤和較長的頻率，例如24小時的警告。 由於錯誤指出可能會遺失資料，因此您想要快速回應這些錯誤，以將遺失的情況降到最低。 警告通常表示不需要立即注意的問題，因此您可以每日進行審查。

使用 [Azure 監視器建立、查看和記錄管理警示](../platform/alerts-log.md) 中的程式，建立記錄警示規則。 下列各節說明每個規則的詳細資料。


| 查詢 | 臨界值 | 期間 | 頻率 |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

這些警示規則會回應相同的所有作業，並出現錯誤或警告。 當您更熟悉產生警示的作業時，您可能會想要以不同的方式回應特定的作業。 例如，您可能會想要將通知傳送給不同的人員以進行特定的作業。 

若要建立特定作業的警示規則，請使用包含 **Category** 和 **operation** 資料行的查詢。 

下列範例會在內嵌磁片區速率達到限制的80% 時建立警告警示。

- 目標：選取您的 Log Analytics 工作區
- 準則：
  - 訊號名稱：自訂記錄搜尋
  - 搜尋查詢： `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - 依據：結果數目
  - 條件：大於
  - 閾值：0
  - 期間：5 (分鐘)
  - 頻率：5 (分鐘)
- 警示規則名稱：已達到每日資料限制
- 嚴重性：警告 (嚴重性 1)


當資料收集達到每日限制時，下列範例會建立警告警示。 

- 目標：選取您的 Log Analytics 工作區
- 準則：
  - 訊號名稱：自訂記錄搜尋
  - 搜尋查詢： `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - 依據：結果數目
  - 條件：大於
  - 閾值：0
  - 期間：5 (分鐘)
  - 頻率：5 (分鐘)
- 警示規則名稱：已達到每日資料限制
- 嚴重性：警告 (嚴重性 1)



## <a name="next-steps"></a>後續步驟

- 深入瞭解 [記錄警示](alerts-log.md)。
- 收集工作區的[查詢審核資料](../log-query/query-audit.md)。
