---
title: Azure 警報中日誌警報的 Webhook 操作
description: 本文介紹如何使用日誌分析工作區或應用程式見解創建日誌警報規則、警報如何將資料推送為 HTTP Webhook 以及可能的不同自訂的詳細資訊。
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667698"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook 動作記錄警示規則
在[Azure 中創建日誌警報時](alerts-log.md)，可以選擇[使用操作組](action-groups.md)執行一個或多個操作來配置日誌警報。 本文介紹了可用的不同 Webhook 操作，並演示如何配置基於 JSON 的自訂 Webhook。

> [!NOTE]
> 您還可以對 Webhook 集成使用[通用警報架構](https://aka.ms/commonAlertSchemaDocs)。 通用警報架構的優點是，在 Azure 監視器中的所有警報服務中具有單個可擴展和統一的警報負載。 請注意，通用警報架構不符合日誌警報的自訂 JSON 選項。 如果選中了公共警報架構負載，則無論在警報規則級別執行的自訂，它都會服從公共警報架構負載。 [瞭解常見的警報架構定義。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook 動作

使用 Webhook 操作，您可以通過單個 HTTP POST 請求調用外部進程。 調用的服務應支援 Webhook，並確定如何使用它接收的任何有效負載。

Webhook 動作需要下表中的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| **Webhook URL** |Webhook 的 URL。 |
| **自訂 JSON 承載** |在創建警報期間選擇此選項時，要隨 Webhook 一起發送的自訂有效負載。 有關詳細資訊，請參閱[管理日誌警報](alerts-log.md)。|

> [!NOTE]
> 日誌警報的"**包括用於 Webhook 的自訂 JSON 有效負載**"旁邊的 **"查看 Webhook"** 按鈕將顯示提供的自訂的示例 Webhook 負載。 它不包含實際資料，但代表用於日誌警報的 JSON 架構。 

Webhook 包括發送到外部服務的 URL 和以 JSON 格式格式化的有效負載。 根據預設，承載會包含下表中的值。 您可以選擇用自己的自訂承載來取代此承載。 在這種情況下，對每個參數使用表中的變數，將其值包含在自訂負載中。


| 參數 | 變數 | 描述 |
|:--- |:--- |:--- |
| *警報規則名稱* |#alertrulename |警示規則的名稱。 |
| *嚴重性* |#severity |為引發的記錄警示設定的嚴重性。 |
| *AlertThresholdOperator* |#thresholdoperator |警報規則的閾值運算子，該規則使用大於或小於。 |
| *AlertThresholdValue* |#thresholdvalue |警示規則的臨界值。 |
| *連結搜尋結果* |#linktosearchresults |連結到分析門戶，該門戶從創建警報的查詢中返回記錄。 |
| *ResultCount* |#searchresultcount |搜尋結果中的記錄數目。 |
| *搜尋間隔結束時間* |#searchintervalendtimeutc |以 UTC 為單位的查詢結束時間，格式為 mm/dd/yyyyHH：mm：sam/PM。 |
| *搜尋間隔* |#searchinterval |警報規則的時間視窗，格式為 HH：mm：ss。 |
| *搜尋間隔開始時間* |#searchintervalstarttimeutc |以 UTC 為單位的查詢開始時間，格式為 mm/dd/yyyyHH：mm：ss AM/PM。 
| *SearchQuery* |#searchquery |警示規則所使用的記錄檔搜尋查詢。 |
| *SearchResults* |"IncludeSearchResults": true|如果"包括搜尋結果"：true 在自訂 JSON Webhook 定義中作為頂級屬性添加，則查詢作為 JSON 表返回的記錄（僅限於前 1，000 條記錄）。 |
| *警報類型*| #alerttype | 配置為[指標度量](alerts-unified-log.md#metric-measurement-alert-rules)或[結果數的](alerts-unified-log.md#number-of-results-alert-rules)日誌警報規則的類型。|
| *工作區 ID* |#workspaceid |Log Analytics 工作區的識別碼。 |
| *應用程式 ID* |#applicationid |應用程式見解應用的 ID。 |
| *訂閱 ID* |#subscriptionid |使用的 Azure 訂閱的 ID。 

> [!NOTE]
> *LinkToSearch 結果**在*Azure 門戶的 URL 中傳遞搜索*查詢*、*搜索間隔開始時間*等參數，以便在"分析"部分中查看。 Azure 門戶的 URI 大小限制約為 2，000 個字元。 如果參數值超過限制，門戶*將不會*打開警報中提供的連結。 您可以手動輸入詳細資訊，在分析門戶中查看結果。 或者，您可以使用[應用程式見解分析 REST API](https://dev.applicationinsights.io/documentation/Using-the-API)或[日誌分析 REST API](/rest/api/loganalytics/)以程式設計方式檢索結果。 

例如，您可以指定下列自訂承載，其中包含稱為 text ** 的單一參數。 此 Webhook 調用的服務需要此參數。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
此示例有效負載在發送到 Webhook 時解析為類似以下內容：

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
由於自訂 Webhook 中的所有變數都必須在 JSON 存儲模組中指定（如"#searchinterval"），因此產生的 Webhook 在存儲模組中也有可變數據，如"00：05：00"。

要將搜尋結果包括在自訂負載中，請確保將**IncludeSearch結果**設置為 JSON 負載中的頂級屬性。 

## <a name="sample-payloads"></a>承載範例
本節顯示日誌警報的 Webhook 的示例有效負載。 示例有效負載包括有效負載是標準負載和自訂負載時的示例。

### <a name="standard-webhook-for-log-alerts"></a>日誌警報的標準 Web 掛鉤 
這兩個示例都有一個虛擬負載，只有兩列和兩行。

#### <a name="log-alert-for-log-analytics"></a>日誌分析日誌警報
以下示例有效負載用於標準 Webhook 操作 *，而不提供自訂 JSON 選項*，該選項用於基於日誌分析的警報：

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
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
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> 如果在日誌分析上切換了日誌警報的 API[首選項](alerts-log-api-switch.md)，"嚴重性"欄位值可能會更改。


#### <a name="log-alert-for-application-insights"></a>記錄應用程式見解警報
當標準 Webhook 用於基於應用程式見解的日誌警報時，以下示例有效負載適用于*沒有自訂 JSON 選項*的標準 Webhook：
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
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
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>使用自訂 JSON 負載記錄警報
例如，要創建僅包含警示名稱和搜尋結果的自訂有效負載，可以使用以下操作： 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

以下示例有效負載用於任何日誌警報的自訂 Webhook 操作：
    
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
- 瞭解[Azure 警報中的日誌警報](alerts-unified-log.md)。
- 瞭解如何在[Azure 中管理日誌警報](alerts-log.md)。
- 在 Azure 中創建和管理[操作組](action-groups.md)。
- 深入了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 瞭解有關[日誌查詢的更多。](../log-query/log-query-overview.md) 

