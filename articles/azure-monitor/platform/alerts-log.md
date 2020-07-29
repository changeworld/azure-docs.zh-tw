---
title: 使用 Azure 監視器建立、查看和記錄管理警示 |Microsoft Docs
description: 使用 Azure 監視器來撰寫、查看和管理 Azure 中的記錄警示規則。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 25604bde3afbbef0d541bc21996b59e98b3090f4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327492"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>使用 Azure 監視器來建立、檢視及管理記錄警示

## <a name="overview"></a>概觀
本文說明如何使用 Azure 入口網站內的警示介面來建立和記錄管理警示。 警示規則是由三個元件所定義：
- 目標：要監視的特定 Azure 資源
- 準則：要評估事實的條件或邏輯。 若為 true，則會引發警示。  
- 動作：傳送至通知 (電子郵件、SMS、Webhook 等) 接收者的特定呼叫。

「**記錄警示**」一詞描述[log Analytics 工作區](../log-query/get-started-portal.md)中的記錄查詢或[Application Insights](../log-query/log-query-overview.md)評估的警示，並在結果為 true 時引發警示。 閱讀[記錄警示 - 概觀](alerts-unified-log.md)以深入了解其功能、術語和類型。

> [!NOTE]
> Log [Analytics 工作區](../log-query/get-started-portal.md)中的記錄資料也可以路由傳送至 Azure 監視器計量資料庫。 計量警示有[不同的行為](alerts-metric-overview.md)，視您使用的資料而定，這可能會更理想。   如需有關如何將記錄路由至計量的詳細資訊，請參閱[記錄的度量警示](alerts-metric-logs.md)。

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>使用 Azure 入口網站建立記錄警示規則

1. 在[入口網站](https://portal.azure.com/)中，選取 [**監視**]。 在該區段中，選擇 [**警示**]。

    ![監視](media/alerts-log/AlertsPreviewMenu.png)

1. 按一下 [**新增警示規則**]。 

    ![新增警示](media/alerts-log/AlertsPreviewOption.png)

1. [**建立警示**] 窗格隨即出現。 其中包含四個部分： 
    - 要套用警示的資源
    - 要檢查的條件
    - 條件為 true 時要採取的動作
    - 用來命名和描述警示的詳細資料。 

    ![建立規則](media/alerts-log/AlertsPreviewAdd.png)

1. 使用 [選取資源]**** 連結並透過選取資源來指定目標，即可定義警示條件。 選擇 [訂閱]**、[資源類型]** 和 [資源]** (必選)，即可進行篩選。 

   ![選取資源](media/alerts-log/Alert-SelectResourceLog.png)

1. 請確定**資源類型**是*log analytics*之類的分析來源，或*Application Insights* ，而信號類型為*log*。 按一下 [完成] 。 接下來，使用 [**新增準則**] 按鈕來查看可用於資源的信號選項清單。 針對*Log Analytics*或*Application Insights*尋找並選擇 [**自訂記錄搜尋**] 選項，視記錄警示的資料所在位置而定。

   ![選取資源 - 自訂記錄搜尋](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > [警示] 清單可匯入分析查詢作為訊號類型 - [記錄 (已儲存的查詢)]****，如上圖所示。 因此，使用者可以在分析中完美查詢，然後加以儲存以供日後用於警示。 如需使用已儲存查詢的詳細資訊，請參閱[在 Azure 監視器中使用記錄查詢](../log-query/log-query-overview.md)和[Application Insights 分析中的共用查詢](../app/app-insights-overview.md)。

1. 選取之後，請在 [**搜尋查詢**] 欄位中建立警示查詢。 如果查詢語法不正確，欄位會以紅色顯示並出現錯誤。 

1. 如果查詢語法正確，則查詢的歷程記錄資料會顯示為圖形，其中有選項可將時間範圍從過去六個小時調整為上周。

    ![設定警示規則](media/alerts-log/AlertsPreviewAlertLog.png)

   只有當查詢結果具有時間詳細資料時，才會顯示歷程記錄資料視覺效果。 如果您的查詢會產生摘要的資料或特定的資料行值，則顯示會顯示單一繪圖。
  
   針對使用 Application Insights 或[Log ANALYTICS API](/rest/api/monitor/scheduledqueryrules)的計量測量，您可以使用 [**匯總依據**] 選項來指定要將資料分組的特定變數。如下所示： 
  
   ![[依據彙總] 選項](media/alerts-log/aggregate-on.png)



1. 接下來，選擇**警示邏輯**條件 [匯總] 和 [臨界值]。 

1. 使用 [**期間**] 選項，選擇評估指定條件的時間週期。 

1. 選擇警示以**頻率**執行的頻率。 

    **記錄警示**可以根據：
    - 記錄數目[](./alerts-unified-log.md#number-of-results-alert-rules)：如果查詢所傳回的記錄數目大於或小於所提供的值，則會建立警示。
    - 計量測量[](./alerts-unified-log.md#metric-measurement-alert-rules)：如果結果中的每個「彙總值」** 超過提供的閾值，而且彙總值依據選擇的值來「分組」**，則會建立警示。 警示的違規數是所選時段中超過閾值的次數。 您可以跨結果集針對任何違規組合指定 [違規數總計]，或指定 [連續違規] 以要求違規必須發生於連續取樣中。


1. 按一下 [完成] 。 

1. 在 [**警示規則名稱**] 欄位中定義警示的名稱，以及從提供的選項中詳細**說明**警示的詳細資訊和**嚴重性**值。 這些詳細資料會在所有警示電子郵件、通知或由 Azure 監視器完成的推送中重複使用。 此外，您可以選擇在建立時按一下 [**啟用規則**]，立即啟動警示規則。

1. 如果您想要隱藏一段時間的**警示**，請選擇此項。  當您開啟警示規則的隱藏功能時，此規則的動作會在建立新警示後停用並持續一段您所定義的時間。 規則仍會執行，並會在符合準則時建立警示記錄。 此設定可讓您有時間更正問題，而不需要執行重複的動作。

   ![記錄警示的隱藏警示](media/alerts-log/AlertsPreviewSuppress.png)

    > [!TIP]
    > 指定大於警示頻率的隱藏警示值，以確保通知已停止而不重迭

1. 在第三個和最後一個步驟中，指定當警示條件符合時，警示規則是否應觸發一個或多個**動作群組**。 您可以選擇任何現有的動作群組，或建立一個新的。 透過動作群組，您可以傳送執行一些動作，例如傳送電子郵件、傳送 SMS、呼叫 Webhook、使用 Azure Runbook 進行補救、推送至您的 ITSM 工具等等。 深入瞭解[動作群組](action-groups.md)。

    > [!NOTE]
    > 如需可執行動作的限制，請參閱[Azure 訂用帳戶服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)。  

    有一些額外的功能可用來覆寫預設動作：

    - **電子郵件通知**：覆寫透過動作群組傳送之電子郵件中的*電子郵件*主旨。 您無法修改電子郵件的本文，且此欄位**不是**用於電子郵件地址。
    - **包含自訂 Json**承載：覆寫動作群組所使用的 webhook Json （假設動作群組包含 webhook 類型）。 如需 webhook 格式的詳細資訊，請參閱[記錄警示的 webhook 動作](./alerts-log-webhook.md)。 提供的 [檢視 Webhook] 選項可用來檢查使用 JSON 資料範例的格式。

        ![記錄警示的動作覆寫](media/alerts-log/AlertsPreviewOverrideLog.png)


1. 如果所有欄位都有效，並且有綠色勾號，您就可以按一下 [建立警示規則]**** 按鈕，在 Azure [監視器 - 警示] 中建立警示。 您可以從「警示儀表板」中檢視所有警示。

     ![建立規則](media/alerts-log/AlertsPreviewCreate.png)

     在幾分鐘之內，警示會開始作用，且先前所述觸發。

使用者也可以在[log analytics](../log-query/log-query-overview.md)中完成分析查詢，然後透過 [設定警示] 按鈕推送它以建立警示，然後依照上述教學課程中步驟6開始的指示進行。

 ![Log Analytics - 設定警示](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>在 Azure 入口網站中檢視和管理記錄警示

1. 在[入口網站](https://portal.azure.com/)中，選取 [**監視**]，然後在 [監視] 區段下選擇 [**警示**]。

1. [**警示] 儀表板**隨即顯示-其中所有 Azure 警示（包括記錄警示）都會顯示在單一面板中;當您的記錄警示規則引發時，包含的每個實例。 若要深入了解，請參閱[警示管理](https://aka.ms/managealertinstances)。
    > [!NOTE]
    > 記錄警示規則由使用者提供的自訂查詢邏輯所組成，因此沒有已解決的狀態。 所以只要符合記錄警示規則中所指定的條件時，就會引發它。

1. 在頂端的工具列上選取 [管理規則]****，即可瀏覽到規則管理區段 - 其中會列出所有已建立的警示規則 (包括已停用的警示)。
    ![管理警示規則](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>使用 Azure 資源範本管理記錄警示

「Azure 監視器」中的記錄警示與資源類型 `Microsoft.Insights/scheduledQueryRules/`相關聯。 如需有關此資源類型的詳細資訊，請參閱 [Azure 監視器 - 排程的查詢規則 API 參考](/rest/api/monitor/scheduledqueryrules/)。 建立 Application Insights 或 Log Analytics 的記錄警示時，可以使用[排程的查詢規則 API](/rest/api/monitor/scheduledqueryrules/) 來建立。

> [!NOTE]
> 管理 Log Analytics 的記錄警示時，也可以使用舊版 [Log Analytics Alert API](api-alerts.md) 及舊版 [Log Analytics 儲存的搜尋和警示](../insights/solutions.md)範本來管理。 如需有關預設使用這裡詳述之新 ScheduledQueryRules API 的詳細資訊，請參閱[切換至 Log Analytics 警示的新 API](alerts-log-api-switch.md)。


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>使用 Azure 資源範本建立範例記錄警示

以下結構可用來根據資源範本使用[結果類型記錄警示數目](alerts-unified-log.md#number-of-results-alert-rules)的標準記錄搜尋查詢[建立排程的查詢規則](/rest/api/monitor/scheduledqueryrules/createorupdate)，其中使用資料集範例作為變數。

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

以下結構可用來根據資源範本使用[計量測量類型記錄警示](../log-query/cross-workspace-query.md)的[跨資源記錄搜尋查詢](./alerts-unified-log.md#metric-measurement-alert-rules)[建立排程的查詢規則](/rest/api/monitor/scheduledqueryrules/createorupdate)，其中使用資料集範例作為變數。

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
> 在記錄警示中使用跨資源查詢時，必須使用 [authorizedResources](/rest/api/monitor/scheduledqueryrules/createorupdate#source)，且使用者必須可存取所列的資源清單

基於本逐步解說的目的，上述 JSON 範例可儲存為 (假設) sampleScheduledQueryRule.json，而且可以使用 [Azure 入口網站中的 Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) 來進行部署。

## <a name="managing-log-alerts-using-powershell"></a>使用 PowerShell 記錄管理警示

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure 監視器[排程的查詢規則 API](/rest/api/monitor/scheduledqueryrules/)是 REST API，而且與 Azure Resource Manager REST API 完全相容。 以下所列的 PowerShell Cmdlet 可用於利用已[排程的查詢規則 API](/rest/api/monitor/scheduledqueryrules/)。

- [AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) ： PowerShell Cmdlet 可建立新的記錄警示規則。
- [AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) ： PowerShell Cmdlet 可更新現有的記錄警示規則。
- [AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) ： PowerShell Cmdlet 可建立或更新指定記錄警示之來源參數的物件。 做為[AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule)和[AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) Cmdlet 的輸入使用。
- [AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule)： PowerShell Cmdlet 可建立或更新指定記錄警示之排程參數的物件。 做為[AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule)和[AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) Cmdlet 的輸入使用。
- [AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) ： PowerShell Cmdlet 可建立或更新指定記錄警示之動作參數的物件。 做為[AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule)和[AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) Cmdlet 的輸入使用。
- [AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) ： PowerShell Cmdlet 可建立或更新指定記錄警示之動作群組參數的物件。 用來做為[AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) Cmdlet 的輸入。
- [AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) ： PowerShell Cmdlet 可建立或更新指定記錄警示之觸發條件參數的物件。 用來做為[AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) Cmdlet 的輸入。
- [AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) ： PowerShell Cmdlet 可用於建立或更新物件，指定度量[量測類型記錄警示的計量](./alerts-unified-log.md#metric-measurement-alert-rules)觸發條件參數。 用來做為[AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) Cmdlet 的輸入。
- [AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) ：用來列出現有記錄警示規則或特定記錄警示規則的 PowerShell Cmdlet
- [AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) ：用來啟用或停用記錄警示規則的 PowerShell Cmdlet
- [AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule)： PowerShell Cmdlet 刪除現有的記錄警示規則

> [!NOTE]
> ScheduledQueryRules PowerShell Cmdlet 只能管理已建立的 Cmdlet 本身，或使用 Azure 監視器[排程的查詢規則 API](/rest/api/monitor/scheduledqueryrules/)。 只有在使用者[切換 Log Analytics 警示的 API 喜好](alerts-log-api-switch.md)設定之後，才可以使用 ScheduledQueryRules PowerShell Cmdlet 來管理使用舊版[LOG analytics 警示 API](api-alerts.md)建立的記錄警示規則，以及[log analytics 儲存的搜尋和警示](../insights/solutions.md)的舊版範本。

下圖說明使用 scheduledQueryRules PowerShell Cmdlet 建立範例記錄警示規則的步驟。

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>使用 CLI 或 API 記錄管理警示

Azure 監視器[排程的查詢規則 API](/rest/api/monitor/scheduledqueryrules/)是 REST API，而且與 Azure Resource Manager REST API 完全相容。 因此，可以使用 Azure CLI 的 Resource Manager 命令，透過 Powershell 使用。


> [!NOTE]
> 管理 Log Analytics 的記錄警示時，也可以使用舊版 [Log Analytics Alert API](api-alerts.md) 及舊版 [Log Analytics 儲存的搜尋和警示](../insights/solutions.md)範本來管理。 如需有關預設使用這裡詳述之新 ScheduledQueryRules API 的詳細資訊，請參閱[切換至 Log Analytics 警示的新 API](alerts-log-api-switch.md)。

記錄警示目前沒有專用的 CLI 命令;但如下所示，您可以透過 Azure Resource Manager CLI 命令，針對先前在資源範本區段中所顯示的範例資源範本（sampleScheduledQueryRule.js開啟）來使用：

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

成功完成作業後，系統會傳回 201，說明新警示規則的建立，或是傳回 200，表示現有的警示規則已修改。

## <a name="next-steps"></a>後續步驟

* 瞭解[Azure 警示中的記錄警示](./alerts-unified-log.md)
* 了解 [Webhook 動作記錄警示](./alerts-log-webhook.md)
* 深入瞭解[Application Insights](../log-query/log-query-overview.md)
* 深入瞭解[記錄查詢](../log-query/log-query-overview.md)。

