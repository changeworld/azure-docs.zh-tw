---
title: 管理解決方案中儲存的搜尋 |Microsoft Docs
description: 管理解決方案通常包含已儲存的記錄查詢，以分析解決方案所收集的資料。 本文說明如何在 Resource Manager 範本中定義 Log Analytics 儲存的搜尋。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663023"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>將 Log Analytics 儲存的搜尋和警示新增到管理解決方案 (預覽)

> [!IMPORTANT]
> 如[先前所宣佈](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)，在*2019 年6月 1*日之後建立的 log analytics 工作區，將**只能使用 Azure** ScheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)、 [Azure Resource Manager 範本](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template)和[PowerShell Cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell)來管理警示規則。 客戶可以輕鬆地切換較舊工作區的[警示規則管理慣用方式](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)，利用 Azure 監視器 scheduledQueryRules 做為預設值，並取得許多[新的優點](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api)，例如使用原生 PowerShell Cmdlet、增加規則中的回顧時間週期、在個別資源群組或訂用帳戶中建立規則，以及更多其他功能。

> [!NOTE]
> 這是建立管理解決方案 (目前處於預覽狀態) 的預備文件。 以下所述的任何結構描述可能會有所變更。

[管理解決方案](solutions.md)通常會包含 Log Analytics 中[儲存的搜尋](../../azure-monitor/log-query/log-query-overview.md)，來分析解決方案所收集的資料。 它們可能也會定義[警示](../../azure-monitor/platform/alerts-overview.md)來通知使用者，或自動採取動作以回應重大的問題。 本文說明如何在[資源範本範本](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)中定義 Log Analytics 儲存的搜尋與警示，讓它們能夠包含於[管理解決方案](solutions-creating.md)中。

> [!NOTE]
> 本文中的範例使用管理解決方案所需或通用的參數和變數，如[在 Azure 中設計和建置管理解決方案](solutions-creating.md)所述。

## <a name="prerequisites"></a>必要條件
本文假設您已經熟悉如何[建立管理解決方案](solutions-creating.md)，以及 [Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)和解決方案檔案的結構。


## <a name="log-analytics-workspace"></a>Log Analytics 工作區
Log Analytics 中的所有資源都包含於[工作區](../../azure-monitor/platform/manage-access.md)中。 如 [Log Analytics 工作區和自動化帳戶](solutions.md#log-analytics-workspace-and-automation-account)所述，工作區不會包含於管理解決方案中，但在安裝解決方案前就必須存在。 如果無法使用，則解決方案安裝會失敗。

工作區的名稱位於每個 Log Analytics 資源的名稱中。 這可在解決方案中使用**工作區**參數來完成，如下列 SavedSearch 資源範例所示。

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API 版本
Resource Manager 範本中所定義的所有 Log Analytics 資源都會有 **apiVersion** 屬性，以定義資源應該使用的 API 版本。

下表列出此範例中使用的資源 API 版本。

| 資源類型 | API 版本 | 查詢 |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>儲存的搜尋
在解決方案中包含[儲存的搜尋](../../azure-monitor/log-query/log-query-overview.md)，可讓使用者查詢您解決方案所收集的資料。 儲存的搜尋會出現在 Azure 入口網站的 [儲存的搜尋] 下方。 每個警示也會需要儲存的搜尋。

[Log Analytics 儲存的搜尋](../../azure-monitor/log-query/log-query-overview.md)資源都具有 `Microsoft.OperationalInsights/workspaces/savedSearches` 類型，並具備下列結構。 這包括一般變數和參數，因此您可以將此程式碼片段複製並貼到您的解決方案檔，然後變更參數名稱。

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

下表說明儲存的搜尋的每個屬性。

| 屬性 | 描述 |
|:--- |:--- |
| category | 儲存的搜尋的類別。  同一個解決方案中所有儲存的搜尋通常都會共用單一類別，因此它們會在主控台中群組在一起。 |
| displayname | 要在入口網站中顯示之儲存的搜尋名稱。 |
| query | 要執行的查詢。 |

> [!NOTE]
> 如果查詢包含可解譯為 JSON 的字元，您可能需要在查詢中使用逸出字元。 例如，如果查詢為 **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"** ，就應該在方案檔中撰寫為 **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"** 。

## <a name="alerts"></a>警示
[Azure 記錄警示](../../azure-monitor/platform/alerts-unified-log.md)是由 Azure 警示規則所建立，以定期執行指定的記錄查詢。 如果查詢的結果符合指定的準則，就會建立警示記錄，並使用[動作群組](../../azure-monitor/platform/action-groups.md)執行一或多個動作。

針對將警示延伸至 Azure 的使用者，現在便會以 Azure 動作群組來控制動作。 將工作區及其警示延伸至 Azure 之後，您便可使用[動作群組 - Azure Resource Manager 範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)來擷取或新增動作。
舊版管理解決方案中的警示規則是由下列三個不同的資源所組成。

- **儲存的搜尋。** 定義所執行的記錄搜尋。 多個警示規則可以共用單一儲存的搜尋。
- **排程。** 定義記錄搜尋的執行頻率。 每個警示規則都只能有一個排程。
- **警示動作。** 每個警示規則都會有一個具有一種**警示**類型的動作群組資源或動作資源 (舊版)，該類型會定義警示的詳細資料，像是建立警示記錄的時機及警示的嚴重性等準則。 [動作群組](../../azure-monitor/platform/action-groups.md)資源可以有一份設定來要在引發警示時採取的動作清單，例如，語音電話、SMS、電子郵件、Webhook、ITSM 工具、自動化 Runbook、邏輯應用程式等等。

儲存的搜尋資源如上所述。 其他資源將在後續內容中加以說明。

### <a name="schedule-resource"></a>排程資源

儲存的搜尋可以有一或多個排程，其中每個排程均代表不同的警示規則。 排程會定義搜尋的執行頻率，以及擷取資料的時間間隔。 排程資源具有 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` 類型，並具備下列結構。 這包括一般變數和參數，因此您可以將此程式碼片段複製並貼到您的解決方案檔，然後變更參數名稱。

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
下表會說明排程資源的屬性。

| 元素名稱 | 必要項 | 描述 |
|:--|:--|:--|
| 已啟用       | 是 | 指定在建立警示時是否要加以啟用。 |
| interval      | 是 | 查詢的執行頻率，以分鐘為單位。 |
| queryTimeSpan | 是 | 評估結果的時間長度，以分鐘為單位。 |

排程資源應該相依於儲存的搜尋，如此就會在排程之前建立該資源。
> [!NOTE]
> 排程名稱在指定工作區中必須是唯一的；兩個排程即使已儲存的相關聯搜尋不同，也不能有相同的識別碼。 此外，使用 Log Analytics API 建立並儲存的所有搜尋、排程和動作，都必須使用小寫名稱。

### <a name="actions"></a>動作
一個排程可以有多個動作。 一個動作可能定義一或多個處理序來執行，例如傳送郵件或啟動 Runbook，或也可能定義臨界值來判斷搜尋結果是否符合某些準則。 某些動作會同時定義這兩者，以便符合臨界值時執行處理序。
動作可以使用 [動作群組] 資源或動作資源來定義。

**Type** 屬性會指定兩種類型的動作資源。 排程需要一個**警示**動作，其會定義警示規則的詳細資料，以及建立警示時要採取哪些動作。 動作資源具有 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions` 類型。

警示動作具備下列結構。 這包括一般變數和參數，因此您可以將此程式碼片段複製並貼到您的解決方案檔，然後變更參數名稱。

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

下表會說明警示動作資源的屬性。

| 元素名稱 | 必要項 | 描述 |
|:--|:--|:--|
| `type` | 是 | 動作的類型。  這是適用於警示動作的**警示**。 |
| `name` | 是 | 警示的顯示名稱。  這是顯示於主控台中的警示規則名稱。 |
| `description` | 否 | 警示的選擇性描述。 |
| `severity` | 是 | 警示記錄的嚴重性有下列值：<br><br> **critical**<br>**warning**<br>**informational**

#### <a name="threshold"></a>閾值
此為必要區段。 它會定義警示臨界值的屬性。

| 元素名稱 | 必要項 | 描述 |
|:--|:--|:--|
| `Operator` | 是 | 比較運算子具有下列值：<br><br>**gt = 大於<br>lt = 少於** |
| `Value` | 是 | 要比較結果的值。 |

##### <a name="metricstrigger"></a>MetricsTrigger
此為選擇性區段。 加入此區段以供計量計量警示使用。

| 元素名稱 | 必要項 | 描述 |
|:--|:--|:--|
| `TriggerCondition` | 是 | 使用下列值來指定臨界值為違反次數總和或連續違反次數：<br><br>**Total<br>Consecutive** |
| `Operator` | 是 | 比較運算子具有下列值：<br><br>**gt = 大於<br>lt = 少於** |
| `Value` | 是 | 必須符合準則以觸發警示的次數。 |


#### <a name="throttling"></a>節流
此為選擇性區段。 如果您想要在建立警示之後的某一段時間內隱藏相同規則所產生的警示，請加入此區段。

| 元素名稱 | 必要項 | 描述 |
|:--|:--|:--|
| DurationInMinutes | 如果包含 Throttling 元素，即為 Yes | 從同一個警示規則中建立一個警示之後隱藏警示的分鐘數。 |

#### <a name="azure-action-group"></a>Azure 動作群組
Azure 中的所有警示都使用「動作群組」作為處理動作的預設機制。 使用「動作群組」時，您可以指定您的動作一次，然後將動作群組與整個 Azure 中的多個警示建立關聯。 這樣就不需要一再地重複宣告相同的動作。 「動作群組」支援多個動作 - 包括電子郵件、、SMS、語音通話、ITSM 連線、自動化 Runbook、Webhook URI 等。

針對將警示延伸至 Azure 的使用者 - 排程的「動作群組」詳細資料現在應該與閾值一起傳遞，才能建立警示。 必須先在動作群組內定義電子郵件詳細資料、Webhook URL、Runbook 自動化詳細資料及其他動作，才能建立警示；使用者可以在入口網站中[從 Azure 監視器建立動作群組](../../azure-monitor/platform/action-groups.md)，或使用[動作群組 - 資源範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)來建立動作群組。

| 元素名稱 | 必要項 | 描述 |
|:--|:--|:--|
| AzNsNotification | 是 | Azure 動作群組的資源識別碼，其會與警示相關聯，以便在符合警示準則時採取必要動作。 |
| CustomEmailSubject | 否 | 電子郵件的自訂主旨列，該電子郵件會傳送到相關聯動作群組中指定的所有地址。 |
| CustomWebhookPayload | 否 | 針對相關動作群組中定義的所有 Webhook 端點，要傳送到端點的自訂酬載。 格式取決於 Webhook 所預期的內容，而且必須是已序列化的有效 JSON。 |

## <a name="sample"></a>範例

以下是解決方案的範例，其中包含下列資源：

- 儲存搜尋
- 排程
- 動作群組

此範例會使用[標準的解決方案參數]( solutions-solution-file.md#parameters)變數，相對於資源定義中的硬式編碼值，這類變數常用於解決方案中。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

下列參數檔會提供此解決方案的範例值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
* 在您的管理解決方案中[新增檢視](solutions-resources-views.md)。
* 在您的管理解決方案中[新增自動化 Runbook 及其他資源](solutions-resources-automation.md)。
