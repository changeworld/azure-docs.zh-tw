---
title: 針對 Azure 監視器中的記錄警示進行疑難排解 |Microsoft Docs
description: Azure 中記錄警示規則的常見問題、錯誤和解決方式。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: e9a1bef582053eccdbfef63c2159cf540ffd9bfb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186587"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>針對 Azure 監視器中的記錄警示進行疑難排解  

本文說明如何解決 Azure 監視器中的記錄警示常見問題。 它也提供記錄警示功能和設定的常見問題解決方案。

記錄警示可讓使用者使用 [Log Analytics](../log-query/log-analytics-tutorial.md) 查詢來評估每個設定頻率的資源記錄，並根據結果引發警示。 規則可以使用 [動作群組](./action-groups.md)觸發一或多個動作。 [深入瞭解記錄警示的功能和術語](alerts-unified-log.md)。

> [!NOTE]
> 本文並不考慮 Azure 入口網站顯示已觸發的警示規則，而且相關聯的動作群組不會執行通知的情況。 在這種情況下，請參閱 [此處](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)的疑難排解詳細資料。

## <a name="log-alert-didnt-fire"></a>記錄警示未引發

### <a name="data-ingestion-time-for-logs"></a>記錄的資料內嵌時間

Azure 監視器處理來自世界各地的數 tb 客戶記錄，這可能會導致 [記錄內嵌延遲](./data-ingestion-time.md)。

記錄是半結構化資料，本質上比度量更高。 如果您在引發的警示中遇到超過4分鐘的延遲，您應該考慮使用計量 [警示](alerts-metric-overview.md)。 您可以使用 [記錄的計量警示](alerts-metric-logs.md)，將資料從記錄傳送至計量存放區。

系統會多次重試警示評估，以降低延遲。 當資料抵達時，就會引發警示，在大部分情況下不會等於記錄檔記錄時間。

### <a name="incorrect-query-time-range-configured"></a>設定的查詢時間範圍不正確

在規則條件定義中設定查詢時間範圍。 此欄位稱為工作區和 Application Insights 的 **期間** ，並且針對所有其他資源類型呼叫覆 **寫查詢時間範圍** 。 如同在 log analytics 中，時間範圍會將查詢資料限制在指定的期間內。 即使在查詢中使用 **前** 一個命令，也會套用時間範圍。 

例如，查詢會掃描60分鐘，如果時間範圍是60分鐘，即使文字包含 **前 (1d)**。 時間範圍和查詢時間篩選需要相符。 在範例案例中，將 **週期** 覆  /  **寫查詢時間範圍** 變更為一天，會如預期般運作。

![時間週期](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>警示規則中的動作已靜音

記錄警示提供選項，可讓您在一段時間內將引發的警示動作靜音。 此欄位稱為隱藏工作區和 Application Insights 中的 **警示** 。 在所有其他資源類型中，它稱為「 **靜音動作**」。 

常見的問題是，您認為警示因為服務問題而未引發動作。 更難使用規則設定將它靜音。

![隱藏警示](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>使用舊版 Log Analytics API 進行分割的計量測量警示規則

[計量測量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 是以摘要時間序列結果為基礎的一種記錄警示。 這些規則允許依資料行分組來 [分割警示](alerts-unified-log.md#split-by-alert-dimensions)。 如果您使用舊版 Log Analytics API，則分割將無法如預期般運作。 不支援選擇舊版 API 中的群組。

目前的 ScheduledQueryRules API 可讓您在 [計量測量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)規則中設定 **匯總**，這會如預期般運作。 [深入瞭解如何切換至目前的 SCHEDULEDQUERYRULES API](alerts-log-api-switch.md)。

## <a name="log-alert-fired-unnecessarily"></a>記錄警示被不必要地引發

[Azure 監視器中設定的記錄警示規則](./alerts-log.md)可能會非預期地觸發。 下列各節將說明一些常見的原因。

### <a name="alert-triggered-by-partial-data"></a>警示被部分資料觸發

Azure 監視器處理來自世界各地的數 tb 客戶記錄，這可能會導致 [記錄內嵌延遲](./data-ingestion-time.md)。

記錄是半結構化資料，本質上比度量更高。 如果您在引發的警示中遇到許多 misfires，您應該考慮使用計量 [警示](alerts-metric-overview.md)。 您可以使用 [記錄的計量警示](alerts-metric-logs.md)，將資料從記錄傳送至計量存放區。

當您嘗試在記錄檔中偵測到資料時，記錄警示的效果最佳。 當您嘗試在記錄檔中偵測缺少的資料時，它的運作效果比較低。 例如，警示虛擬機器的信號。 

雖然有內建功能可以防止誤報警示，但仍可能發生在大約30分鐘的資料 (，) 和延遲尖峰的資料。

### <a name="query-optimization-issues"></a>查詢優化問題

警示服務會變更您的查詢，以針對較低的負載和警示延遲進行優化。 警示流程的建立是為了將表示問題的結果轉換成警示。 例如，在類似以下的查詢案例中：

``` Kusto
SecurityEvent
| where EventID == 4624
```

如果使用者的意圖是警示，當發生此事件種類時，警示邏輯會附加 `count` 至查詢。 將執行的查詢將會是：

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

不需要將警示邏輯新增到查詢中，這樣做可能甚至會造成問題。 在上述範例中，如果您將包含 `count` 在查詢中，它一律會產生值1，因為警示服務將會完成 `count` `count` 。

優化查詢是記錄警示服務執行的結果。 您可以在 Log Analytics [入口網站](../log-query/log-query-overview.md) 或 [API](/rest/api/loganalytics/)中執行修改過的查詢。

針對工作區和 Application Insights，會在 [條件] 窗格中呼叫 **要執行的查詢** 。 在 [其他所有資源類型] 中，選取 [條件] 索引標籤中的 [ **查看最終警示查詢** ]。

![要執行的查詢](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>記錄警示已停用

下列各節列出 Azure 監視器可能會停用記錄警示規則的一些原因。 此外，我們也包含 [停用規則時所傳送的活動記錄範例](#activity-log-example-when-rule-is-disabled)。

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>警示範圍不再存在或已移動

當警示規則的範圍資源不再有效時，規則的執行將會失敗。 在此情況下，計費也會停止。

如果連續失敗，Azure 監視器將會在一周後停用記錄警示。

### <a name="query-used-in-a-log-alert-isnt-valid"></a>記錄警示中使用的查詢無效

建立記錄警示規則時，會驗證查詢的語法是否正確。 但有時候，記錄警示規則中提供的查詢可能會開始失敗。 常見的原因包括：

- 規則是透過 API 建立的，且使用者已略過驗證。
- 查詢會 [在多個資源上執行](../log-query/cross-workspace-query.md) ，而且已刪除或移動一或多個資源。
- [查詢失敗](https://dev.loganalytics.io/documentation/Using-the-API/Errors)，因為：
    - 記錄解決方案未 [部署至工作區](../insights/solutions.md#install-a-monitoring-solution)，因此不會建立資料表。
    - 在過去30天內，資料已停止流入查詢中的資料表。
    - 尚未建立[自訂記錄資料表](data-sources-custom-logs.md)，因為資料流程尚未啟動。
- [查詢語言](/azure/kusto/query/)的變更包含命令和函式的修訂格式。 因此先前提供的查詢已不再有效。

[Azure Advisor](../../advisor/advisor-overview.md) 警告您此行為。 它會新增受影響記錄警示規則的建議。 使用的類別是具有中度影響的「高可用性」，以及「修復您的記錄警示規則以確保監視」的說明。

## <a name="alert-rule-quota-was-reached"></a>已達到警示規則配額

每個訂用帳戶和資源的記錄搜尋警示規則數受限於[這裡](../service-limits.md)所述的配額限制。

### <a name="recommended-steps"></a>建議的步驟
    
如果您已達到配額限制，下列步驟可能有助於解決此問題。

1. 請嘗試刪除或停用不再使用的記錄搜尋警示規則。
1. 嘗試 [依維度使用警示分割](alerts-unified-log.md#split-by-alert-dimensions) ，以減少規則計數。 這些規則可以監視許多資源和偵測案例。
1. 如果您需要增加配額限制，請繼續開啟支援要求，並提供下列資訊：

    - 需要增加配額限制的訂用帳戶識別碼和資源識別碼。
    - 增加配額的原因。
    - 配額增加的資源類型： **Log Analytics**、 **Application Insights** 等等。
    - 要求的配額限制。


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>若要檢查新記錄警示規則的目前使用量
    
#### <a name="from-the-azure-portal"></a>從 Azure 入口網站

1. 開啟 [*警示*] 畫面，然後選取 [*管理警示規則*]。
2. 使用 [訂用帳戶] 下拉式控制項來篩選相關的訂用帳戶
3. 請確定不要篩選至特定資源群組、資源類型或資源
4. 在 [訊號類型] 下拉式控制項中，選取 [記錄搜尋]
5. 確認 [狀態] 下拉式控制項已設定為 [已啟用]
6. 記錄搜尋警示規則的總數會顯示在規則清單上方

#### <a name="from-api"></a>從 API

- PowerShell- [取得-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [依訂用帳戶列出](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>停用規則時的活動記錄範例

如果查詢連續七天失敗，Azure 監視器將會停用記錄警示，並停止規則的計費。 您可以在 [Azure 活動記錄](../../azure-resource-manager/management/view-activity-logs.md)中找出 Azure 監視器停用記錄警示的確切時間。 請參閱此範例：

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 中的記錄警示](./alerts-unified-log.md)。
- 深入瞭解如何設定 [記錄警示](../log-query/log-query-overview.md)。
- 深入瞭解 [記錄查詢](../log-query/log-query-overview.md)。