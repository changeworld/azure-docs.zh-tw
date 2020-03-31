---
title: 在 Azure 監視器中排除日誌警報的故障 |微軟文檔
description: Azure 中日誌警報規則的常見問題、錯誤和解決方法。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249031"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>在 Azure 監視器中排除日誌警報的疑難排解  

本文介紹如何解決在 Azure 監視器中設置日誌警報時可能發生的常見問題。 它還為日誌警報的功能或配置的常見問題提供了解決方案。

術語*日誌警報*描述基於[Azure 日誌分析工作區](../learn/tutorial-viewdata.md)中的日誌查詢或在[Azure 應用程式見解](../../azure-monitor/app/analytics.md)中觸發的規則。 在[Azure 監視器 中的日誌警報](../platform/alerts-unified-log.md)中瞭解有關功能、術語和類型的更多資訊。

> [!NOTE]
> 本文不考慮 Azure 門戶顯示觸發的警報規則且不由關聯的操作組執行通知的情況。 有關此類情況，請參閱 Azure[門戶中創建和管理操作組中](../platform/action-groups.md)的詳細資訊。

## <a name="log-alert-didnt-fire"></a>記錄警示未引發

以下是[Azure 監視器中配置的日誌警報規則](../platform/alerts-log.md)的狀態未[按預期*顯示*的](../platform/alerts-managing-alert-states.md)一些常見原因。

### <a name="data-ingestion-time-for-logs"></a>日誌的資料引入時間

日誌警報會根據[日誌分析](../learn/tutorial-viewdata.md)或[應用程式見解](../../azure-monitor/app/analytics.md)定期執行查詢。 由於 Azure 監視器處理來自世界各地不同來源的數千個客戶的數 TB 資料，因此該服務容易受到不同的時間延遲的影響。 有關詳細資訊，請參閱[Azure 監視器日誌中的資料引入時間](../platform/data-ingestion-time.md)。

為了緩解延遲，如果系統發現所需資料尚未被引入，系統會多次等待並重試警報查詢。 系統已設定以指數方式增加等候時間。 日誌警報僅在資料可用後觸發，因此延遲可能是由於日誌資料的引入速度緩慢。

### <a name="incorrect-time-period-configured"></a>設定的時間週期不正確

如日誌[警報術語](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)一文所述，配置中所述的時間段指定查詢的時間範圍。 查詢僅返回在此範圍內創建的記錄。

時間段限制日誌查詢獲取的資料以防止濫用，並規避日誌查詢中使用的任何時間命令（如**前）。** 例如，如果時間週期設定為 60 分鐘，且查詢會在下午 1:15 執行，則只有在下午 12:15 與下午 1:15 之間所建立的記錄會用於記錄查詢。 如果日誌查詢使用時間命令（如**前一時間命令 （1d），** 則查詢仍僅使用 12：15 PM 和 1：15 PM 之間的資料，因為時間段設置為該間隔。

檢查配置中的時間段是否與查詢匹配。 對於前面顯示的示例，如果日誌查詢使用前 **（1d）** 與綠色標記，時間段應設置為 24 小時或 1，440 分鐘（以紅色表示）。 此設置可確保查詢按預期運行。

![時間週期](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>已設定隱藏警示選項

如在[Azure 門戶中創建日誌警報規則](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)的文章步驟 8 中所述，日誌警報提供 **"禁止警報"** 選項，用於在配置的時間內禁止觸發和通知操作。 因此，您可能會認為警報沒有觸發。 事實上，它確實開火，但被壓制了。  

![隱藏警示](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>公制度量單位警示規則不正確

*指標測量日誌警報*是具有特殊功能和受限警報查詢語法的日誌警報的子類型。 指標度量日誌警報的規則要求查詢輸出為指標時間序列。 也就是說，輸出是一個具有不同、大小相等的時間段以及相應的聚合值的表。

您可以選擇在表中具有其他變數以及**聚合值**。 這些變數可用於對表進行排序。

例如，假設指標度量日誌警報的規則配置為：

- 查詢`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 小時時間段
- 閾值為 50
- 連續三次違規的警報邏輯
- **選擇$table時聚合**** **

因為該命令包括**摘要...和**提供兩個變數（**時間戳記**和 **$table），** 系統選擇 **$table**在**聚合時**。 系統按 **$table**欄位對結果表進行排序，如下圖所示。 然後，它查看每個表類型的多個**聚合值**實例（如**可用性結果**），以查看是否有三個或多個連續違規。

![具有多個值的度量度量查詢執行](media/alert-log-troubleshoot/LogMMQuery.png)

由於 **"聚合上"** 在 **$table**上定義，因此資料在 **$table**列（以紅色表示）排序。 然後，我們分組並查找**聚合"上"** 欄位的類型。

例如，對於 **$table**，可用性值**結果**將被視為一個繪圖/實體（以橙色表示）。 在此值圖/實體中，警報服務檢查連續三次違規（以綠色表示）。 這些違規事件觸發表值**可用性的警報結果**。

同樣，如果發生三次連續$table值的連續**違規，則**為同一事件觸發另一個警報通知。 警報服務按時間自動對一個繪圖/實體中的值進行排序（以橙色表示）。

現在假設指標度量日誌警報的規則已修改，查詢為`search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`。 其餘配置與以前相同，包括連續三次違規的警報邏輯。 在這種情況下，"**聚合時間"** 選項預設為**時間戳記**。 查詢中僅提供一個值，用於**匯總...（** 即**時間戳記**）。 與前面的示例一樣，執行結束時的輸出如下說明。

   ![具有奇異值的度量度量查詢執行](media/alert-log-troubleshoot/LogMMtimestamp.png)

由於 **"聚合時間"** 是在**時間戳記**上定義的，因此資料在**時間戳記**列上排序（以紅色表示）。 然後我們按**時間戳記**分組。 例如，的值`2018-10-17T06:00:00Z`將被視為一個繪圖/實體（以橙色表示）。 在此值圖/實體中，警報服務將找不到連續的違規（因為每個**時間戳記**值只有一個條目）。 因此，從不觸發警報。 在這種情況下，使用者必須：

- 添加虛擬變數或現有變數（如 **$table），** 以便使用 **"聚合上"** 欄位正確排序。
- 重新配置警報規則，以便根據**完全違反**行為使用警報邏輯。

## <a name="log-alert-fired-unnecessarily"></a>記錄警示被不必要地引發

在[Azure 警報](../platform/alerts-managing-alert-states.md)中查看[Azure 監視器中配置的日誌警報規則](../platform/alerts-log.md)時可能會意外觸發。 以下各節介紹一些常見原因。

### <a name="alert-triggered-by-partial-data"></a>警示被部分資料觸發

日誌分析和應用程式見解可能會受到引入延遲和處理。 運行日誌警報查詢時，您可能會發現沒有可用的資料或只有某些資料可用。 有關詳細資訊，請參閱[Azure 監視器 中的日誌資料引入時間](../platform/data-ingestion-time.md)。

根據配置警報規則的方式，如果在警報執行時日誌中沒有資料或部分資料，則可能發生誤燒。 在這種情況下，我們建議您更改警報查詢或配置。

例如，如果將日誌警報規則配置為在分析查詢的結果數小於 5 時觸發，則當沒有資料（零記錄）或部分結果（一條記錄）時觸發警報。 但在資料引入延遲之後，包含完整資料的相同查詢可能會提供 10 條記錄的結果。

### <a name="alert-query-output-is-misunderstood"></a>警報查詢輸出被誤解

您在分析查詢中提供記錄警示的邏輯。 分析查詢可以使用各種大資料和數學函數。 警報服務按指定時間段內的資料指定的時間間隔執行查詢。 警報服務根據警報類型對查詢進行細微更改。 您可以在 **"配置信號邏輯**"螢幕上的 **"要執行的查詢**"部分中查看此更改：

![要執行的查詢](media/alert-log-troubleshoot/LogAlertPreview.png)

**要執行的查詢**框是日誌警報服務運行的內容。 如果要在創建警報之前瞭解警報查詢輸出可能是什麼，可以通過[分析門戶](../log-query/portals.md)或[分析 API](https://docs.microsoft.com/rest/api/loganalytics/)運行所述查詢和時間跨度。

## <a name="log-alert-was-disabled"></a>日誌警報已禁用

以下各節列出了 Azure 監視器可能禁用[日誌警報規則的](../platform/alerts-log.md)一些原因。

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>創建警報的資源不再存在

在 Azure 監視器中創建的日誌警報規則以特定資源為目標，如 Azure 日誌分析工作區、Azure 應用程式見解應用和 Azure 資源。 然後，日誌警報服務將運行規則中為指定目標提供的分析查詢。 但是，在創建規則後，使用者通常會繼續從 Azure 中刪除日誌警報規則的目標，或在 Azure 中移動。 由於警報規則的目標不再有效，因此執行規則失敗。

在這種情況下，Azure 監視器禁用日誌警報，並確保當規則無法持續運行一段時間（如一周）時，不會不必要地計費。 您可以找出 Azure 監視器通過[Azure 活動日誌](../../azure-resource-manager/management/view-activity-logs.md)禁用日誌警報的確切時間。 在 Azure 活動日誌中，當 Azure 監視器禁用日誌警報規則時，將添加事件。

Azure 活動日誌中的以下示例事件適用于由於持續故障而已禁用的警報規則。

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>日誌警報中使用的查詢無效

作為配置的一部分在 Azure 監視器中創建的每個日誌警報規則都必須指定警報服務將定期運行的分析查詢。 在創建或更新規則時，分析查詢可能具有正確的語法。 但有時，在一段時間內，日誌警報規則中提供的查詢可能會開發語法問題並導致規則執行失敗。 日誌警報規則中提供的分析查詢可能出現錯誤的一些常見原因是：

- 編寫查詢以[跨多個資源運行](../log-query/cross-workspace-query.md)。 一個或多個指定資源不再存在。
- [配置的指標度量類型日誌警報](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)具有不符合語法規范的警報查詢
- 沒有資料流程向分析平臺。 [查詢執行會提供錯誤，](https://dev.loganalytics.io/documentation/Using-the-API/Errors)因為提供的查詢沒有資料。
- [查詢語言](https://docs.microsoft.com/azure/kusto/query/)的更改包括命令和函數的修訂格式。 因此，警報規則中較早提供的查詢不再有效。

[Azure 顧問](../../advisor/advisor-overview.md)會警告您有關此行為。 在 Azure Advisor 上為特定日誌警報規則添加了建議，該類別屬於具有中等影響的"高可用性"類別，並添加了"修復日誌警報規則以確保監視"的說明。 如果在 Azure Advisor 提供了建議七天后未糾正日誌警報規則中的警報查詢，Azure 監視器將禁用日誌警報，並確保當規則無法在相當大的時間段內持續運行時，不會不必要地計費（像一個星期）。

通過在[Azure 活動日誌](../../azure-resource-manager/management/view-activity-logs.md)中查找事件，可以找到 Azure 監視器禁用日誌警報規則的確切時間。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 中的記錄警示](../platform/alerts-unified-log.md)。
- 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 瞭解有關[日誌查詢的更多。](../log-query/log-query-overview.md)
