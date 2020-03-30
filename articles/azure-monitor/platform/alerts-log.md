---
title: 使用 Azure 監視器創建、查看和管理日誌警報 |微軟文檔
description: 使用 Azure 監視器在 Azure 中創作、查看和管理日誌警報規則。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249421"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>使用 Azure 監視器來建立、檢視及管理記錄警示

## <a name="overview"></a>總覽
本文會示範如何使用 Azure 入口網站內的警示介面來設定記錄警示。 警示規則的定義分成三個部分：
- 目標：要監視的特定 Azure 資源
- 條件：訊號中所示且會觸發動作的特定條件或邏輯
- 動作：傳送至通知 (電子郵件、SMS、Webhook 等) 接收者的特定呼叫。

術語 **"日誌警報"** 用於描述信號在[日誌分析工作區](../learn/tutorial-viewdata.md)或[應用程式見解](../app/analytics.md)中顯示日誌查詢的警報。 閱讀[記錄警示 - 概觀](alerts-unified-log.md)以深入了解其功能、術語和類型。

> [!NOTE]
> [日誌分析工作區中](../../azure-monitor/learn/tutorial-viewdata.md)常用的日誌資料現在也可在 Azure 監視器中的指標平臺上使用。 如需詳細資料檢視，請參閱[記錄的計量警示](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>從 Azure 入口網站管理記錄警示

詳細的下一步是將使用 Azure 入口網站介面來使用記錄警示的逐步指南。

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>使用 Azure 入口網站建立記錄警示規則

1. 在[門戶](https://portal.azure.com/)中，選擇 **"監視器**"，在 MONITOR 部分下選擇 **"警報**"。

    ![監視](media/alerts-log/AlertsPreviewMenu.png)

1. 選取 [新增警示規則]**** 按鈕，以在 Azure 中建立新的警示。

    ![新增警示](media/alerts-log/AlertsPreviewOption.png)

1. [建立警示] 區段會以三個部分顯示：「定義警示條件」**、「定義警示詳細資料」** 和「定義動作群組」**。

    ![建立規則](media/alerts-log/AlertsPreviewAdd.png)

1. 使用 [選取資源]**** 連結並透過選取資源來指定目標，即可定義警示條件。 選擇 [訂閱]__、[資源類型]__ 和 [資源]__ (必選)，即可進行篩選。

   > [!NOTE]
   > 若要建立記錄警示 - 請先確認選取的資源可使用 **log** 訊號，再繼續下面的步驟。
   >  ![選取資源](media/alerts-log/Alert-SelectResourceLog.png)

1. 記錄警示**：請確認 [資源類型]**** 是分析來源 (例如「Log Analytics」** 或「Application Insights」**) 且訊號類型為 [記錄]****，然後在選擇適當的 [資源]**** 後，按一下 [完成]**。 接著使用 [新增準則]**** 按鈕，檢視資源可用的訊號選項清單，然後從訊號清單中選取所選記錄監視服務 (例如 *Log Analytics* 或 *Application Insights*) 的 [自訂記錄搜尋]**** 選項。

   ![選取資源 - 自訂記錄搜尋](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > [警示] 清單可匯入分析查詢作為訊號類型 - [記錄 (已儲存的查詢)]****，如上圖所示。 因此，使用者可以在 Analytics（分析）中完善查詢，然後將它們保存起來以供將來用於警報中 - 有關[使用 Azure 監視器中的日誌查詢](../log-query/log-query-overview.md)或在[應用程式見解分析中使用](../app/app-insights-overview.md)日誌查詢的保存查詢的更多詳細資訊。

1. *記錄警示*：選好後，即可在 [搜尋查詢]**** 欄位中指定警示的查詢；如果查詢語法不正確，欄位會以紅色顯示錯誤。 如果查詢語法正確 - 指定查詢的記錄資料會顯示為圖形以供參考，以及顯示調整時間範圍的選項 (從過去六小時到過去一週)。

    ![設定警示規則](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > 記錄資料的視覺效果只會在查詢結果具有時間詳細資料時才會顯示。 如果您的查詢結果的是摘要的資料或特定資料行的值，則會以單一繪圖顯示相同資料。
   > 若使用 Application Insights 或[切換至新的 API](alerts-log-api-switch.md) 的記錄警示類型是「計量測量」，您可以使用 [彙總依據]**** 選項來指定要群組資料的特定變數，如下所示：
   > 
   > ![[依據彙總] 選項](media/alerts-log/aggregate-on.png)

1. 記錄警示**：當視覺效果就緒後，您就可以從所示的條件、彙總和閾值 (最後選取) 選項來選取**警示邏輯**。 最後，使用 [期間]**** 選項，在邏輯中指定用來評估指定條件的時間。 以及，透過選取 [頻率]****，來指定警示執行的頻率。 **記錄警示**可以根據：
    - 記錄數目[](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules)：如果查詢所傳回的記錄數目大於或小於所提供的值，則會建立警示。
    - 計量測量[](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)：如果結果中的每個「彙總值」** 超過提供的閾值，而且彙總值依據選擇的值來「分組」**，則會建立警示。 警示的違規數是所選時段中超過閾值的次數。 您可以跨結果集針對任何違規組合指定 [違規數總計]，或指定 [連續違規] 以要求違規必須發生於連續取樣中。


1. 第二個步驟是在 [警示規則名稱]**** 欄位中定義警示的名稱，以及在 [描述]**** 中具體描述警示，並從提供的選項中選取 [嚴重性]**** 的值。 這些詳細資料會在所有由 Azure 監視器完成的警示電子郵件、通知或推送中重複使用。 此外，使用者可以透過適當地切換 [在建立時啟用規則]**** 選項，選擇在建立警示規則後立即啟用警示。

    警示詳細資料中有一些額外的功能可使用 (僅限於**記錄警示**)：

    - **隱藏警示**：當您開啟警示規則的隱藏功能時，此規則的動作會在建立新警示後停用並持續一段您所定義的時間。 此規則仍在執行中，並且會在符合準則時建立警示記錄。 讓您有時間更正問題，而不需執行重複的動作。

        ![記錄警示的隱藏警示](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > 指定大於警示頻率的隱藏警示值，可確保通知會停止而不重疊

1. 第三個步驟 (也就是最後一個步驟) 是指定當警示條件符合時，是否有任何**動作群組**必須根據警示規則來觸發。 您可以選擇任何具有警示的現有動作群組，或建立新的動作群組。 根據選取的動作群組，當警示觸發時，Azure 將會：傳送電子郵件、傳送 SMS、呼叫 Webhook、使用 Azure Runbook 進行補救，發送到您的 ITSM 工具等等。 瞭解有關[操作組](action-groups.md)的更多。

    > [!NOTE]
    > 請參閱 [Azure 訂用帳戶服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)，了解透過 Azure 動作群組針對記錄警示觸發 Runbook 承載的限制

    針對**記錄警示**，有一些額外的功能可用來覆寫預設的動作：

    - **電子郵件通知**：覆寫透過「動作群組」傳送的電子郵件「電子郵件主旨」**；如果前述的「動作群組」中存在一或多個電子郵件動作。 您無法修改電子郵件的本文，且此欄位**不是**用於電子郵件地址。
    - **包括自訂 JSON 承載**：覆寫「動作群組」所使用的 Webhook JSON；如果前述的「動作群組」中存在一或多個 Webhook 動作。 使用者可以指定要用於相關聯動作群組中設定的所有 Webhook JSON 格式；如需有關 Webhook 格式的詳細資訊，請參閱[記錄警示的 Webhook 動作](../../azure-monitor/platform/alerts-log-webhook.md)。 提供的 [檢視 Webhook] 選項可用來檢查使用 JSON 資料範例的格式。

        ![記錄警示的動作覆寫](media/alerts-log/AlertsPreviewOverrideLog.png)


1. 如果所有欄位都有效，並且有綠色勾號，您就可以按一下 [建立警示規則]**** 按鈕，在 Azure [監視器 - 警示] 中建立警示。 您可以從「警示儀表板」中檢視所有警示。

     ![建立規則](media/alerts-log/AlertsPreviewCreate.png)

     在幾分鐘之內，警示會開始作用，且先前所述觸發。

使用者還可以在[日誌分析](../log-query/portals.md)中完成其分析查詢，然後通過"設置警報"按鈕推送以創建警報 -然後按照上述教程中步驟 6 中的說明進行操作。

 ![Log Analytics - 設定警示](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>在 Azure 入口網站中檢視和管理記錄警示

1. 在[門戶](https://portal.azure.com/)中，選擇 **"監視器**"，在 MONITOR 部分下選擇 **"警報**"。

1. 將顯示**警報儀表板**- 其中所有 Azure 警報（包括日誌警報）都顯示在單一板中;包括日誌警報規則引發的每個實例。 若要深入了解，請參閱[警示管理](https://aka.ms/managealertinstances)。
    > [!NOTE]
    > 記錄警示規則由使用者提供的自訂查詢邏輯所組成，因此沒有已解決的狀態。 所以只要符合記錄警示規則中所指定的條件時，就會引發它。

1. 在頂端的工具列上選取 [管理規則]****，即可瀏覽到規則管理區段 - 其中會列出所有已建立的警示規則 (包括已停用的警示)。
    ![管理警示規則](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>使用 Azure 資源範本管理記錄警示

「Azure 監視器」中的記錄警示與資源類型 `Microsoft.Insights/scheduledQueryRules/`相關聯。 如需有關此資源類型的詳細資訊，請參閱 [Azure 監視器 - 排程的查詢規則 API 參考](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)。 建立 Application Insights 或 Log Analytics 的記錄警示時，可以使用[排程的查詢規則 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) 來建立。

> [!NOTE]
> 管理 Log Analytics 的記錄警示時，也可以使用舊版 [Log Analytics Alert API](api-alerts.md) 及舊版 [Log Analytics 儲存的搜尋和警示](../insights/solutions-resources-searches-alerts.md)範本來管理。 如需有關預設使用這裡詳述之新 ScheduledQueryRules API 的詳細資訊，請參閱[切換至 Log Analytics 警示的新 API](alerts-log-api-switch.md)。


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>使用 Azure 資源範本建立範例記錄警示

以下結構可用來根據資源範本使用[結果類型記錄警示數目](alerts-unified-log.md#number-of-results-alert-rules)的標準記錄搜尋查詢[建立排程的查詢規則](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate)，其中使用資料集範例作為變數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

基於本逐步解說的目的，上述 JSON 範例可儲存為 (假設) sampleScheduledQueryRule.json，而且可以使用 [Azure 入口網站中的 Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) 來進行部署。


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>使用 Azure 資源範本和跨資源查詢的記錄警示

以下結構可用來根據資源範本使用[計量測量類型記錄警示](../../azure-monitor/log-query/cross-workspace-query.md)的[跨資源記錄搜尋查詢](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)[建立排程的查詢規則](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate)，其中使用資料集範例作為變數。

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> 在記錄警示中使用跨資源查詢時，必須使用 [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source)，且使用者必須可存取所列的資源清單

基於本逐步解說的目的，上述 JSON 範例可儲存為 (假設) sampleScheduledQueryRule.json，而且可以使用 [Azure 入口網站中的 Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) 來進行部署。

## <a name="managing-log-alerts-using-powershell"></a>使用 PowerShell 管理日誌警報

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure 監視器 -[計畫查詢規則 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)是 REST API，與 Azure 資源管理器 REST API 完全相容。 下面列出的 PowerShell Cmdlet 可用於利用[計畫查詢規則 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)。

1. [新建-Az計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule)：電源shell Cmdlet 以創建新的日誌警報規則。
1. [設置-Az計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)：電源shell Cmdlet 以更新現有的日誌警報規則。
1. [New-Az計畫查詢規則來源](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource)：電源shell Cmdlet 用於創建或更新指定日誌警報源參數的物件。 用作[輸入的新-阿茲計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule)和[集-阿茲計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)Cmdlet。
1. [New-Az計畫查詢規則計畫](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule)：用於創建或更新指定日誌警報計畫參數的物件的電源shell Cmdlet。 用作[輸入的新-阿茲計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule)和[集-阿茲計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)Cmdlet。
1. [New-Az計畫查詢規則警報操作](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)：Powershell Cmdlet 用於創建或更新指定日誌警報指令引數的物件。 用作[輸入的新-阿茲計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule)和[集-阿茲計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)Cmdlet。
1. [New-Az-計畫查詢規則AznsActionGroup：](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup)用於創建或更新指定日誌警報操作組參數的物件的 Powershell Cmdlet。 用作輸入的新[-阿茲特查詢規則警報操作](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)Cmdlet。
1. [New-Az計畫查詢規則引發條件](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition)：用於創建或更新指定日誌警報的觸發條件參數的物件。 用作輸入的新[-阿茲特查詢規則警報操作](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)Cmdlet。
1. [New-Az計畫查詢RuleLogMetric觸發器](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger)：用於創建或更新指定[指標度量類型日誌警報](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)的指標觸發器條件參數的物件。 用作輸入的新[-阿茲計畫查詢規則引發條件](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition)Cmdlet。
1. [獲取-Az計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule)：Powershell Cmdlet 列出現有日誌警報規則或特定日誌警報規則
1. [更新-阿茲計畫查詢規則](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule)：電源殼 Cmdlet 以啟用或禁用日誌警報規則
1. [刪除-阿茲特規則](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule)：電源殼 Cmdlet 刪除現有日誌警報規則

> [!NOTE]
> 計畫查詢規則 PowerShell Cmdlet 只能管理創建的規則 Cmdlet 本身或使用 Azure 監視器 -[計畫查詢規則 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)。 使用舊日誌[分析警報 API](api-alerts.md)創建的日誌警報規則以及[日誌分析保存的搜索和警報](../insights/solutions-resources-searches-alerts.md)的舊範本只能在使用者[切換日誌分析警報的 API 首選項](alerts-log-api-switch.md)後，才能使用計畫查詢規則 PowerShell Cmdlet 進行管理。

下圖是使用計畫的QueryRules PowerShell Cmdlet創建示例日誌警報規則的步驟。
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>使用 CLI 或 API 管理日誌警報

Azure 監視器 -[計畫查詢規則 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)是 REST API，與 Azure 資源管理器 REST API 完全相容。 因此，可以使用 Azure CLI 的資源管理器命令通過 Powershell 使用它。


> [!NOTE]
> 管理 Log Analytics 的記錄警示時，也可以使用舊版 [Log Analytics Alert API](api-alerts.md) 及舊版 [Log Analytics 儲存的搜尋和警示](../insights/solutions-resources-searches-alerts.md)範本來管理。 如需有關預設使用這裡詳述之新 ScheduledQueryRules API 的詳細資訊，請參閱[切換至 Log Analytics 警示的新 API](alerts-log-api-switch.md)。

日誌警報當前沒有專用的 CLI 命令;因此，日誌警報當前沒有專用 CLI 命令。但如下圖所示，可以通過 Azure 資源管理器 CLI 命令用於資源範本（示例計畫查詢規則.json）中"資源範本"部分中前面顯示的示例資源範本：

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

成功完成作業後，系統會傳回 201，說明新警示規則的建立，或是傳回 200，表示現有的警示規則已修改。

## <a name="next-steps"></a>後續步驟

* 瞭解[Azure 警報中的日誌警報](../../azure-monitor/platform/alerts-unified-log.md)
* 了解 [Webhook 動作記錄警示](../../azure-monitor/platform/alerts-log-webhook.md)
* 瞭解有關[應用程式見解](../../azure-monitor/app/analytics.md)的更多
* 瞭解有關[日誌查詢的更多。](../log-query/log-query-overview.md)
