---
title: Azure 自動化的停機期間啟動/停止 VM 概觀
description: 本文說明「停機期間啟動/停止 VM」功能，此功能會依排程啟動或停止 VM，並從 Azure 監視器記錄中主動監視這些 VM。
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3210aa5ae2ff94ba2c7dda673fbb60847c4dfd0b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372152"
---
# <a name="startstop-vms-during-off-hours-overview"></a>停機期間啟動/停止 VM 概觀

停機期間啟動/停止 VM 功能啟動或停止啟用的 Azure Vm。 此功能會依使用者定義的排程來啟動或停止機器、透過 Azure 監視器記錄來提供深入解析，以及使用[動作群組](../azure-monitor/platform/action-groups.md)傳送選用的電子郵件。 在大多數情況下，Azure Resource Manager 和傳統 VM 上都可以啟用此功能。 

這項功能會使用 [new-azvm](/powershell/module/az.compute/start-azvm) Cmdlet 來啟動 vm。 它會使用 [停止 new-azvm](/powershell/module/az.compute/stop-azvm) 來停止 vm。

> [!NOTE]
> 當 runbook 已更新為使用新的 Azure Az 模組 Cmdlet 時，會使用 AzureRM 前置詞別名。

> [!NOTE]
> 「停機期間啟動/停止 VM」已更新為支援最新可用的 Azure 模組版本。 此功能的更新版本 (已在 Marketplace 中推出) 不支援 AzureRM 模組，因為我們已從 AzureRM 遷移至 Az 模組。

針對想要將 VM 成本最佳化的使用者，此功能提供非集中式的低成本自動化選項。 此功能的用途：

- [排定啟動和停止 VM](automation-solution-vm-management-config.md#schedule)。
- [使用 Azure 標記](automation-solution-vm-management-config.md#tags)依遞增順序排定啟動和停止 VM。 傳統 VM 不支援此活動。
- [根據低 CPU 使用率](automation-solution-vm-management-config.md#cpuutil)自動停止 VM。

下列是目前功能的限制：

- 此功能可管理任何區域中的 VM，但是只能在 Azure 自動化帳戶的同一訂用帳戶中使用。
- 在支援 Log Analytics 工作區、Azure 自動化帳戶及警示的任何區域中，Azure 和 Azure Government 都提供此功能。 Azure Government 區域目前不支援電子郵件功能。

## <a name="prerequisites"></a>Prerequisites

- 「關機期間啟動/停止 VM」功能的 Runbook 支援 [Azure 執行身分帳戶](./manage-runas-account.md)。 「執行身分帳戶」是較好的驗證方法，因為使用憑證驗證，而不是可能過期或經常變更的密碼。

- 連結的自動化帳戶和 Log Analytics 工作區必須位於相同的資源群組中。

- 建議您以單獨的自動化帳戶來執行已啟用「停機期間啟動/停止 VM」功能的 VM。 Azure 模組版本經常升級，而且參數可能變更。 此功能不會隨之同步升級，還可能不適用於較新版的 Cmdlet。 建議先在測試自動化帳戶中測試模組更新，再將更新匯入至生產自動化帳戶。

## <a name="permissions"></a>權限

您必須具有特定權限，才能啟用 VM 的「停機期間啟動/停止 VM」功能。 權限有所不同，取決於此功能是使用預先建立的自動化帳戶和 Log Analytics 工作區，還是建立新的帳戶和工作區。

如果您是訂用帳戶的參與者，以及 Azure Active Directory (AD) 租用戶的全域管理員，則不需要設定權限。 如果您沒有這些權限，或需要設定自訂角色，請確定您具有如下所述的權限。

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>既有自動化帳戶和 Log Analytics 工作區的權限

若要使用現有的自動化帳戶和 Log Analytics 工作區，以啟用 VM 的「停機期間啟動/停止 VM」功能，您在資源群組範圍內需要下列權限。 若要深入瞭解角色，請參閱 [Azure 自訂角色](../role-based-access-control/custom-roles.md)。

| 權限 | 影響範圍|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | 資源群組 |
| Microsoft.Automation/automationAccounts/variables/write | 資源群組 |
| Microsoft.Automation/automationAccounts/schedules/write | 資源群組 |
| Microsoft.Automation/automationAccounts/runbooks/write | 資源群組 |
| Microsoft.Automation/automationAccounts/connections/write | 資源群組 |
| Microsoft.Automation/automationAccounts/certificates/write | 資源群組 |
| Microsoft.Automation/automationAccounts/modules/write | 資源群組 |
| Microsoft.Automation/automationAccounts/modules/read | 資源群組 |
| Microsoft.automation/automationAccounts/jobSchedules/write | 資源群組 |
| Microsoft.Automation/automationAccounts/jobs/write | 資源群組 |
| Microsoft.Automation/automationAccounts/jobs/read | 資源群組 |
| Microsoft.OperationsManagement/solutions/write | 資源群組 |
| Microsoft.OperationalInsights/workspaces/* | 資源群組 |
| Microsoft.Insights/diagnosticSettings/write | 資源群組 |
| Microsoft.Insights/ActionGroups/Write | 資源群組 |
| Microsoft.Insights/ActionGroups/read | 資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 資源群組 |
| Microsoft.Resources/deployments/* | 資源群組 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>新自動化帳戶和新 Log Analytics 工作區的權限

您可以使用新的自動化帳戶和 Log Analytics 工作區，以啟用 VM 的「停機期間啟動/停止 VM」功能。 在此情況下，您需要上一節所定義的權限及本節所定義的權限。 您也需要下列角色：

- 訂用帳戶上的 Co-Administrator。 如果您要管理傳統 VM，則需要此角色才能建立「傳統執行身分帳戶」。 預設不會再建立[傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。
- [Azure AD](../active-directory/roles/permissions-reference.md) 應用程式開發人員角色的成員資格。 如需設定「執行身分帳戶」的詳細資訊，請參閱[設定「執行身分帳戶」所需的權限](manage-runas-account.md#permissions)。
- 訂用帳戶的參與者，或下列權限。

| 權限 |影響範圍|
| --- | --- |
| Microsoft.Authorization/Operations/read | 訂用帳戶|
| Microsoft.Authorization/permissions/read |訂用帳戶|
| Microsoft.Authorization/roleAssignments/read | 訂用帳戶 |
| Microsoft.Authorization/roleAssignments/write | 訂用帳戶 |
| Microsoft.Authorization/roleAssignments/delete | 訂用帳戶 || Microsoft.Automation/automationAccounts/connections/read | 資源群組 |
| Microsoft.Automation/automationAccounts/certificates/read | 資源群組 |
| Microsoft.Automation/automationAccounts/write | 資源群組 |
| Microsoft.OperationalInsights/workspaces/write | 資源群組 |

## <a name="components"></a>元件

停機期間啟動/停止 VM 功能包括預先設定的 runbook、排程，以及與 Azure 監視器記錄的整合。 您可以使用這些元素來量身打造 VM 的啟動和關機，以符合您的商務需求。

### <a name="runbooks"></a>Runbook

下表列出此功能部署至自動化帳戶的 Runbook。 「請勿」變更 Runbook 程式碼。 相反地，為新功能撰寫自己的 Runbook。

> [!IMPORTANT]
> 請勿直接執行名稱中附加 **child** 的任何 Runbook。

所有父代 Runbook 都包含`WhatIf` 參數。 此參數設為 True 時，支援詳述 Runbook 不以此參數執行時所採取的確切行為，並驗證是否以正確的 VM 為目標。 只有當 `WhatIf` 參數設為 False 時，Runbook 才會執行已定義的動作。

|Runbook | 參數 | 描述|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 從父系 Runbook 呼叫。 在自動停止案例中，此 Runbook 會為每個資源建立警示。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目標訂用帳戶或資源群組中的 VM 上建立或更新 Azure 警示規則。 <br> `VMList` 這是以逗號分隔的 Vm 清單， (沒有空格) ，例如， `vm1,vm2,vm3` 。<br> `WhatIf` 可驗證 Runbook 邏輯但不執行。|
|AutoStop_Disable | None | 停用「自動停止」警示和預設排程。|
|AutoStop_VM_Child | WebHookData | 從父系 Runbook 呼叫。 警示規則會呼叫此 Runbook 以停止傳統 VM。|
|AutoStop_VM_Child_ARM | WebHookData |從父系 Runbook 呼叫。 警示規則會呼叫此 Runbook 以停止 VM。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 動作：啟動或停止<br> VMList  | 由雲端服務在傳統 VM 群組中執行啟動或停止動作。 |
|ScheduledStartStop_Child | VMName <br> 動作：啟動或停止 <br> resourceGroupName | 從父系 Runbook 呼叫。 針對排程的停止執行啟動或停止動作。|
|ScheduledStartStop_Child_Classic | VMName<br> 動作：啟動或停止<br> resourceGroupName | 從父系 Runbook 呼叫。 針對傳統 VM 已排定的停止，執行啟動或停止動作。 |
|ScheduledStartStop_Parent | 動作：啟動或停止 <br>VMList <br> WhatIf：True 或 False | 啟動或停止訂用帳戶中的所有 VM。 編輯 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 變數，只在這些目標資源群組上執行。 您也可以更新 `External_ExcludeVMNames` 變數來排除特定的 VM。|
|SequencedStartStop_Parent | 動作：啟動或停止 <br> WhatIf：True 或 False<br>VMList| 在您要序列啟動/停止活動的每個 VM 上，建立名為 **sequencestart** 和 **sequencestop** 的標記。 這些標記名稱會區分大小寫。 標記的值應為正整數的清單，例如， `1,2,3` 對應至您要啟動或停止的順序。 <br>**注意**：VM 必須位於 `External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 變數所定義的資源群組內。 虛擬機器必須具有適當的標記以使動作生效。|

### <a name="variables"></a>變數

下表列出在您自動化帳戶中建立的變數。 只修改開頭為 `External` 的變數。 修改開頭為 `Internal` 的變數會造成不良後果。

> [!NOTE]
> VM 名稱和資源群組的限制主要是由於變數大小。 請參閱 [Azure 自動化中的變數資產](./shared-resources/variables.md)。

|變數 | 描述|
|---------|------------|
|External_AutoStop_Condition | 設定觸發警示之條件所需的條件運算子。 可接受的值為 `GreaterThan`、`GreaterThanOrEqual`、`LessThan` 和 `LessThanOrEqual`。|
|External_AutoStop_Description | 在 CPU 百分比超出閾值的情況下停止虛擬機器的警示。|
|External_AutoStop_Frequency | 規則的評估頻率。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。 |
|External_AutoStop_MetricName | 要設定 Azure 警示規則的效能計量名稱。|
|External_AutoStop_Severity | 計量警示的嚴重性，範圍可以從 0 到 4。 |
|External_AutoStop_Threshold | `External_AutoStop_MetricName` 變數所指定 Azure 警示規則的閾值。 百分比值的範圍從 1 到 100。|
|External_AutoStop_TimeAggregationOperator | 時間彙總運算子，可套用至選取的時間範圍大小來評估條件。 可接受的值為 `Average`、`Minimum`、`Maximum`、`Total` 和 `Last`。|
|External_AutoStop_TimeWindow | 時間範圍大小，在此期間，Azure 分析選取的計量以觸發警示。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。|
|External_EnableClassicVMs| 值可指定此功能是否以傳統 VM 為目標。 預設值為 true。 針對 Azure 雲端方案提供者 (CSP) 訂用帳戶，請將此變數設定為 False。 傳統 VM 需要[傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。|
|External_ExcludeVMNames | 要排除的 VM 名稱清單 (以逗號分隔)，限制為 140 個 VM。 如果您在清單中新增140以上的 Vm，則為排除指定的 Vm 可能會不慎啟動或停止。|
|External_Start_ResourceGroupNames | 由啟動動作鎖定為目標的一或多個資源群組 (以逗號分隔的清單)。|
|External_Stop_ResourceGroupNames | 由停止動作鎖定為目標的一或多個資源群組 (以逗號分隔的清單)。|
|External_WaitTimeForVMRetrySeconds |針對 **SequencedStartStop_Parent** Runbook，等待在 VM 上執行動作的時間 (以秒為單位)。 此變數可讓 Runbook 在指定的秒數內等待子作業，再繼續下一個動作。 最長等待時間為 10800 秒 (即三小時)。 預設值為 2100 秒。|
|Internal_AutomationAccountName | 指定自動化帳戶的名稱。|
|Internal_AutoSnooze_ARM_WebhookURI | 在 VM 的自動停止案例下呼叫的 Webhook URI。|
|Internal_AutoSnooze_WebhookUri | 在傳統 VM 的自動停止案例下呼叫的 Webhook URI。|
|Internal_AzureSubscriptionId | Azure 訂用帳戶識別碼。|
|Internal_ResourceGroupName | 自動化帳戶資源群組名稱。|

>[!NOTE]
>變數 `External_WaitTimeForVMRetryInSeconds` 的預設值已從 600 更新為 2100。 

在所有案例中，目標 VM 需要 `External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 變數，但 **AutoStop_CreateAlert_Parent**、**SequencedStartStop_Parent** 和 **ScheduledStartStop_Parent** Runbook 的 VM 清單除外 (以逗號分隔)。 也就是說，您的 VM 必須屬於目標資源群組，啟動和停止動作才會執行。 邏輯的運作類似於 Azure 原則，您可以將訂用帳戶或資源群組設為目標，並讓新建立的 VM 繼承動作。 此方法可免去針對每部虛擬機器維護個別排程的必要，並可大規模地管理啟動和停止。

### <a name="schedules"></a>排程

下表列出在您的自動化帳戶中建立的各個預設排程。  您可以修改它們，或建立自己的自訂排程。  所有排程都預設為停用，但 **Scheduled_StartVM** 和 **Scheduled_StopVM** 除外。

請勿啟用所有排程，因為這樣可能會產生重疊的排程動作。 最好先決定您想要的最佳化，再據以修改。 如需進一步說明，請參閱＜概觀＞一節中的範例案例。

|排程名稱 | 頻率 | 描述|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每 8 小時 | 每 8 小時執行一次 **AutoStop_CreateAlert_Parent** Runbook，以停止 `External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 變數中以 VM 為基礎的值。 或者，您可以使用 `VMList` 參數，指定以逗號分隔的 VM 清單。|
|Scheduled_StopVM | 使用者定義，每日 | 每天在指定的時間，搭配參數 `Stop` 執行 **ScheduledStopStart_Parent** Runbook。  自動停止與資產變數所定義的規則相符的所有 VM。  啟用相關排程 **Scheduled-StartVM**。|
|Scheduled_StartVM | 使用者定義，每日 | 每天在指定的時間，搭配參數值 `Start` 執行 **ScheduledStopStart_Parent** Runbook。 自動啟動與資產變數所定義的規則相符的所有 VM。  啟用相關排程 **Scheduled-StopVM**。|
|Sequenced-StopVM | 上午 1:00 (UTC)，每星期五 | 每星期五在指定的時間，搭配參數值 `Stop` 執行 **Sequenced_StopStop_Parent** Runbook。  會以循序方式 (遞增) 停止具有由適當變數定義之 **SequenceStop** 標記的所有虛擬機器。 如需標記值和資產變數的詳細資訊，請參閱 [Runbook](#runbooks)。  啟用相關排程 **Sequenced-StartVM**。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 每星期一在指定的時間，搭配參數 `Start` 執行 **SequencedStopStart_Parent** Runbook。 會以循序方式 (遞減) 啟動具有由適當變數定義之 **SequenceStart** 標記的所有虛擬機器。 如需標記值和變數資產的詳細資訊，請參閱 [Runbook](#runbooks)。 啟用相關排程 **Sequenced-StopVM**。

## <a name="use-the-feature-with-classic-vms"></a>對傳統 VM 使用此功能

如果您對傳統 VM 使用「停機期間啟動/停止 VM」功能，自動化會依每個雲端服務來循序處理所有 VM。 在不同雲端服務之間仍然平行處理 VM。 

若要對傳統 VM 使用此功能，您需要「傳統執行身分帳戶」(依預設不會建立)。 如需建立「傳統執行身分帳戶」的指示，請參閱[建立傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。

如果每個雲端服務有 20 個以上的 VM，以下提供一些建議：

* 使用父代 Runbook **ScheduledStartStop_Parent** 建立多個排程，並為每個排程指定 20 個 VM。 
* 在 [排程] 屬性中，使用 `VMList` 參數將 VM 名稱指定為逗點分隔清單， (沒有空格) 。 

否則，如果此功能的自動化作業執行超過三個小時，則根據[公平共用](automation-runbook-execution.md#fair-share)限制，作業會暫時卸載或停止。

Azure CSP 訂用帳戶僅支援 Azure Resource Manager 模型。 非 Azure Resource Manager 服務不在方案中。 當「停機期間啟動/停止 VM」功能執行時，因為此功能有 Cmdlet 來管理傳統資源，您可能會收到錯誤。 若要深入了解 CSP，請參閱 [CSP 訂用帳戶中可用的服務](/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果您使用 CSP 訂用帳戶，則在部署之後，應該將 [External_EnableClassicVMs](#variables) 變數修改為 False。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-feature"></a>啟用此功能

若要開始使用此功能，請遵循[啟用停機期間啟動/停止 VM](automation-solution-vm-management-enable.md) 中的步驟。

## <a name="view-the-feature"></a>檢視功能

使用下列其中一種機制來存取已啟用的功能：

* 從自動化帳戶中，選取 [相關資源] 下的 [啟動/停止 VM]。 在 [啟動/停止 VM] 頁面上，選取 [管理啟動/停止 VM 解決方案] 下的 [管理解決方案]。

* 巡覽至已連結到自動化帳戶的 Log Analytics 工作區。 選取工作區之後，從左窗格中選擇 [解決方案]。 在 [解決方案] 頁面上，從清單中選取 [Start-Stop-VM[工作區]]。  

選取此功能會顯示 [Start-Stop-VM[工作區]] 頁面。 在這裡您可以檢閱重要的詳細資料，例如 [StartStopVM] 圖格中的資訊。 如同在 Log Analytics 工作區中，這個圖格會顯示此功能中已啟動並順利完成的 Runbook 作業計數和圖形表示。

![自動化更新管理頁面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

您可以按一下環圈圖格，以進一步分析作業記錄。 儀表板會顯示作業記錄和預先定義的記錄搜尋查詢。 切換至 Log Analytics 進階入口網站，以根據搜尋查詢來搜尋。

## <a name="update-the-feature"></a>更新功能

如果您已部署舊版的「停機期間啟動/停止 VM」，則在部署更新的版本之前，請先刪除舊版本。 遵循步驟[移除功能](#remove-the-feature)，然後遵循步驟[啟用功能](automation-solution-vm-management-enable.md)。

## <a name="remove-the-feature"></a>移除功能

如果不再需要使用此功能，您可以從自動化帳戶中刪除此功能。 刪除此功能只會移除相關聯的 Runbook。 不會刪除新增此功能時所建立的排程或變數。 

若要刪除「停機期間啟動/停止 VM」：

1. 從自動化帳戶中，選取 [相關資源] 下的 [連結的工作區]。

2. 選取 [前往工作區]。

3. 按一下 [一般] 下的 [解決方案]。 

4. 在 [解決方案] 頁面上，選取 [Start-Stop-VM[工作區]]。 

5. 在 [VMManagementSolution[工作區]] 頁面上，從功能表中選取 [刪除]。<br><br> ![刪除 VM 管理功能](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在 [刪除解決方案] 視窗中，確認您要刪除此功能。

7. 在驗證資訊和刪除此功能期間，您可以在 [通知] (從功能表中選擇)  下追蹤進度。 移除過程完成之後，您會回到 [解決方案] 頁面。

8. 此過程中不會刪除自動化帳戶和 Log Analytics 工作區。 如果您不想保留 Log Analytics 工作區，則必須從 Azure 入口網站手動刪除工作區：

    1. 搜尋並選取 **Log Analytics 工作區**。

    2. 在 [Log Analytics 工作區] 頁面上，選取工作區。

    3. 從功能表中選取 [刪除]。

    4. 如果您不想保留 Azure 自動化帳戶[功能元件](#components)，您可以手動刪除每個元件。

## <a name="next-steps"></a>後續步驟

若要在環境中的 VM 上啟用此功能，請參閱[啟用停機期間啟動/停止 VM](automation-solution-vm-management-enable.md)。
