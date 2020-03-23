---
title: 將 Azure 平臺記錄串流至事件中樞
description: 瞭解如何將 Azure 資源記錄串流至事件中樞，以將資料傳送至外部系統（例如協力廠商 Siem 和其他 log analytics 解決方案）。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658909"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>將 Azure 平臺記錄串流至 Azure 事件中樞
Azure 中的[平臺記錄](platform-logs-overview.md)，包括 azure 活動記錄和資源記錄，可提供 azure 資源的詳細診斷和審核資訊，以及它們所依賴的 azure 平臺。  本文說明將資料串流至事件中樞，以將資料傳送至外部系統（例如協力廠商 Siem 和其他 log analytics 解決方案）的資料流程平臺記錄。


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>您可以使用傳送到事件中樞的平臺記錄來執行的動作
將 Azure 中的平臺記錄串流至事件中樞，以提供下列功能：

* **將記錄串流至協力廠商記錄和遙測系統**–將所有平臺記錄串流至單一事件中樞，以將記錄資料傳送至協力廠商 SIEM 或 log analytics 工具。
  
* **建立自訂遙測和記錄平臺**–事件中樞的高擴充性發佈訂閱特性，可讓您將平臺記錄彈性地內嵌到自訂的 teletry 平臺。 如需詳細資訊，請參閱[Azure 事件中樞上的全域調整遙測平臺的設計和調整大小](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

* 藉**由將資料串流至 Power BI 來查看服務健全狀況**–使用事件中樞、串流分析和 Power BI，將您的診斷資料轉換為 Azure 服務上近乎即時的深入解析。 請參閱 [串流分析和 Power BI：串流資料的即時分析儀表板](../../stream-analytics/stream-analytics-power-bi-dashboard.md)，以取得此解決方案的詳細資訊。

    下列 SQL 程式碼是您可以使用的範例串流分析查詢，能將所有記錄資料剖析至 Power BI 表格：
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>必要條件
如果您還沒有[事件中樞](../../event-hubs/event-hubs-create.md)，您必須建立它。 如果您已經有使用此事件中樞命名空間的診斷設定，則會重複使用該事件中樞。

命名空間的共用存取原則會定義串流機制擁有的許可權。 串流至事件中樞需要 [管理]、[傳送] 和 [接聽] 許可權。 您可以在事件中樞命名空間的 [設定] 索引標籤下的 Azure 入口網站中，建立或修改共用存取原則。

若要更新診斷設定以包含串流，您必須具有該事件中樞授權規則的 ListKey 許可權。 事件中樞命名空間不一定要和發出記錄的訂用帳戶屬於相同的訂用帳戶，只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權，而且這兩個訂用帳戶都屬於同一個 ADD 租用戶。

## <a name="create-a-diagnostic-setting"></a>建立診斷設定
藉由建立 Azure 資源的診斷設定，將平臺記錄傳送至事件中樞和其他目的地。 如需詳細資訊，請參閱[建立診斷設定以收集 Azure 中的記錄和計量](diagnostic-settings.md)。

## <a name="collect-data-from-compute-resources"></a>從計算資源收集資料
診斷設定會收集 Azure 計算資源的資源記錄，就像任何其他資源一樣，而不是其客體作業系統或工作負載。 若要收集此資料，請安裝[Log Analytics 代理程式](log-analytics-agent.md)。 


## <a name="consuming-log-data-from-event-hubs"></a>從事件中樞取用記錄資料
來自事件中樞的平臺記錄會以 JSON 格式使用下表中的元素。

| 元素名稱 | 描述 |
| --- | --- |
| 記錄數 |此承載中的所有記錄事件的陣列。 |
| time |事件發生的時間。 |
| category |此事件的記錄檔分類。 |
| resourceId |產生此事件之資源的資源識別碼。 |
| operationName |作業名稱。 |
| level |選擇性。 表示記錄事件層級。 |
| properties |事件的屬性。 每個 Azure 服務都會有不同的變更，[]()如中所述。 |


以下是來自資源記錄檔事件中樞的範例輸出資料：

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>後續步驟

* [深入瞭解資源記錄](platform-logs-overview.md)。
* [建立診斷設定以收集 Azure 中的記錄和計量](diagnostic-settings.md)。
* [使用 Azure 監視器串流 Azure Active Directory 記錄](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
* [開始使用事件中樞](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。

