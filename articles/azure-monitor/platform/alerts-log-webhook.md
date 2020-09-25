---
title: Azure 警示中記錄警示的 Webhook 動作
description: 說明如何使用 webhook 動作和可用自訂設定記錄警示推播
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 9a074be9bcc62d8c20635400f462f52fb796d2fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294303"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook 動作記錄警示規則

[記錄警示](alerts-log.md) 支援設定 [webhook 動作群組](action-groups.md#webhook)。 在本文中，我們將說明可用的屬性，以及如何設定自訂 JSON webhook。

> [!NOTE]
> API 版本目前不支援以 JSON 為基礎的自訂 webhook `2020-05-01-preview`

> [!NOTE]
> 建議您針對 webhook 整合使用 [常見的警示架構](alerts-common-schema.md) 。 常見的警示架構可讓您在 Azure 監視器中的所有警示服務上擁有單一可延伸和統一的警示承載。 針對已定義自訂 JSON 承載的記錄警示規則，啟用一般架構會將裝載架構還原為 [此處](alerts-common-schema-definitions.md#log-alerts)所述的內容。 啟用通用架構的警示具有每個警示 256 KB 的大小上限，較大的警示將不會包含搜尋結果。 未包含搜尋結果時，您應該使用或透過 `LinkToFilteredSearchResultsAPI` `LinkToSearchResultsAPI` LOG Analytics API 存取查詢結果。

## <a name="webhook-payload-properties"></a>Webhook 承載屬性

Webhook 動作可讓您叫用單一 HTTP POST 要求。 呼叫的服務應支援 webhook，並瞭解如何使用它所接收的承載。

預設 webhook 動作屬性和其自訂 JSON 參數名稱：

| 參數 | 變數 | 描述 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |警示規則的名稱。 |
| *嚴重性* |#severity |為引發的記錄警示設定的嚴重性。 |
| *AlertThresholdOperator* |#thresholdoperator |警示規則的臨界值運算子。 |
| *AlertThresholdValue* |#thresholdvalue |警示規則的臨界值。 |
| *LinkToSearchResults* |#linktosearchresults |連結至分析入口網站，以從建立警示的查詢傳回記錄。 |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |連結至分析 API，以從建立警示的查詢傳回記錄。 |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |連結至 Analytics 入口網站，此入口網站會從建立警示的維度值組合所篩選的查詢傳回記錄。 |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |連結至分析 API，此 API 會從建立警示的維度值組合所篩選的查詢傳回記錄。 |
| *ResultCount* |#searchresultcount |搜尋結果中的記錄數目。 |
| *搜尋間隔結束時間* |#searchintervalendtimeutc |查詢的結束時間（UTC），格式為 mm/dd/yyyy HH： mm： ss AM/PM。 |
| *搜尋間隔* |#searchinterval |警示規則的時間範圍，格式為 HH： mm： ss。 |
| *搜尋間隔開始時間* |#searchintervalstarttimeutc |查詢的開始時間（UTC），格式為 mm/dd/yyyy HH： mm： ss AM/PM。 
| *SearchQuery* |#searchquery |警示規則所使用的記錄檔搜尋查詢。 |
| *SearchResults* |"IncludeSearchResults": true|查詢以 JSON 資料表形式傳回的記錄，受限於前1000筆記錄。 ">includesearchresults"： true 是在自訂 JSON webhook 定義中新增為最上層屬性。 |
| *維度* |"IncludeDimensions"： true|觸發該警示作為 JSON 區段的維度值組合。 "IncludeDimensions"： true 是在自訂 JSON webhook 定義中新增為最上層屬性。 |
| *警示類型*| #alerttype | 設定為 [度量量測或結果數目](alerts-unified-log.md#measure)的記錄警示規則類型。|
| *WorkspaceID* |#workspaceid |Log Analytics 工作區的識別碼。 |
| *應用程式識別碼* |#applicationid |Application Insights 應用程式的識別碼。 |
| *訂用帳戶識別碼* |#subscriptionid |使用的 Azure 訂用帳戶識別碼。 |

## <a name="custom-webhook-payload-definition"></a>自訂 webhook 承載定義

您可以使用 [ **包含 webhook 的自訂 json** 承載]，利用上述參數取得自訂 json 承載。 您也可以產生其他屬性。
例如，您可以指定下列自訂承載，其中包含稱為 text ** 的單一參數。 此 webhook 呼叫的服務需要此參數：

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
此範例承載會在傳送至 webhook 時解析為類似下列的內容：

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
自訂 webhook 中的變數必須在 JSON 主機殼內指定。 例如，在上述 webhook 範例中參考 "#searchresultcount"，將會根據警示結果來輸出。

若要包含搜尋結果，請將 **>includesearchresults** 新增為自訂 JSON 中的最上層屬性。 搜尋結果會包含為 JSON 結構，因此無法在自訂定義的欄位中參考結果。 

> [!NOTE]
> [**包含 webhook 的自訂 JSON**承載] 選項旁的 [ **View Webhook** ] 按鈕會顯示所提供內容的預覽。 它不包含實際的資料，但代表將使用的 JSON 架構。 

## <a name="sample-payloads"></a>承載範例
本節顯示 webhook 記錄警示的範例承載。 範例承載包含當承載為標準和自訂時的範例。

### <a name="log-alert-for-log-analytics"></a>Log Analytics 的記錄警示
下列範例承載適用于以 Log Analytics 為基礎的警示所使用的標準 webhook 動作：

> [!NOTE]
> 如果您已從[舊版 Log Analytics 警示 API](api-alerts.md)[切換至目前的 scheduledQueryRules API](alerts-log-api-switch.md) ，[嚴重性] 域值就會變更。

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Application Insights 的記錄警示
下列範例承載適用于根據 Application Insights 資源，用於記錄警示的標準 webhook：
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>從 API 版本)  (其他資源記錄的記錄警示 `2020-05-01-preview`

> [!NOTE]
> API 版本 `2020-05-01-preview` 和以資源為中心的記錄警示目前不會產生額外費用。  預覽版中的功能價格將于未來宣佈，並在開始計費之前提供通知。 如果您選擇在通知期間之後繼續使用新的 API 版本和以資源為中心的記錄警示，將會以適用的費率計費。

下列範例承載適用于標準 webhook，適用于以其他資源記錄為基礎的記錄警示， (排除工作區和 Application Insights) ：

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>具有自訂 JSON 承載的記錄警示
例如，若要建立只包含警示名稱和搜尋結果的自訂承載，請使用此設定： 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

下列範例承載適用于任何記錄警示的自訂 webhook 動作：
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>後續步驟
- 瞭解 [Azure 警示中的記錄警示](alerts-unified-log.md)。
- 瞭解如何 [在 Azure 中記錄管理警示](alerts-log.md)。
- [在 Azure 中](action-groups.md)建立和管理動作群組。
- 深入了解 [Application Insights](../log-query/log-query-overview.md)。
- 深入瞭解 [記錄查詢](../log-query/log-query-overview.md)。 
