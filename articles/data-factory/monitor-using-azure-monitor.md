---
title: 使用 Azure 監視器來監視 Data Factory
description: 瞭解如何使用 Azure 監視器來監視/Azure Data Factory 管線，方法是啟用具有 Data Factory 資訊的診斷記錄。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419454"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>使用 Azure 監視器來警示及監視 data factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

雲端應用程式很複雜，而且有許多移動元件。 監視器會提供資料，協助確保您的應用程式保持正常運作狀態。 監視也可以協助您避免潛在的問題，並針對過去的問題進行疑難排解。

您可以使用監視資料來取得應用程式的深入見解。 此知識可協助您改善應用程式效能和可維護性。 它也可協助您將需要手動介入的動作自動化。

Azure 監視器提供適用于大部分 Azure 服務的基本層級基礎結構計量和記錄。 Azure 診斷記錄是由資源發出，並提供有關該資源之作業的豐富、經常性資料。 和 Azure Data Factory 會在監視器中寫入診斷記錄。

如需詳細資訊，請參閱[Azure 監視器總覽](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。

## <a name="keeping-azure-data-factory-data"></a>保留 Azure Data Factory 的資料

Data Factory 只會儲存45天的管線執行資料。 如果您想要將該資料保留一段較長的時間，請使用 [監視器]。 使用監視器，您可以路由診斷記錄以進行分析。 您也可以將它們保留在儲存體帳戶中，讓您擁有所選持續時間的原廠資訊。

## <a name="diagnostic-logs"></a>診斷記錄

* 將您的診斷記錄儲存到儲存體帳戶，以進行審核或手動檢查。 您可以使用診斷設定來指定保留時間（以天為單位）。
* 將記錄串流至 Azure 事件中樞。 記錄會變成合作夥伴服務或自訂分析解決方案的輸入，如 Power BI。
* 使用 Log Analytics 分析記錄。

您可以使用不在發出記錄的資源訂用帳戶中的儲存體帳戶或事件中樞命名空間。 設定此設定的使用者必須具有這兩個訂用帳戶的適當角色型存取控制（RBAC）存取權。

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

### <a name="diagnostic-settings"></a>診斷設定

使用診斷設定來設定 noncompute 資源的診斷記錄。 資源控制項的設定具有下列功能：

* 它們會指定診斷記錄的傳送位置。 範例包括 Azure 儲存體帳戶、Azure 事件中樞或監視器記錄。
* 它們會指定要傳送的記錄類別。
* 它們會指定每個記錄類別應該保留在儲存體帳戶中的時間長度。
* 保留期為 0 天表示會永遠保留記錄。 否則，此值可以是從1到2147483647的任何天數。
* 如果已設定保留原則，但已停用儲存體帳戶中的記錄，保留原則不會有任何作用。 例如，只有選取事件中樞或監視記錄檔選項時，才會發生這種狀況。
* 保留原則會每天套用。 日期之間的界限會在國際標準時間（UTC）午夜發生。 一天結束時，會刪除保留原則以外的天數記錄。 例如，如果您的保留原則是一天，則在今天的一開始，會刪除昨天之前的記錄。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>透過 Azure 監視器 REST API 啟用診斷記錄

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在監視 REST API 中建立或更新診斷設定

##### <a name="request"></a>要求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* 將 `{api-version}` 取代為 `2016-09-01`。
* 將`{resource-id}`取代為您要編輯診斷設定的資源識別碼。 如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 將 `Content-Type` 標頭設定為 `application/json`。
* 將授權標頭設定為您從 Azure Active Directory （Azure AD）所獲得的 JSON web 權杖。 如需詳細資訊，請參閱[驗證要求](../active-directory/develop/authentication-scenarios.md)。

##### <a name="body"></a>body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 屬性 | 類型 | 描述 |
| --- | --- | --- |
| **storageAccountId** |字串 | 您要傳送診斷記錄之目標儲存體帳戶的資源識別碼。 |
| **serviceBusRuleId** |字串 | 服務匯流排命名空間的服務匯流排規則識別碼，您想要在其中為串流診斷記錄建立事件中樞。 規則識別碼的格式`{service bus resource ID}/authorizationrules/{key name}`為。|
| **workspaceId** | 複雜類型 | 度量時間粒紋和其保留原則的陣列。 這個屬性的值是空的。 |
|**規範**| 要傳遞給已叫用之管線的管線執行參數值| 將參數名稱對應到引數值的 JSON 物件。 |
| **退出**| 複雜類型| 資源類型的診斷記錄類別目錄名稱。 若要取得資源的診斷記錄類別清單，請執行 [取得診斷-設定] 作業。 |
| **類別**| 字串| 記錄類別和其保留原則的陣列。 |
| **timeGrain** | 字串 | 計量的細微性，以 ISO 8601 持續時間格式加以捕捉。 屬性值必須是， `PT1M`這會指定一分鐘。 |
| **後**| 布林值 | 指定是否為此資源啟用度量或記錄類別的收集。 |
| **retentionPolicy**| 複雜類型| 描述計量或記錄類別的保留原則。 此屬性僅用於儲存體帳戶。 |
|**之內**| Int| 要保留計量或記錄的天數。 如果屬性值為0，則會永遠保留記錄。 此屬性僅用於儲存體帳戶。 |

##### <a name="response"></a>回應

200 確定。


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>在監視器中取得診斷設定的相關資訊 REST API

##### <a name="request"></a>要求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* 將 `{api-version}` 取代為 `2016-09-01`。
* 將`{resource-id}`取代為您要編輯診斷設定的資源識別碼。 如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 將 `Content-Type` 標頭設定為 `application/json`。
* 將授權標頭設定為您從 Azure AD 所獲得的 JSON web 權杖。 如需詳細資訊，請參閱[驗證要求](../active-directory/develop/authentication-scenarios.md)。

##### <a name="response"></a>回應

200 確定。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
如需詳細資訊，請參閱[診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)。

## <a name="schema-of-logs-and-events"></a>記錄和事件的架構

### <a name="monitor-schema"></a>監視架構

#### <a name="activity-run-log-attributes"></a>活動-執行記錄屬性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 屬性 | 類型 | 描述 | 範例 |
| --- | --- | --- | --- |
| **Level** |字串 | 診斷記錄的層級。 針對 [活動-執行記錄]，將屬性值設定為4。 | `4` |
| **correlationId** |字串 | 用於追蹤特定要求的唯一識別碼。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字串 | 以 timespan UTC 格式`YYYY-MM-DDTHH:MM:SS.00000Z`的事件時間。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| 字串| 活動執行的識別碼。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| 字串| 管線執行的識別碼。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| 字串 | 與資料處理站資源相關聯的識別碼。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| 字串 | 診斷記錄的類別。 將屬性 (Property) 值設定為 `ActivityRuns`。 | `ActivityRuns` |
|**二級**| 字串 | 診斷記錄的層級。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**operationName**| 字串 | 活動的名稱及其狀態。 如果活動是啟動信號，則屬性值為`MyActivity -`。 如果活動是結束的信號，則屬性值為`MyActivity - Succeeded`。 | `MyActivity - Succeeded` |
|**pipelineName**| 字串 | 管線名稱。 | `MyPipeline` |
|**activityName**| 字串 | 活動名稱。 | `MyActivity` |
|**開始**| 字串 | 活動的開始時間是以 timespan UTC 格式執行。 | `2017-06-26T20:55:29.5007959Z`|
|**成品**| 字串 | 活動的結束時間會以 timespan UTC 格式執行。 如果診斷記錄顯示活動已啟動但尚未結束，則屬性值為`1601-01-01T00:00:00Z`。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>管線-執行記錄屬性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 屬性 | 類型 | 描述 | 範例 |
| --- | --- | --- | --- |
| **Level** |字串 | 診斷記錄的層級。 針對 [活動-執行記錄]，將屬性值設定為4。 | `4` |
| **correlationId** |字串 | 用於追蹤特定要求的唯一識別碼。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字串 | 以 timespan UTC 格式`YYYY-MM-DDTHH:MM:SS.00000Z`的事件時間。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| 字串| 管線執行的識別碼。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| 字串 | 與資料處理站資源相關聯的識別碼。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| 字串 | 診斷記錄的類別。 將屬性 (Property) 值設定為 `PipelineRuns`。 | `PipelineRuns` |
|**二級**| 字串 | 診斷記錄的層級。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**operationName**| 字串 | 管線的名稱及其狀態。 管線執行完成之後，屬性值為`Pipeline - Succeeded`。 | `MyPipeline - Succeeded`. |
|**pipelineName**| 字串 | 管線名稱。 | `MyPipeline` |
|**開始**| 字串 | 活動的開始時間是以 timespan UTC 格式執行。 | `2017-06-26T20:55:29.5007959Z`. |
|**成品**| 字串 | 活動的結束時間會以 timespan UTC 格式執行。 如果診斷記錄顯示活動已啟動但尚未結束，則屬性值為`1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| 字串 | 管線執行的最終狀態。 可能的屬性值`Succeeded`為`Failed`和。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>觸發程式-執行記錄屬性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| 屬性 | 類型 | 描述 | 範例 |
| --- | --- | --- | --- |
| **Level** |字串 | 診斷記錄的層級。 針對 [活動-執行記錄]，將屬性值設定為4。 | `4` |
| **correlationId** |字串 | 用於追蹤特定要求的唯一識別碼。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字串 | 以 timespan UTC 格式`YYYY-MM-DDTHH:MM:SS.00000Z`的事件時間。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| 字串| 觸發程式執行的識別碼。 | `08587023010602533858661257311` |
|**resourceId**| 字串 | 與資料處理站資源相關聯的識別碼。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| 字串 | 診斷記錄的類別。 將屬性 (Property) 值設定為 `PipelineRuns`。 | `PipelineRuns` |
|**二級**| 字串 | 診斷記錄的層級。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**operationName**| 字串 | 觸發程式的名稱，其最終狀態為，指出是否已成功引發觸發程式。 如果信號成功，則屬性值為`MyTrigger - Succeeded`。 | `MyTrigger - Succeeded` |
|**triggerName**| 字串 | 觸發程序的名稱。 | `MyTrigger` |
|**triggerType**| 字串 | 觸發程序的類型。 可能的屬性值`Manual Trigger`為`Schedule Trigger`和。 | `ScheduleTrigger` |
|**triggerEvent**| 字串 | 觸發程式的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**開始**| 字串 | 觸發程式的開始時間（以 timespan UTC 格式引發）。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| 字串 | 最後的狀態會顯示是否已成功引發觸發程式。 可能的屬性值`Succeeded`為`Failed`和。 | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics 架構

Log Analytics 會從 [監視] 繼承架構，但有下列例外狀況：

* 每個資料行名稱中的第一個字母都是大寫。 例如，監視器中的資料行名稱「correlationId」是 Log Analytics 中的「CorrelationId」。
* 沒有「層級」資料行。
* 動態的「屬性」資料行會保留為下列動態 JSON blob 類型。

    | Azure 監視器資料行 | Log Analytics 資料行 | 類型 |
    | --- | --- | --- |
    | $. 屬性。UserProperties | UserProperties | 動態 |
    | $. 屬性。備註 | 標註 | 動態 |
    | $. 屬性。源 | 輸入 | 動態 |
    | $. 屬性。輸出 | 輸出 | 動態 |
    | $. 屬性。錯誤。 errorCode | ErrorCode | int |
    | $. 屬性。錯誤。訊息 | ErrorMessage | 字串 |
    | $. 屬性。糾錯 | 錯誤 | 動態 |
    | $. 屬性。父 | 父 | 動態 |
    | $. 屬性。參數 | 參數 | 動態 |
    | $. 屬性。SystemParameters | SystemParameters | 動態 |
    | $. 屬性。標記 | Tags | 動態 |
    
## <a name="metrics"></a>計量

使用監視器，您可以查看 Azure 工作負載的效能和健康情況。 最重要的監視資料類型是度量，也稱為效能計數器。 計量是由大部分的 Azure 資源所發出。 [監視] 提供數種方式來設定和使用這些計量來進行監視和疑難排解。

Azure Data Factory 版本2會發出下列計量。

| **計量**           | **度量顯示名稱**         | **單位** | **匯總類型** | **說明**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | 成功的管線執行計量 | 計數    | 總計                | 在一分鐘時間範圍內成功執行的管線總次數。 |
| PipelineFailedRuns   | 失敗的管線執行計量    | 計數    | 總計                | 在一分鐘時間範圍內失敗的管線執行總數。    |
| ActivitySucceededRuns | 成功的活動執行計量 | 計數    | 總計                | 在一分鐘時間範圍內成功的活動執行總數。  |
| ActivityFailedRuns   | 失敗的活動執行計量    | 計數    | 總計                | 在一分鐘時間範圍內失敗的活動執行總數。     |
| TriggerSucceededRuns | 成功的觸發程序執行計量  | 計數    | 總計                | 在一分鐘時間範圍內成功的觸發程式執行總數。   |
| TriggerFailedRuns    | 失敗的觸發程序執行計量     | 計數    | 總計                | 在一分鐘時間範圍內失敗的觸發程式執行總數。      |

若要存取計量，請完成[Azure 監視器資料平臺](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)中的指示。

> [!NOTE]
> 只會發出已完成、已觸發的活動和管線執行事件。 進行中和沙箱/debug 回合**不**會發出。 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>使用 Azure 監視器監視 Data Factory 計量

您可以使用 Data Factory 與 Monitor 整合，將資料路由至監視器。 此整合在下列案例中很實用 ：

* 您想要在一組豐富的計量上撰寫複雜的查詢，由 Data Factory 所發行以進行監視。 您可以透過 [監視] 來建立這些查詢的自訂警示。

* 您想要監視所有資料處理站。 您可以將多個資料處理站的資料路由傳送到單一監視工作區。

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>設定診斷設定和工作區

建立或新增您 data factory 的診斷設定。

1. 在入口網站中，移至 [監視]。 選取 [**設定** > ] [**診斷設定**]。

1. 選取您要設定診斷設定的 data factory。

1. 如果選取的 data factory 上沒有任何設定，系統會提示您建立設定。 選取 [開啟診斷]****。

   ![如果沒有設定存在，請建立診斷設定](media/data-factory-monitor-oms/monitor-oms-image1.png)

   如果資料處理站上有現有的設定，您會看到已在 data factory 上設定的設定清單。 選取 [**新增診斷設定**]。

   ![如果設定存在，則新增診斷設定](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 為設定命名，選取 [**傳送至 Log analytics**]，然後從**Log analytics 工作區**中選取工作區。

    ![命名您的設定，並選取 log analytics 工作區](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. 選取 [儲存]  。

幾分鐘後，新的設定就會出現在此 data factory 的設定清單中。 一旦產生新的事件資料，診斷記錄就會串流處理到該工作區。 當事件發出時和記錄分析中出現時，可能會有最多15分鐘的時間。

* 在_資源特定_模式中，從 Azure Data Factory 流程到_ADFPipelineRun_、 _ADFTriggerRun_和_ADFActivityRun_資料表的診斷記錄
* 在 _Azure 診斷_模式中，診斷記錄會流入 _AzureDiagnostics_ 資料表

> [!NOTE]
> 因為 Azure 記錄資料表不能有超過500個數據行，所以強烈建議您選取 [資源特定模式]。 如需詳細資訊，請參閱[Log Analytics 已知限制](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)。

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>從 Azure Marketplace 安裝 Azure Data Factory Analytics

![移至 [Azure Marketplace]，輸入 [分析篩選器]，然後選取 [Azure Data Factory 分析（預覽）]](media/data-factory-monitor-oms/monitor-oms-image3.png)

![「Azure Data Factory 分析（預覽）」的詳細資料](media/data-factory-monitor-oms/monitor-oms-image4.png)

選取 [**建立**]，然後選取 [ **oms 工作區**] 和 [ **oms 工作區設定**]。

![建立新的解決方案](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>監視 Data Factory 計量

安裝 Azure Data Factory 分析會建立一組預設的視圖，以啟用下列計量：

- ADF 執行-1）依 Data Factory 的管線執行
 
- ADF 執行- 2) 依 Data Factory 的活動執行

- ADF 執行-3）依 Data Factory 的觸發程式執行

- ADF 錯誤-1）依 Data Factory 的前10個管線錯誤

- ADF 錯誤-2） Data Factory 前10個活動執行

- ADF 錯誤-3） Data Factory 前10個觸發程式錯誤

- ADF 統計資料-1）活動執行（依類型）

- ADF 統計資料-2）依類型的觸發程式執行

- ADF 統計資料-3）管線執行持續時間上限

![已反白顯示 [活頁簿（預覽）] 和 [AzureDataFactoryAnalytics] 的視窗](media/data-factory-monitor-oms/monitor-oms-image6.png)

您可以將上述計量視覺化、查看這些計量背後的查詢、編輯查詢、建立警示，以及採取其他動作。

![依 data factory 的管線執行圖形標記法](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory 分析（預覽）會將診斷記錄傳送至_資源特定的_目的地資料表。 您可以針對下列資料表撰寫查詢： _ADFPipelineRun_、 _ADFTriggerRun_和_ADFActivityRun_。

## <a name="alerts"></a>警示

登入 Azure 入口網站並選取 [**監視** > **警示**] 以建立警示。

![入口網站功能表中的警示](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>建立警示

1. 選取 [+ 新增警示規則]**** 來新建警示。

    ![新增警示規則](media/monitor-using-azure-monitor/alerts_image4.png)

1. 定義警示條件。

    > [!NOTE]
    > 請務必在 [**依資源類型篩選**] 下拉式清單中選取 [**全部**]。

    ![[定義警示條件] > [選取目標]，這會開啟 [選取資源] 窗格 ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![[定義警示條件] > [新增條件]，這會開啟 [設定信號邏輯] 窗格](media/monitor-using-azure-monitor/alerts_image6.png)

    ![[設定信號類型] 窗格](media/monitor-using-azure-monitor/alerts_image7.png)

1. 定義警示詳細資料。

    ![警示詳細資料](media/monitor-using-azure-monitor/alerts_image8.png)

1. 定義動作群組。

    ![建立規則，並反白顯示 [新增動作群組]](media/monitor-using-azure-monitor/alerts_image9.png)

    ![建立新的動作群組](media/monitor-using-azure-monitor/alerts_image10.png)

    ![設定電子郵件、SMS、push 和 voice](media/monitor-using-azure-monitor/alerts_image11.png)

    ![定義動作群組](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>後續步驟
[以程式設計方式監視和管理管線](monitor-programmatically.md)
