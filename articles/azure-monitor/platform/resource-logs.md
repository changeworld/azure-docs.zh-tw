---
title: Azure 資源記錄
description: 瞭解如何在 Azure 監視器中將 Azure 資源記錄串流至 Log Analytics 工作區。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: a2f46440a4214e298bc6d2f3b9c2b5680437ead7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522697"
---
# <a name="azure-resource-logs"></a>Azure 資源記錄
Azure 資源記錄是 [平臺記錄](platform-logs-overview.md) ，可讓您深入瞭解在 Azure 資源中執行的作業。 資源記錄的內容會依 Azure 服務和資源類型而有所不同。 預設不會收集資源記錄。 您必須為每個 Azure 資源建立診斷設定，以將其資源記錄傳送至 Log Analytics 工作區，以搭配 [Azure 監視器記錄](data-platform-logs.md)、Azure 事件中樞在 Azure 外部轉送，或用於封存的 Azure 儲存體。

請參閱 [建立診斷設定以將平臺記錄和計量傳送至不同的目的地](diagnostic-settings.md) ，以取得有關建立診斷設定的詳細資料，並 [使用 Azure 原則來大規模部署 Azure 監視器](../deploy-scale.md) 的詳細資訊，請參閱使用 Azure 原則自動為您建立的每個 Azure 資源建立診斷設定。

## <a name="send-to-log-analytics-workspace"></a>傳送至 Log Analytics 工作區
 將資源記錄傳送至 Log Analytics 工作區，以啟用 [Azure 監視器記錄](data-platform-logs.md) 的功能，包括下列各項：

- 將資源記錄資料與 Azure 監視器所收集的其他監視資料相互關聯。
- 將來自多個 Azure 資源、訂用帳戶和租使用者的記錄專案合併至一個位置，以便一起分析。
- 使用記錄查詢來執行複雜的分析，並深入瞭解記錄資料。
- 使用具有複雜警示邏輯的記錄警示。

[建立診斷設定](diagnostic-settings.md) ，以將資源記錄傳送至 Log Analytics 工作區。 這項資料會儲存在資料表中，如 [Azure 監視器記錄結構](./data-platform-logs.md)中所述。 資源記錄所使用的資料表取決於資源所使用的集合類型：

- Azure 診斷-所有寫入的資料都是 _AzureDiagnostics_ 資料表。
- 資源特有的資料會針對資源的每個類別目錄寫入個別的資料表。

### <a name="azure-diagnostics-mode"></a>Azure 診斷模式 
在此模式中，會在 _AzureDiagnostics_ 資料表中收集來自任何診斷設定的所有資料。 這是現今大部分 Azure 服務所使用的舊版方法。 由於多個資源類型會將資料傳送到相同的資料表，因此其架構為所收集之所有不同資料類型之架構的超集合。

請考慮下列範例，其中會針對下列資料類型在相同的工作區中收集診斷設定：

- 服務1的審核記錄 (具有由 A、B 和 C 資料行組成的架構)   
- 服務1的錯誤記錄 (具有由 D、E 和 F 資料行所組成的架構)   
- Service 2 的 Audit 記錄 (具有由 G、H 和 a 資料行所組成的架構)   

AzureDiagnostics 資料表看起來會像這樣：  

| ResourceProvider    | 類別     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j3 接 | k3 | l3 |
| Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>特定資源
在此模式中，會為在 [診斷] 設定中選取的每個類別建立所選工作區中的個別資料表。 建議使用此方法，因為它可讓您更輕鬆地在記錄查詢中使用資料、提供更好的架構和其結構探索、改善內嵌延遲和查詢時間的效能，以及在特定資料表上授與 Azure RBAC 許可權的能力。 所有 Azure 服務最終都會遷移至 Resource-Specific 模式。 

上述範例會產生三個數據表：
 
- 表格 *Service1AuditLogs* 如下：

    | 資源提供者 | 類別 | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- 表格 *Service1ErrorLogs* 如下：  

    | 資源提供者 | 類別 | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- 表格 *Service2AuditLogs* 如下：  

    | 資源提供者 | 類別 | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 接 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>選取收集模式
大部分的 Azure 資源會將資料以 **Azure 診斷** 或 **資源特定模式** 寫入工作區，而不會提供您選擇。 請參閱 [每個服務的檔](./resource-logs-schema.md) ，以取得其所使用之模式的詳細資料。 所有 Azure 服務最終都會使用 Resource-Specific 模式。 在這項轉換過程中，某些資源可讓您在診斷設定中選取模式。 針對任何新的診斷設定指定資源特定模式，因為這可讓資料更容易管理，並可協助您避免在日後進行複雜的遷移。
  
   ![診斷設定模式選取器](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)

> [!NOTE]
> 如需使用 resource manager 範本設定收集模式的範例，請參閱 [Azure 監視器中的診斷設定 Resource Manager 範本範例](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault)。


您可以將現有的診斷設定修改為資源特定模式。 在此情況下，已收集的資料將會保留在 _AzureDiagnostics_ 資料表中，直到根據您的工作區保留設定移除為止。 系統會在專用資料表中收集新的資料。 使用 [union](/azure/kusto/query/unionoperator) 運算子來查詢兩個數據表之間的資料。

繼續觀看 [Azure 更新](https://azure.microsoft.com/updates/) 的 blog，以取得支援 Resource-Specific 模式之 Azure 服務的相關公告。

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics 中的資料行限制
Azure 監視器記錄中的任何資料表都有500屬性限制。 一旦達到此限制，就會在內嵌階段卸載任何包含前500以外之任何屬性之資料的資料列。 *AzureDiagnostics* 資料表特別容易受到這項限制，因為它包含所有寫入至該資料表之 Azure 服務的屬性。

如果您要從多個服務收集資源記錄， _AzureDiagnostics_ 可能會超過此限制，而且資料將會遺失。 在所有 Azure 服務都支援資源特定模式之前，您應該設定資源以寫入至多個工作區，以降低達到500資料行限制的可能性。

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory，因為有一組詳細的記錄，所以已知寫入大量資料行，而且可能會導致 _AzureDiagnostics_ 超過其限制的服務。 針對在啟用資源特定模式之前設定的任何診斷設定，將會針對任何活動，為每個唯一名稱的使用者參數建立新的資料行。 因為活動輸入和輸出的詳細特性，所以會建立更多資料行。
 
您應該儘快遷移記錄，以使用資源特定模式。 如果您無法立即這麼做，暫時的替代方法是將 Azure Data Factory 記錄隔離到它們自己的工作區，以最小化這些記錄檔影響在您工作區中收集的其他記錄類型。


## <a name="send-to-azure-event-hubs"></a>傳送給 Azure 事件中樞
將資源記錄傳送至事件中樞，以將其傳送至 Azure 外部，例如協力廠商 SIEM 或其他 log analytics 解決方案。 事件中樞的資源記錄會以 JSON 格式取用，其中 `records` 包含每個承載中記錄的元素。 架構視資源類型而定，如 [Azure 資源記錄的一般和服務特定架構](resource-logs-schema.md)中所述。

以下是來自資源記錄檔之事件中樞的範例輸出資料：

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
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
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
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
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
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

## <a name="send-to-azure-storage"></a>傳送到 Azure 儲存體
將資源記錄檔傳送至 Azure 儲存體，以保留該記錄以供保存。 一旦您建立了診斷設定，在其中一個已啟用的記錄類別中發生事件時，就會在儲存體帳戶中建立儲存體容器。 容器內的 blob 會使用下列命名慣例：

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，網路安全性群組的 blob 可能會有類似以下的名稱：

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

每個 PT1H.json blob 有一個 JSON blob，包含在 blob URL 指定時數內 (例如 h = 12) 發生的事件。 在目前這一小時，事件一發生就會附加到 PT1H.json 檔案。  (m = 00) 的分鐘值一律為00，因為資源記錄檔事件會分成每小時的個別 blob。

在 PT1H.js檔案中，每個事件會以下列格式儲存。 這將會使用通用的最上層架構，但在 [資源記錄架構](./resource-logs-schema.md)中所述的每個 Azure 服務都是唯一的。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 平臺記錄會使用 [JSON 行](http://jsonlines.org/)寫入至 blob 儲存體，其中每個事件都是一行，而分行符號表示新事件。 此格式已于2018年11月執行。 在此日期之前，記錄會以 json 記錄檔的形式寫入 blob 儲存體，如在封存 [至儲存體帳戶的 Azure 監視器平臺記錄檔的「準備進行格式變更](resource-logs-blob-format.md)」中所述。


## <a name="next-steps"></a>後續步驟

* [深入瞭解資源記錄](platform-logs-overview.md)。
* [建立診斷設定，以將平臺記錄和計量傳送至不同的目的地](diagnostic-settings.md)。