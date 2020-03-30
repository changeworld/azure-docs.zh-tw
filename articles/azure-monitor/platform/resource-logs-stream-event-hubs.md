---
title: 將 Azure 平台記錄串流至事件中樞
description: 瞭解如何將 Azure 資源日誌資料流到事件中心，將資料發送到外部系統，如協力廠商 SIEM 和其他日誌分析解決方案。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658909"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>將 Azure 平臺日誌資料流到 Azure 事件中心
Azure 中[的平臺日誌](platform-logs-overview.md)（包括 Azure 活動日誌和資源日誌）為 Azure 資源和它們所依賴的 Azure 平臺提供了詳細的診斷和審核資訊。  本文介紹了流式處理平臺日誌到事件中心，將資料發送到外部系統，如協力廠商 SIEM 和其他日誌分析解決方案。


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>對發送到事件中心的平臺日誌可以執行哪些操作
將 Azure 中的平臺日誌資料流到事件中心，以提供以下功能：

* **將日誌資料流到協力廠商日誌記錄和遙測系統**– 將所有平臺日誌資料流到單個事件中心，以將日誌資料傳送到協力廠商 SIEM 或日誌分析工具。
  
* **構建自訂遙測和日誌記錄平臺**– 事件中心的高度可擴展的發佈-訂閱特性允許您靈活地將平臺日誌引入自訂遠端嘗試平臺。 有關詳細資訊[，請參閱在 Azure 事件中心上設計和調整全域縮放遙測平臺的尺寸](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

* **通過將資料流程式傳輸到 Power BI 來查看服務運行狀況**— 使用事件中心、流分析和 Power BI 將診斷資料轉換為有關 Azure 服務的近乎即時的見解。 有關此解決方案的詳細資訊[，請參閱流分析和 Power BI：有關流資料的即時分析儀表板](../../stream-analytics/stream-analytics-power-bi-dashboard.md)。

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

## <a name="prerequisites"></a>Prerequisites
如果還沒有[事件中心，則需要創建一個事件中心](../../event-hubs/event-hubs-create.md)。 如果您已經使用此事件中心命名空間設置診斷設置，則將重用該事件中心。

命名空間的共用訪問策略定義流式處理機制具有的許可權。 資料流到事件中心需要管理、發送和偵聽許可權。 您可以在"為事件中心命名空間配置"選項卡下的 Azure 門戶中創建或修改共用訪問策略。

要更新診斷設置以包括流式處理，您必須具有該事件中心授權規則的 ListKey 許可權。 事件中樞命名空間不一定要和發出記錄的訂用帳戶屬於相同的訂用帳戶，只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權，而且這兩個訂用帳戶都屬於同一個 ADD 租用戶。

## <a name="create-a-diagnostic-setting"></a>建立診斷設定
通過為 Azure 資源創建診斷設置，將平臺日誌發送到事件中心和其他目標。 有關詳細資訊[，請參閱創建診斷設置以在 Azure 中收集日誌和指標](diagnostic-settings.md)。

## <a name="collect-data-from-compute-resources"></a>從計算資源收集資料
診斷設置將像收集任何其他資源一樣為 Azure 計算資源收集資源日誌，但不會收集其客體作業系統或工作負荷的資源日誌。 要收集此資料，請安裝[日誌分析代理](log-analytics-agent.md)。 


## <a name="consuming-log-data-from-event-hubs"></a>使用來自事件中心的日誌資料
來自事件中心的平臺日誌使用 JSON 格式，並具有下表中的元素。

| 元素名稱 | 描述 |
| --- | --- |
| 記錄 |此承載中的所有記錄事件的陣列。 |
| time |事件發生的時間。 |
| category |此事件的記錄檔分類。 |
| resourceId |產生此事件之資源的資源識別碼。 |
| operationName |作業名稱。 |
| 層級 |選擇性。 表示記錄事件層級。 |
| properties |事件的屬性。 每個 Azure 服務都會有不同的變更，[]()如中所述。 |


以下是資源日誌的事件中心的示例輸出資料：

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

* [閱讀有關資源日誌的更多內容](platform-logs-overview.md)。
* [創建診斷設置以在 Azure 中收集日誌和指標](diagnostic-settings.md)。
* [使用 Azure 監視器 資料流 Azure 活動目錄日誌](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
* [開始使用事件中心](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。

