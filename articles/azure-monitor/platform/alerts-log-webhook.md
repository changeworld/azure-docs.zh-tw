---
title: Azure 警示中記錄警示的 Webhook 動作
description: 本文說明如何使用 Log Analytics 工作區或 Application Insights 來建立記錄警示規則、警示如何將資料推送為 HTTP webhook，以及各種可能的自訂詳細資料。
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 3311819f021533a28a41daf2c2f08193218fae96
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075270"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook 動作記錄警示規則
[在 Azure 中建立記錄警示](alerts-log.md)時，您可以選擇[使用動作群組](action-groups.md)來設定它，以執行一或多個動作。 本文說明可用的不同 webhook 動作，並說明如何設定以 JSON 為基礎的自訂 webhook。

> [!NOTE]
> 您也可以使用適用于 webhook 整合的[一般警示架構](https://aka.ms/commonAlertSchemaDocs)。 常見的警示架構可讓您在 Azure 監視器中的所有警示服務上擁有單一可擴充且整合的警示承載。請注意，一般警示架構不會接受記錄警示的自訂 JSON 選項。 如果選取該內容，則它會延遲一般的警示架構內容，而不論您可能已在警示規則層級進行的自訂。 [瞭解常見的警示架構定義。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook 動作

使用 webhook 動作時，您可以透過單一 HTTP POST 要求叫用外部進程。 所呼叫的服務應支援 webhook，並判斷如何使用其所接收的任何承載。

Webhook 動作需要下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| **Webhook URL** |Webhook 的 URL。 |
| **自訂 JSON 承載** |在警示建立期間選擇此選項時，要與 webhook 一起傳送的自訂承載。 如需詳細資訊，請參閱[記錄管理警示](alerts-log.md)。|

> [!NOTE]
> 記錄警示的 [**包含 webhook 的自訂 JSON**承載] 選項旁的 [ **View webhook** ] 按鈕會顯示所提供自訂的範例 Webhook 承載。 它不包含實際資料，而是代表用於記錄警示的 JSON 架構。 

Webhook 包含 URL 和以 JSON 格式格式化的內容，這些資料會傳送到外部服務。 根據預設，承載會包含下表中的值。 您可以選擇用自己的自訂承載來取代此承載。 在此情況下，請針對每個參數使用資料表中的變數，以在您的自訂承載中包含它們的值。


| 參數 | 變數 | 描述 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |警示規則的名稱。 |
| *嚴重性* |#severity |為引發的記錄警示設定的嚴重性。 |
| *AlertThresholdOperator* |#thresholdoperator |警示規則的臨界值運算子，其使用 [大於] 或 [小於]。 |
| *AlertThresholdValue* |#thresholdvalue |警示規則的臨界值。 |
| *LinkToSearchResults* |#linktosearchresults |從建立警示的查詢傳回記錄的分析入口網站連結。 |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |從建立警示的查詢傳回記錄的分析 API 連結。 |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |連結至分析入口網站，它會傳回查詢中的記錄，而篩選準則是由建立警示的維度值組合所篩選。 |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |連結至分析 API，此應用程式會傳回查詢中的記錄，而篩選準則是由建立警示的維度值組合所篩選。 |
| *ResultCount* |#searchresultcount |搜尋結果中的記錄數目。 |
| *搜尋間隔結束時間* |#searchintervalendtimeutc |查詢的結束時間（UTC），格式為 mm/dd/yyyy HH： mm： ss AM/PM。 |
| *搜尋間隔* |#searchinterval |警示規則的時間範圍，其格式為 HH： mm： ss。 |
| *搜尋間隔開始時間* |#searchintervalstarttimeutc |查詢的開始時間（UTC），格式為 mm/dd/yyyy HH： mm： ss AM/PM。 
| *SearchQuery* |#searchquery |警示規則所使用的記錄檔搜尋查詢。 |
| *SearchResults* |"IncludeSearchResults": true|查詢所傳回的記錄為 JSON 資料表，受限於前1000筆記錄。 "IncludeSearchResults"： true 會在自訂 JSON webhook 定義中新增為最上層屬性。 |
| *維度* |"IncludeDimensions"： true|當做 JSON 區段觸發該警示的維度值組合。 "IncludeDimensions"： true 會在自訂 JSON webhook 定義中新增為最上層屬性。 |
| *警示類型*| #alerttype | 設定為[計量量測](alerts-unified-log.md#metric-measurement-alert-rules)或[結果數目](alerts-unified-log.md#number-of-results-alert-rules)的記錄警示規則類型。|
| *WorkspaceID* |#workspaceid |Log Analytics 工作區的識別碼。 |
| *應用程式識別碼* |#applicationid |Application Insights 應用程式的識別碼。 |
| *訂用帳戶識別碼* |#subscriptionid |您的 Azure 訂用帳戶識別碼。 

> [!NOTE]
> 提供的連結會將 URL 中的參數（例如*SearchQuery*、*搜尋間隔 StartTime*和*搜尋間隔結束時間*）傳遞給 Azure 入口網站或 API。

例如，您可以指定下列自訂承載，其中包含稱為 text ** 的單一參數。 此 webhook 呼叫的服務需要此參數。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
此範例承載會在傳送至 webhook 時解析成類似下列的內容：

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
由於自訂 webhook 中的所有變數都必須在 JSON 主機殼內指定，例如「#searchinterval」，因此產生的 webhook 在主機殼內也有變數資料，例如 "00:05:00"。

若要在自訂承載中包含搜尋結果，請確定**IncludeSearchResults**已設定為 JSON 承載中的最上層屬性。 

## <a name="sample-payloads"></a>承載範例
本節顯示適用于記錄警示之 webhook 的範例承載。 範例承載包括當裝載是標準的和自訂時的範例。

### <a name="standard-webhook-for-log-alerts"></a>記錄警示的標準 webhook 
這兩個範例都有一個虛擬承載，其中只有兩個數據行和兩個數據列。

#### <a name="log-alert-for-log-analytics"></a>Log Analytics 的記錄警示
下列範例承載適用于*不含自訂 JSON 選項*的標準 webhook 動作，用於以 Log Analytics 為基礎的警示：

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

> [!NOTE]
> 如果您已針對 Log Analytics 上的記錄警示[切換 API 喜好](alerts-log-api-switch.md)設定，則「嚴重性」域值可能會變更。


#### <a name="log-alert-for-application-insights"></a>Application Insights 的記錄警示
下列範例承載適用于*不含自訂 JSON 選項*的標準 webhook，用於以 Application Insights 為基礎的記錄警示：
    
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

#### <a name="log-alert-with-custom-json-payload"></a>具有自訂 JSON 承載的記錄警示
例如，若要建立只包含警示名稱和搜尋結果的自訂承載，您可以使用下列方式： 

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
- 瞭解[Azure 警示中的記錄警示](alerts-unified-log.md)。
- 瞭解如何[在 Azure 中記錄管理警示](alerts-log.md)。
- [在 Azure 中](action-groups.md)建立及管理動作群組。
- 深入了解 [Application Insights](../log-query/log-query-overview.md)。
- 深入瞭解[記錄查詢](../log-query/log-query-overview.md)。 
