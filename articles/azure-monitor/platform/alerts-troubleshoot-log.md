---
title: 針對 Azure 監視器中的記錄警示進行疑難排解 |Microsoft Docs
description: Azure 中記錄警示規則的常見問題、錯誤和解決方式。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: 7be1c350af6c9bb84669b45a9bc8a1d9dd808133
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165629"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>針對 Azure 監視器中的記錄警示進行疑難排解  

本文說明如何解決 Azure 監視器中記錄警示的常見問題。 它也提供記錄警示功能和設定的常見問題解決方案。

「*記錄警示*」一詞描述根據[Azure log Analytics 工作區](../learn/tutorial-viewdata.md)中的記錄查詢或[Azure 應用程式 Insights](../../azure-monitor/app/analytics.md)中引發的規則。 在 Azure 監視器中，深入瞭解[記錄警示](../platform/alerts-unified-log.md)中的功能、術語和類型。

> [!NOTE]
> 本文並不考慮到 Azure 入口網站會顯示觸發的警示規則，而且相關聯的動作群組不會執行通知。 如需這類情況，請參閱在[Azure 入口網站中建立和管理動作群組中](../platform/action-groups.md)的詳細資料。

## <a name="log-alert-didnt-fire"></a>記錄警示未引發

以下是一些[在 Azure 監視器中設定的記錄警示規則](../platform/alerts-log.md)狀態不會[如預期般顯示為已*引發*](../platform/alerts-managing-alert-states.md)的常見原因。

### <a name="data-ingestion-time-for-logs"></a>記錄的資料內嵌時間

記錄警示會根據[Log Analytics](../learn/tutorial-viewdata.md)或[Application Insights](../../azure-monitor/app/analytics.md)定期執行查詢。 由於 Azure 監視器會處理來自全球各地不同來源的數千個客戶的數 tb 資料，因此服務容易受到不同時間延遲的影響。 如需詳細資訊，請參閱[Azure 監視器記錄中的資料內嵌時間](../platform/data-ingestion-time.md)。

為了減輕延遲，系統會等待並多次嘗試警示查詢，以找出所需的資料尚未內嵌。 系統已設定以指數方式增加等候時間。 只有在資料可供使用之後，才會觸發記錄警示，因此延遲可能是因為記錄資料的內嵌緩慢所造成。

### <a name="incorrect-time-period-configured"></a>設定的時間週期不正確

如[記錄警示的術語](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)一文所述，設定中所述的時間週期會指定查詢的時間範圍。 此查詢只會傳回在此範圍內建立的記錄。

時間週期會限制針對記錄檔查詢所提取的資料，以防止濫用，而且它會規避記錄查詢中所使用的任何時間命令 (例如**前**) 。 例如，如果時間週期設定為 60 分鐘，且查詢會在下午 1:15 執行，則只有在下午 12:15 與下午 1:15 之間所建立的記錄會用於記錄查詢。 如果記錄查詢使用類似**前 (1d) **的時間命令，則查詢仍然只會使用 12:15 pm 到 1:15 pm 之間的資料，因為時間週期設定為該間隔。

檢查設定中的時間週期是否符合您的查詢。 針對稍早所示的範例，如果記錄查詢使用**前 (1d) **加上綠色標記，則時間週期應設定為24小時或1440分鐘 (以紅色) 表示。 此設定可確保查詢會如預期執行。

![時間週期](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>已設定隱藏警示選項

如在[Azure 入口網站中建立記錄警示規則](../platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)一文的步驟8所述，記錄警示會提供**隱藏警示**選項，以隱藏設定的一段時間內的觸發和通知動作。 因此，您可能會認為警示未引發。 事實上，它已引發但已隱藏。  

![隱藏警示](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>公制度量單位警示規則不正確

*計量度量記錄警示*是記錄警示的子類型，具有特殊功能和受限制的警示查詢語法。 計量度量記錄警示的規則要求查詢輸出必須是計量時間序列。 也就是說，輸出是一個資料表，其中包含不同的時間週期，以及對應的匯總值。

您可以選擇在資料表中加入額外的變數，並**AggregatedValue**。 這些變數可以用來排序資料表。

例如，假設計量度量記錄警示的規則設定為：

- 查詢`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6小時的時間週期
- 閾值50
- 三個連續違規的警示邏輯
- 選擇做為 **$table**的**匯總**

因為此命令包含**摘要 .。。根據**和提供兩個變數 (**時間戳記**和 **$table**) ，系統會選擇 **$table** **匯總依據**。 系統會依照 [ **$table** ] 欄位排序結果資料表，如下列螢幕擷取畫面所示。 然後它會查看每個資料表類型的多個**AggregatedValue**實例， (例如**AvailabilityResults**) ，以查看是否有三個或多個連續的違規。

![具有多個值的度量測量查詢執行](media/alert-log-troubleshoot/LogMMQuery.png)

由於在 **$table**上定義了**Aggregate** ，因此資料會在 **$table**資料行上排序， (以紅色) 表示。 然後，我們會群組並尋找 [**匯總依據**] 欄位的類型。

例如，針對 **$table**， **availabilityResults**的值會被視為一個繪圖/實體 (以橙色) 表示。 在這個值繪圖/實體中，警示服務會檢查是否有三個連續的缺口 (以綠色) 表示。 缺口會觸發資料表值**availabilityResults**的警示。

同樣地，如果 **$table**的任何其他值發生三個連續的缺口，則會針對相同的內容觸發另一個警示通知。 警示服務會自動排序一個繪圖/實體中的值， (以橙色) 依時程表示。

現在假設已修改計量度量記錄警示的規則，而且查詢為 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` 。 其餘的設定與之前保持不變，包括三個連續違規的警示邏輯。 在此情況下，[**匯總依據**] 選項預設為 **[時間戳記]** 。 查詢中只會提供一個值給**摘要 .。。 (也**就是，**時間戳記**) 。 如同先前的範例，執行結束時的輸出會如下所示。

   ![使用單數值的度量測量查詢執行](media/alert-log-troubleshoot/LogMMtimestamp.png)

由於 [**匯總依據**] 是在**時間戳記**上定義，因此資料會依據 [**時間戳記]** 欄排序 (以紅色) 表示。 然後依**時間戳記**分組。 例如，的值 `2018-10-17T06:00:00Z` 會被視為一個繪圖/實體 (以橙色) 表示。 在這個值繪圖/實體中，警示服務將不會發現連續的漏洞 (因為每個**時間戳記**值只有一個) 的專案。 所以永遠不會觸發警示。 在這種情況下，使用者必須執行下列其中一項動作：

- 加入虛擬變數或現有的變數 (例如 **$table**) ，以便使用 [**匯總依據**] 欄位正確排序。
- 重新設定警示規則，以改為根據**總缺口**使用警示邏輯。

## <a name="log-alert-fired-unnecessarily"></a>記錄警示被不必要地引發

當您在[Azure 警示](../platform/alerts-managing-alert-states.md)中進行查看時，可能會意外觸發 Azure 監視器中已設定的[記錄警示規則](../platform/alerts-log.md)。 下列各節將說明一些常見的原因。

### <a name="alert-triggered-by-partial-data"></a>警示被部分資料觸發

Log Analytics 和 Application Insights 會受限於內嵌延遲和處理。 當您執行記錄警示查詢時，可能會發現沒有可用的資料，或只有部分資料可以使用。 如需詳細資訊，請參閱[Azure 監視器中的記錄資料內建時間](../platform/data-ingestion-time.md)。

根據您設定警示規則的方式而定，如果在警示執行時，記錄中沒有資料或部分資料，就可能會發生 misfiring。 在這種情況下，建議您變更警示查詢或設定。

例如，如果您設定在分析查詢的結果數目小於5時觸發記錄警示規則，則當沒有任何資料 (零筆記錄時，就會觸發警示) 或 (一筆記錄) 的部分結果。 但在資料內嵌延遲之後，具有完整資料的相同查詢可能會提供10筆記錄的結果。

### <a name="alert-query-output-is-misunderstood"></a>警示查詢輸出被誤解

您在分析查詢中提供記錄警示的邏輯。 分析查詢可以使用各種龐大的資料和數學函數。 警示服務會在指定的一段時間內，以資料指定的間隔執行查詢。 警示服務會根據警示類型對查詢進行細微的變更。 您可以在 [**設定信號邏輯**] 畫面上的 [**要執行的查詢**] 區段中，看到這項變更：

![要執行的查詢](media/alert-log-troubleshoot/LogAlertPreview.png)

[**要執行的查詢**] 方塊是記錄警示服務執行的內容。 如果您想要瞭解警示查詢輸出在建立警示之前可能會發生的情況，您可以透過[分析入口網站](../log-query/portals.md)或[分析 API](https://docs.microsoft.com/rest/api/loganalytics/)來執行指定的查詢和 timespan。

## <a name="log-alert-was-disabled"></a>記錄警示已停用

下列各節列出 Azure 監視器可能停用[記錄警示規則](../platform/alerts-log.md)的一些原因。

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>已建立警示的資源已不存在

在 Azure 監視器中建立的記錄警示規則會以特定資源（例如 Azure Log Analytics 工作區、Azure 應用程式 Insights 應用程式和 Azure 資源）為目標。 然後，記錄警示服務會針對指定的目標，執行規則中提供的分析查詢。 但在建立規則之後，使用者通常會繼續從 Azure 刪除--或在 Azure 內部移動--記錄警示規則的目標。 因為警示規則的目標已不再有效，所以規則的執行會失敗。

在這種情況下，Azure 監視器會停用記錄警示，並確保當規則無法持續長時間執行時，您不需要支付任何費用， (例如一周) 。 您可以找出 Azure 監視器透過[Azure 活動記錄](../../azure-resource-manager/management/view-activity-logs.md)停用記錄警示的確切時間。 在 [Azure 活動記錄檔] 中，當 Azure 監視器停用記錄警示規則時，就會新增事件。

Azure 活動記錄中的下列範例事件適用于因為連續失敗而停用的警示規則。

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>記錄警示中使用的查詢無效

在 Azure 監視器中建立的每個記錄警示規則，都必須指定要定期執行警示服務的分析查詢。 在建立或更新規則時，分析查詢可能會有正確的語法。 但有時候，在一段時間內，記錄警示規則中提供的查詢可能會開發語法問題，並導致規則執行失敗。 記錄警示規則中提供的分析查詢可能會產生錯誤的一些常見原因如下：

- 此查詢會撰寫成[跨多個資源執行](../log-query/cross-workspace-query.md)。 一或多個指定的資源已不存在。
- [計量測量類型記錄警示](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)已設定的警示查詢不符合語法規范
- 沒有分析平臺的資料流程。 [查詢執行會產生錯誤](https://dev.loganalytics.io/documentation/Using-the-API/Errors)，因為提供的查詢沒有任何資料。
- [查詢語言](https://docs.microsoft.com/azure/kusto/query/)的變更包括針對命令和函式修改後的格式。 因此，先前在警示規則中提供的查詢已不再有效。

[Azure Advisor](../../advisor/advisor-overview.md)警告您有關此行為。 系統會針對 Azure Advisor 上的 [高可用性] 類別下的 [使用中度影響] 和 [修復您的記錄警示規則以確保監視] 的描述，新增特定記錄警示規則的建議。

> [!NOTE]
> 如果在 Azure Advisor 提供了七天的建議之後，記錄警示規則中的警示查詢不會受到修正，Azure 監視器將會停用記錄警示，並確保當規則無法持續在一段可調整的期間內（ (7 天) 時，不會以不必要的方式計費。 您可以尋找[Azure 活動記錄](../../azure-resource-manager/management/view-activity-logs.md)中的事件，以找出 Azure 監視器停用記錄警示規則的確切時間。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 中的記錄警示](../platform/alerts-unified-log.md)。
- 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 深入瞭解[記錄查詢](../log-query/log-query-overview.md)。
