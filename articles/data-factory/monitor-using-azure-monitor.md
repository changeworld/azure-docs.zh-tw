---
title: 使用 Azure 監視器來監視 Data Factory
description: 瞭解如何使用 Azure 監視器使用數據工廠的資訊啟用診斷日誌來監視 /Azure 數據工廠管道。
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419454"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>使用 Azure 監視器對資料工廠發出警報和監視

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

雲應用程式非常複雜,並且有許多移動部件。 監視器提供數據,以幫助確保應用程式處於正常運行狀態。 監視器還可以説明您避免潛在問題並排除過去的故障。

您可以使用監視數據深入瞭解您的應用程式。 這些知識可説明您提高應用程式性能和可維護性。 它還可以説明您自動執行需要手動干預的操作。

Azure 監視器為大多數 Azure 服務提供基本級別的基礎結構指標和日誌。 Azure 診斷日誌由資源發出,並提供有關該資源操作的豐富、頻繁的數據。 Azure 數據工廠在監視器中寫入診斷日誌。

有關詳細資訊,請參閱[Azure 監視器概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。

## <a name="keeping-azure-data-factory-data"></a>保留 Azure 資料工廠資料

數據工廠僅存儲管道運行的數據僅 45 天。 如果要將數據保留更長時間,請使用 Monitor。 使用 Monitor,您可以路由診斷日誌進行分析。 您還可以將它們保存在儲存帳戶中,以便獲得所選持續時間的工廠資訊。

## <a name="diagnostic-logs"></a>診斷記錄

* 將診斷日誌保存到存儲帳戶,以便進行審核或手動檢查。 您可以使用診斷設置指定保留時間(以天計)。
* 將日誌流式傳輸到 Azure 事件中心。 日誌將成為合作夥伴服務或自定義分析解決方案(如 Power BI)的輸入。
* 使用日誌分析分析日誌。

您可以使用不在發出日誌的資源的訂閱中的存儲帳戶或事件中心命名空間。 配置設置的用戶必須對兩個訂閱具有適當的基於角色的訪問控制 (RBAC) 訪問許可權。

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄

### <a name="diagnostic-settings"></a>診斷設定

使用診斷設置為非計算資源配置診斷日誌。 資源控制項設定有以下功能:

* 它們指定診斷日誌的發送位置。 範例包括 Azure 儲存帳戶、Azure 事件中心或監視器日誌。
* 它們指定發送的日誌類別。
* 它們指定每個日誌類別應保留在存儲帳戶中的時間。
* 保留期為 0 天表示會永遠保留記錄。 否則,該值可以是從 1 到 2,147,483,647 的任何天數。
* 如果設置了保留策略,但禁用了存儲帳戶中的日誌存儲策略,則保留策略不起作用。 例如,當僅選擇事件中心或監視器日誌選項時,可能會發生此情況。
* 每天應用保留策略。 日之間的邊界發生在午夜協調通用時間 (UTC)。 在一天結束時,將刪除超出保留策略的天數的日誌。 例如,如果您有一天的保留策略,則從今天開始,昨天之前的日誌將被刪除。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>透過 Azure 監視器 REST API 啟用診斷紀錄

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在監視器 REST API 中建立或更新診斷設定

##### <a name="request"></a>要求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* 將 `{api-version}` 取代為 `2016-09-01`。
* 替換為`{resource-id}`要為其編輯診斷設置的資源的識別碼。 有關詳細資訊,請參閱[使用資源群組來管理 Azure 資源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 將 `Content-Type` 標頭設定為 `application/json`。
* 將授權標頭設置為從 Azure 活動目錄 (Azure AD) 獲得的 JSON Web 權杖。 有關詳細資訊,請參閱[身份認證請求](../active-directory/develop/authentication-scenarios.md)。

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
| **storageAccountId** |String | 要向其發送診斷日誌的存儲帳戶的資源 ID。 |
| **serviceBusRuleId** |String | 服務總線命名空間的服務總線規則 ID,您希望在其中為流式診斷日誌創建事件中心。 規則識別碼`{service bus resource ID}/authorizationrules/{key name}`具有格式。|
| **工作區 Id** | 複雜類型 | 一系列指標時粒及其保留策略。 此屬性的值為空。 |
|**指標**| 要傳遞給已叫用之管線的管線執行參數值| 將參數名稱映射到參數值的 JSON 物件。 |
| **紀錄**| 複雜類型| 資源類型的診斷日誌類別的名稱。 要獲取資源的診斷日誌類別清單,請執行 GET 診斷設置操作。 |
| **類別**| String| 日誌類別及其保留策略的陣列。 |
| **時間格雷** | String | 以 ISO 8601 持續時間格式捕獲的指標粒度。 屬性值必須為`PT1M`指定一分鐘。 |
| **開啟**| Boolean | 指定是否為此資源啟用了指標或日誌類別的集合。 |
| **retentionPolicy**| 複雜類型| 描述計量或記錄類別的保留原則。 此屬性僅用於存儲帳戶。 |
|**天**| Int| 保留指標或日誌的天數。 如果屬性值為 0,則日誌將永久保留。 此屬性僅用於存儲帳戶。 |

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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>取得有關監視器 REST API 中的診斷設定的資訊

##### <a name="request"></a>要求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* 將 `{api-version}` 取代為 `2016-09-01`。
* 替換為`{resource-id}`要為其編輯診斷設置的資源的識別碼。 有關詳細資訊,請參閱[使用資源群組來管理 Azure 資源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 將 `Content-Type` 標頭設定為 `application/json`。
* 將授權標頭設置為從 Azure AD 獲得的 JSON Web 權杖。 有關詳細資訊,請參閱[身份認證請求](../active-directory/develop/authentication-scenarios.md)。

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
有關詳細資訊,請參閱[診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)。

## <a name="schema-of-logs-and-events"></a>紀錄與事件架構

### <a name="monitor-schema"></a>監察架構

#### <a name="activity-run-log-attributes"></a>活動執行紀錄屬性

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
| **層級** |String | 診斷日誌的級別。 對於活動運行日誌,將屬性值設置為 4。 | `4` |
| **相關性Id** |String | 用於追蹤特定請求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 時間跨度 UTC`YYYY-MM-DDTHH:MM:SS.00000Z`格式的事件的時間。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 活動的 ID 運行。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 管道的識別碼運行。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**資源代碼**| String | 與數據工廠資源關聯的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| String | 診斷日誌的類別。 將屬性 (Property) 值設定為 `ActivityRuns`。 | `ActivityRuns` |
|**水平**| String | 診斷日誌的級別。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**操作名稱**| String | 具有其狀態的活動的名稱。 如果作用時會開始偵測訊號,則`MyActivity -`屬性值為 。 如果作用時是結束偵測訊號,則`MyActivity - Succeeded`屬性值為 。 | `MyActivity - Succeeded` |
|**pipelineName**| String | 管線名稱。 | `MyPipeline` |
|**activityName**| String | 活動名稱。 | `MyActivity` |
|**開始**| String | 活動的開始時間以時間跨度 UTC 格式運行。 | `2017-06-26T20:55:29.5007959Z`|
|**結束**| String | 活動的結束時間以時間跨度 UTC 格式運行。 如果診斷紀錄顯示活動已啟動但尚未結束,則屬性值為`1601-01-01T00:00:00Z`。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>導管執行紀錄屬性

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
| **層級** |String | 診斷日誌的級別。 對於活動運行日誌,將屬性值設置為 4。 | `4` |
| **相關性Id** |String | 用於追蹤特定請求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 時間跨度 UTC`YYYY-MM-DDTHH:MM:SS.00000Z`格式的事件的時間。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| 管道的識別碼運行。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**資源代碼**| String | 與數據工廠資源關聯的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| String | 診斷日誌的類別。 將屬性 (Property) 值設定為 `PipelineRuns`。 | `PipelineRuns` |
|**水平**| String | 診斷日誌的級別。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**操作名稱**| String | 管道的名稱及其狀態。 管線執行完成後,屬性值`Pipeline - Succeeded`為 。 | `MyPipeline - Succeeded`. |
|**pipelineName**| String | 管線名稱。 | `MyPipeline` |
|**開始**| String | 活動的開始時間以時間跨度 UTC 格式運行。 | `2017-06-26T20:55:29.5007959Z`. |
|**結束**| String | 活動的結束時間以時間跨度 UTC 格式運行。 如果診斷紀錄顯示活動已啟動但尚未結束,則屬性值為`1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|**狀態**| String | 管道運行的最終狀態。 可能的屬性值是`Succeeded``Failed`與 。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>觸發器執行紀錄屬性

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
| **層級** |String | 診斷日誌的級別。 對於活動運行日誌,將屬性值設置為 4。 | `4` |
| **相關性Id** |String | 用於追蹤特定請求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 時間跨度 UTC`YYYY-MM-DDTHH:MM:SS.00000Z`格式的事件的時間。 | `2017-06-28T21:00:27.3534352Z` |
|**觸發器 Id**| String| 運行觸發器的 ID。 | `08587023010602533858661257311` |
|**資源代碼**| String | 與數據工廠資源關聯的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| String | 診斷日誌的類別。 將屬性 (Property) 值設定為 `PipelineRuns`。 | `PipelineRuns` |
|**水平**| String | 診斷日誌的級別。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**操作名稱**| String | 具有最終狀態的觸發器的名稱,指示觸發器是否成功觸發。 如果偵測訊號成功,則屬性`MyTrigger - Succeeded`值為 。 | `MyTrigger - Succeeded` |
|**觸發器名稱**| String | 觸發程序的名稱。 | `MyTrigger` |
|**觸發器類型**| String | 觸發程序的類型。 可能的屬性值是`Manual Trigger``Schedule Trigger`與 。 | `ScheduleTrigger` |
|**triggerEvent**| String | 觸發器的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**開始**| String | 以時間跨度 UTC 格式觸發觸發器的開始時間。 | `2017-06-26T20:55:29.5007959Z`|
|**狀態**| String | 顯示觸發器是否成功觸發的最終狀態。 可能的屬性值是`Succeeded``Failed`與 。 | `Succeeded`|

### <a name="log-analytics-schema"></a>紀錄分析架構

日誌分析從監視器繼承架構,但有以下例外情況:

* 每個列名稱中的第一個字母大寫。 例如,監視器中的列名稱「關聯 Id」在日誌分析中為「關聯 Id」。。
* 沒有"級別"列。
* 動態「屬性」列將保留為以下動態 JSON blob 類型。

    | Azure 監視器列 | 紀錄分析欄 | 類型 |
    | --- | --- | --- |
    | $.屬性。使用者屬性 | UserProperties | 動態 |
    | $.屬性。註解 | 註解 | 動態 |
    | $.屬性。輸入 | 輸入 | 動態 |
    | $.屬性。輸出 | 輸出 | 動態 |
    | $.屬性。錯誤代碼 | ErrorCode | int |
    | $.屬性。錯誤.訊息 | ErrorMessage | 字串 |
    | $.屬性。錯誤 | 錯誤 | 動態 |
    | $.屬性。前輩 | 前輩 | 動態 |
    | $.屬性。參數 | 參數 | 動態 |
    | $.屬性。系統參數 | SystemParameters | 動態 |
    | $.屬性。標籤 | Tags | 動態 |
    
## <a name="metrics"></a>計量

使用 Monitor,您可以瞭解 Azure 工作負荷的性能和運行狀況。 Monitor 資料最重要的類型是指標,也稱為性能計數器。 指標由大多數 Azure 資源發出。 監視器提供了幾種配置和使用這些指標以進行監視和故障排除的方法。

Azure 數據工廠版本 2 發出以下指標。

| **計量**           | **指標顯示名稱**         | **單位** | **彙總型態** | **說明**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | 成功的管線執行計量 | Count    | 總計                | 在一分鐘內成功運行的管道總數。 |
| PipelineFailedRuns   | 失敗的管線執行計量    | Count    | 總計                | 在一分鐘內失敗的管道運行的總數。    |
| ActivitySucceededRuns | 成功的活動執行計量 | Count    | 總計                | 在一分鐘內成功運行的活動總數。  |
| ActivityFailedRuns   | 失敗的活動執行計量    | Count    | 總計                | 在一分鐘內失敗的活動總數。     |
| TriggerSucceededRuns | 成功的觸發程序執行計量  | Count    | 總計                | 在一分鐘內成功運行的觸發器總數。   |
| TriggerFailedRuns    | 失敗的觸發程序執行計量     | Count    | 總計                | 在一分鐘內失敗的觸發器運行總數。      |

要訪問指標,在[Azure 監視器數據平臺](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)中完成說明。

> [!NOTE]
> 僅發出已完成、觸發的活動和管道運行事件。 正在進行,**不會**發出沙箱/調試運行。 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>使用 Azure 監視器監視資料工廠指標

您可以使用與監視器的資料工廠整合將數據路由到監視器。 此整合在下列案例中很實用 ：

* 您希望在數據工廠到監視器發佈的大量指標上編寫複雜的查詢。 您可以通過 Monitor 在這些查詢上創建自訂警報。

* 您想要監視所有資料處理站。 您可以將資料從多個資料工廠路由到單個監視器工作區。

如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>設定診斷設定與工作區

為資料工廠創建或添加診斷設置。

1. 在門戶中,轉到監視器。 選擇 **「設定** > **診斷設定**」 。

1. 選擇要為其設置診斷設置的數據工廠。

1. 如果選取的資料工廠上不存在設定,系統將提示您建立設定。 選取 [開啟診斷]****。

   ![如果沒有設定,則建立診斷設定](media/data-factory-monitor-oms/monitor-oms-image1.png)

   如果數據工廠上存在現有設置,則會看到數據工廠上已配置的設置清單。 選擇 **「添加診斷設置**」。

   ![如果存在設定,則新增診斷設定](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 為設置指定名稱,選擇 **「發送到日誌分析**」,然後從**日誌分析工作區**中選擇工作區。

    ![具名設定並選擇紀錄分析工作區](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. 選取 [儲存]  。

幾分鐘后,新設置將顯示在此數據工廠的設置清單中。 生成新事件數據後,診斷日誌將立即流式傳輸到該工作區。 在發出事件和在日誌分析中顯示事件之間,最多 15 分鐘可能會消失。

* 在_特定於資源的_模式下,Azure 資料工廠的診斷日誌將流入_ADFPipelineRun、ADF__觸發器執行_和_ADFActivityRun_表
* 在 _Azure 診斷_模式中，診斷記錄會流入 _AzureDiagnostics_ 資料表

> [!NOTE]
> 由於 Azure 日誌表的列數不能超過 500 列,因此強烈建議您選擇特定於資源的模式。 有關詳細資訊,請參閱[日誌分析已知限制](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)。

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>從 Azure Marketplace 安裝 Azure Data Factory Analytics

![轉到「Azure 應用商店」,輸入「分析篩選器」,然後選擇「Azure 數據工廠分析(預覽)」)](media/data-factory-monitor-oms/monitor-oms-image3.png)

![有關「Azure 資料工廠分析(預覽)」的詳細資訊](media/data-factory-monitor-oms/monitor-oms-image4.png)

選擇 **'建立**',然後選擇**OMS 工作區**與**OMS 工作區設定**。

![建立新解決方案](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>監察資料工廠指標

安裝 Azure 資料工廠分析將建立預設的檢視,以便啟用以下指標:

- ADF 執行 - 1) 依資料工廠執行導管
 
- ADF 執行- 2) 依 Data Factory 的活動執行

- ADF 執行 - 3) 按資料工廠觸發執行

- ADF 錯誤 - 1) 資料工廠前 10 個導管錯誤

- ADF 錯誤 - 2) 資料工廠執行的前 10 個活動

- ADF 錯誤 - 3) 資料工廠前 10 個觸發錯誤

- ADF 統計 - 1) 依類型執行活動

- ADF 統計 - 2) 依類型執行觸發器

- ADF 統計 - 3) 最大導管執行持續時間

![突顯工作簿(預覽)「Azure 資料工廠分析」的視窗](media/data-factory-monitor-oms/monitor-oms-image6.png)

您可以可視化上述指標、查看這些指標後面的查詢、編輯查詢、創建警報以及執行其他操作。

![數據工廠運行的管道的圖形表示"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure 資料工廠分析(預覽)將診斷日誌發送到_特定於資源_的目標表。 您可以針對下表編寫查詢 _:ADFPipelineRun、ADF__觸發器執行_與_ADFActivityRun_。

## <a name="alerts"></a>警示

登錄到 Azure 門戶並選擇 **「監視** > **警報」** 以建立警報。

![入口網站功能表中的警示](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>建立警示

1. 選取 [+ 新增警示規則]**** 來新建警示。

    ![新增警示規則](media/monitor-using-azure-monitor/alerts_image4.png)

1. 定義警報條件。

    > [!NOTE]
    > 請確保在 **「按資源類型」** 下拉清單中選擇「**篩選器中的所有全部**」。

    !["定義警報條件">"選擇目標",該"選擇目標"將打開"選擇資源"窗格 ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["定義警報準則">"添加條件",這將開啟「設定信號邏輯」窗格](media/monitor-using-azure-monitor/alerts_image6.png)

    ![設定訊號型態 "窗格](media/monitor-using-azure-monitor/alerts_image7.png)

1. 定義警示詳細資料。

    ![警示詳細資料](media/monitor-using-azure-monitor/alerts_image8.png)

1. 定義操作組。

    ![建立突顯新操作群組的規則](media/monitor-using-azure-monitor/alerts_image9.png)

    ![建立新的動作群組](media/monitor-using-azure-monitor/alerts_image10.png)

    ![設定電子郵件、簡訊、推送和語音](media/monitor-using-azure-monitor/alerts_image11.png)

    ![定義操作群組](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>後續步驟
[以程式設計方式監控及管理導管](monitor-programmatically.md)
