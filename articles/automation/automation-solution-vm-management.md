---
title: 在下班時間解決方案中啟動/停止 Vm
description: 此 VM 管理解決方案會依排程啟動和停止您的 Azure 虛擬機器，並從 Azure 監視器記錄中主動進行監視。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 0331678b50d2448013556ab0694d0ca87045c3a3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096911"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>在 Azure 自動化中的離峰時間啟動/停止 Vm 解決方案

[**在離峰期間啟動/停止 vm** ] 解決方案會啟動或停止您的 Azure 虛擬機器。 它會以使用者定義的排程啟動或停止機器、提供 Azure 監視器記錄的深入解析，並使用[動作群組](../azure-monitor/platform/action-groups.md)來傳送選用的電子郵件。 在大部分的情況下，此解決方案都支援 Azure Resource Manager 和傳統 Vm。 

此解決方案可針對想要將 VM 成本最佳化的使用者，提供非集中式的低成本自動化選項。 使用此解決方案，您可以：

- [排程要啟動和停止的 vm](automation-solution-vm-management-config.md#schedule)。
- [使用 Azure 標記](automation-solution-vm-management-config.md#tags)（不支援傳統 vm），以遞增順序將 vm 排程為啟動和停止。
- 根據[低 CPU 使用量](automation-solution-vm-management-config.md#cpuutil)來 Autostop vm。

> [!NOTE]
> [**在離峰期間啟動/停止 vm** ] 解決方案已更新，可支援可用的最新 Azure 模組版本。

下列是目前解決方案的限制：

- 它會管理任何區域中的 Vm，但只能在與您 Azure 自動化帳戶相同的訂用帳戶中使用。
- 可在 Azure 中使用，並 Azure Government 至任何支援 Log Analytics 工作區、Azure 自動化帳戶和警示的區域。 Azure Government 區域目前不支援電子郵件功能。

## <a name="solution-prerequisites"></a>解決方案必要條件

此解決方案的 Runbook 會使用 [Azure 執行身分帳戶](automation-create-runas-account.md)。 執行身分帳戶是慣用的驗證方法，因為它使用憑證驗證，而不是可能過期或經常變更的密碼。

我們建議您在離峰時段使用個別的自動化帳戶來**啟動/停止 vm**解決方案。 Azure 模組版本經常升級，且其參數可能會變更。 此解決方案不會以相同的步調升級，而且可能無法與它所使用的較新版本 Cmdlet 搭配使用。 建議您先在測試自動化帳戶中測試模組更新，再將它們匯入您的生產自動化帳戶中。

## <a name="solution-permissions"></a>解決方案許可權

您必須具有特定許可權，才能在**下班時間解決方案中部署啟動/停止 vm** 。 如果解決方案在部署期間建立新的帳戶和工作區，則當解決方案使用預先建立的自動化帳戶和 Log Analytics 工作區時，這些許可權會有所不同。 

如果您是訂用帳戶的參與者，以及 Azure Active Directory 租使用者中的全域管理員，則不需要設定許可權。 如果您沒有這些許可權，或需要設定自訂角色，請確定您具有下面所述的許可權。

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>預先存在的自動化帳戶和 Log Analytics 工作區的許可權

若要在**離峰期間將啟動/停止 vm**解決方案部署至現有的自動化帳戶和 Log Analytics 工作區，部署解決方案的使用者需要資源群組範圍的下列許可權。 若要深入瞭解角色，請參閱[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。

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
| AutomationAccounts/jobSchedules/write | 資源群組 |
| Microsoft.Automation/automationAccounts/jobs/write | 資源群組 |
| Microsoft.Automation/automationAccounts/jobs/read | 資源群組 |
| Microsoft.OperationsManagement/solutions/write | 資源群組 |
| Microsoft.operationalinsights/工作區/* | 資源群組 |
| Microsoft Insights/diagnosticSettings/write | 資源群組 |
| Microsoft.Insights/ActionGroups/Write | 資源群組 |
| Microsoft Insights/ActionGroups/read | 資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 資源群組 |
| Microsoft.Resources/deployments/* | 資源群組 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>新的自動化帳戶和新的 Log Analytics 工作區的許可權

您可以在**離峰時間將啟動/停止 vm**解決方案部署至新的自動化帳戶和 Log Analytics 工作區。 在此情況下，部署解決方案的使用者需要在上一節中定義的許可權，以及本節中定義的許可權。 

部署解決方案的使用者需要下列角色：

- 訂用帳戶上的共同管理員。 如果您要管理傳統 Vm，必須要有此角色才能建立傳統執行身分帳戶。 預設不會再建立[傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。
- [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)應用程式開發人員角色的成員資格。 如需設定執行身分帳戶的詳細資訊，請參閱[設定執行身分帳戶的許可權](manage-runas-account.md#permissions)。
- 訂用帳戶的參與者或下列許可權。

| 權限 |影響範圍|
| --- | --- |
| Microsoft。授權/作業/讀取 | 訂用帳戶|
| Microsoft.Authorization/permissions/read |訂用帳戶|
| Microsoft.Authorization/roleAssignments/read | 訂用帳戶 |
| Microsoft.Authorization/roleAssignments/write | 訂用帳戶 |
| Microsoft.Authorization/roleAssignments/delete | 訂用帳戶 |
| Microsoft.Automation/automationAccounts/connections/read | 資源群組 |
| Microsoft.Automation/automationAccounts/certificates/read | 資源群組 |
| Microsoft.Automation/automationAccounts/write | 資源群組 |
| Microsoft.OperationalInsights/workspaces/write | 資源群組 |

## <a name="solution-components"></a>方案元件

在**離峰期間啟動/停止 vm 解決方案**包含預先設定的 runbook、排程，以及與 Azure 監視器記錄的整合。 您可以使用這些元素來量身打造 Vm 的啟動和關閉，以符合您的商務需求。

### <a name="runbooks"></a>Runbook

下表列出解決方案部署至您的自動化帳戶的 runbook。 請勿對 runbook 程式碼進行變更。 相反地，為新功能撰寫自己的 Runbook。

> [!IMPORTANT]
> 請勿直接執行任何**子**附加至其名稱的 runbook。

所有的`WhatIf`父 runbook 都包含參數。 當設定為 True 時，參數會支援在沒有參數的情況下執行時，runbook 所採取的確切行為，並驗證是否以正確的 Vm 為目標。 當`WhatIf`參數設定為 False 時，runbook 只會執行其定義的動作。

|Runbook | 參數 | 描述|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 從父系 Runbook 呼叫。 此 runbook 會針對自動停止案例，以每個資源為基礎建立警示。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目標訂用帳戶或資源群組中的 VM 上建立或更新 Azure 警示規則。 <br> `VMList`是以逗號分隔的 Vm 清單。 例如： `vm1, vm2, vm3` 。<br> `WhatIf`啟用 runbook 邏輯的驗證，而不執行。|
|AutoStop_Disable | None | 停用自動停止警示和預設排程。|
|AutoStop_VM_Child | WebHookData | 從父系 Runbook 呼叫。 警示規則會呼叫此 runbook 以停止傳統 VM。|
|AutoStop_VM_Child_ARM | WebHookData |從父系 Runbook 呼叫。 警示規則會呼叫此 runbook 來停止 VM。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 動作：啟動或停止<br> VMList  | 藉由雲端服務，在傳統 VM 群組中執行動作開始或停止。 |
|ScheduledStartStop_Child | VMName <br> 動作：啟動或停止 <br> resourceGroupName | 從父系 Runbook 呼叫。 針對排程的停止執行啟動或停止動作。|
|ScheduledStartStop_Child_Classic | VMName<br> 動作：啟動或停止<br> resourceGroupName | 從父系 Runbook 呼叫。 針對傳統 Vm 的排程停止執行啟動或停止動作。 |
|ScheduledStartStop_Parent | 動作：啟動或停止 <br>VMList <br> WhatIf：True 或 False | 啟動或停止訂用帳戶中的所有 Vm。 編輯變數`External_Start_ResourceGroupNames` ，並`External_Stop_ResourceGroupNames`只在這些目標資源群組上執行。 您也可以藉由更新`External_ExcludeVMNames`變數來排除特定的 vm。|
|SequencedStartStop_Parent | 動作：啟動或停止 <br> WhatIf：True 或 False<br>VMList| 在您要排序開始/停止活動的每個 VM 上，建立名為**sequencestart**和**sequencestop**的標記。 這些標記名稱會區分大小寫。 標記值應為正整數 (1, 2, 3)，對應至您要啟動或停止的順序。 <br>**注意**： vm 必須位於、 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`變數中定義的資源群組內。 虛擬機器必須具有適當的標記以使動作生效。|

### <a name="variables"></a>變數

下表列出在您自動化帳戶中建立的變數。 只修改前面加上`External`的變數。 修改前面加上`Internal`的變數會造成不想要的效果。

> [!NOTE]
> VM 名稱和資源群組的限制主要是變數大小的結果。 請參閱[Azure 自動化中的變數資產](https://docs.microsoft.com/azure/automation/shared-resources/variables)。

|變數 | 描述|
|---------|------------|
|External_AutoStop_Condition | 設定觸發警示之條件所需的條件運算子。 可接受的`GreaterThan`值`GreaterThanOrEqual`為`LessThan`、、 `LessThanOrEqual`和。|
|External_AutoStop_Description | 在 CPU 百分比超出閾值的情況下停止虛擬機器的警示。|
|External_AutoStop_Frequency | 規則的評估頻率。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。 |
|External_AutoStop_MetricName | 要設定 Azure 警示規則的效能計量名稱。|
|External_AutoStop_Severity | 計量警示的嚴重性，範圍可以從0到4。 |
|External_AutoStop_Threshold | 變數`External_AutoStop_MetricName`中指定的 Azure 警示規則的閾值。 百分比值的範圍是從1到100。|
|External_AutoStop_TimeAggregationOperator | 時間匯總運算子會套用至選取的視窗大小，以評估條件。 可接受的`Average`值`Minimum`為`Maximum`、 `Total`、、 `Last`和。|
|External_AutoStop_TimeWindow | Azure 分析所選計量以觸發警示的時間範圍大小。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。|
|External_EnableClassicVMs| 指定傳統 Vm 是否為解決方案目標的值。 預設值為 true。 針對 Azure 雲端解決方案提供者（CSP）訂用帳戶，將此變數設定為 False。 傳統 Vm 需要[傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。|
|External_ExcludeVMNames | 要排除的 VM 名稱清單（以逗號分隔），限制為140個 Vm。 如果您將超過140個 Vm 新增至清單，則設定為 [已排除] 的 Vm 可能會不慎啟動或停止。|
|External_Start_ResourceGroupNames | 以逗號分隔的清單，其中列出一個或多個以開始動作為目標的資源群組。|
|External_Stop_ResourceGroupNames | 一或多個以停止動作為目標之資源群組的逗號分隔清單。|
|External_WaitTimeForVMRetrySeconds |在 Vm 上針對**SequencedStartStop_Parent** runbook 執行動作所需的等候時間（以秒為單位）。 此變數可讓 runbook 在指定的秒數內等候子作業，再繼續進行下一個動作。 最長等候時間是10800或三小時。 預設值為2100秒。|
|Internal_AutomationAccountName | 指定自動化帳戶的名稱。|
|Internal_AutoSnooze_ARM_WebhookURI | 針對 Vm 的 AutoStop 案例呼叫的 webhook URI。|
|Internal_AutoSnooze_WebhookUri | 針對傳統 Vm 的 AutoStop 案例呼叫的 webhook URI。|
|Internal_AzureSubscriptionId | Azure 訂用帳戶識別碼。|
|Internal_ResourceGroupName | 自動化帳戶資源組名。|

>[!NOTE]
>若為變數`External_WaitTimeForVMRetryInSeconds`，預設值已從600更新為2100。 

在所有案例中，除了`External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` **AutoStop_CreateAlert_Parent**、 **SequencedStartStop_Parent**和**ScheduledStartStop_Parent** runbook 的以逗號分隔的 vm 清單以外，所有的變數、和`External_ExcludeVMNames`都是目標 vm 所需的。 也就是說，您的 Vm 必須屬於目標資源群組，才會發生啟動和停止動作。 此邏輯的運作方式類似 Azure 原則，其中您可以將訂用帳戶或資源群組設為目標，並讓新建立的 VM 繼承動作。 此方法可免去針對每部虛擬機器維護個別排程的必要，並可大規模地管理啟動和停止。

### <a name="schedules"></a>排程

下表列出在您的自動化帳戶中建立的各個預設排程。您可以修改它們，或建立自己的自訂排程。預設會停用所有排程，但**Scheduled_StartVM**和**Scheduled_StopVM**排程除外。

請勿啟用所有排程，因為這麼做可能會建立重迭的排程動作。 最好是判斷您想要執行的優化，並據以修改。 如需進一步說明，請參閱＜概觀＞一節中的範例案例。

|排程名稱 | 頻率 | 描述|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每 8 小時 | 每隔8小時執行**AutoStop_CreateAlert_Parent** runbook，這會在`External_Start_ResourceGroupNames`、 `External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`變數中停止以 VM 為基礎的值。 或者，您也可以使用`VMList`參數來指定以逗號分隔的 vm 清單。|
|Scheduled_StopVM | 使用者定義，每日 | 在指定**ScheduledStopStart_Parent**的時間，以`Stop`每日的參數執行 ScheduledStopStart_Parent runbook。自動停止符合變數資產所定義之規則的所有 Vm。啟用 [已排程的相關排程 **-StartVM**]。|
|Scheduled_StartVM | 使用者定義，每日 | 在指定**ScheduledStopStart_Parent**的時間執行`Start`每日參數值為的 ScheduledStopStart_Parent runbook。 會自動啟動所有符合變數資產所定義之規則的 Vm。啟用 [已排程的相關排程 **-scheduled-stopvm**]。|
|Sequenced-StopVM | 上午 1:00 (UTC)，每星期五 | 在指定**Sequenced_StopStop_Parent**的時間執行具有每個星期五`Stop`之參數值的 Sequenced_StopStop_Parent runbook。會以循序方式 (遞增) 停止具有由適當變數定義之 **SequenceStop** 標記的所有虛擬機器。 如需標記值和資產變數的詳細資訊，請參閱[runbook](#runbooks)。啟用相關排程 **Sequenced-StartVM**。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 在指定**SequencedStopStart_Parent**的時間，以`Start`每個星期一的參數值執行 SequencedStopStart_Parent runbook。 會以循序方式 (遞減) 啟動具有由適當變數定義之 **SequenceStart** 標記的所有虛擬機器。 如需標記值和變數資產的詳細資訊，請參閱[runbook](#runbooks)。 啟用相關排程 **Sequenced-StopVM**。

## <a name="use-of-the-solution-with-classic-vms"></a>搭配傳統 Vm 使用解決方案

如果您在傳統 Vm 的離峰時間使用 [**啟動/停止 vm** ] 解決方案，則自動化會依照每個雲端服務連續處理所有 vm。 Vm 仍會在不同的雲端服務之間平行處理。 

若要搭配傳統 Vm 使用解決方案，您需要傳統的執行身分帳戶，預設不會建立。 如需建立傳統執行身分帳戶的指示，請參閱[建立傳統執行身分帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。

如果您的每個雲端服務有20部以上的 Vm，以下是一些建議：

* 建立多個具有父 runbook **ScheduledStartStop_Parent**的排程，並指定每個排程20個 vm。 
* 在 [排程] 屬性中， `VMList`使用參數將 VM 名稱指定為逗號分隔清單。 

否則，如果此解決方案的自動化作業執行超過三個小時，則會根據[公平共用](automation-runbook-execution.md#fair-share)限制暫時卸載或停止該工作。

Azure CSP 訂用帳戶僅支援 Azure Resource Manager 模型。 程式中無法使用非 Azure Resource Manager 的服務。 當在**離峰時間執行「啟動/停止 Vm」解決方案**時，您可能會收到錯誤，因為它有管理傳統資源的 Cmdlet。 若要深入了解 CSP，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果您使用 CSP 訂用帳戶，您應該在部署之後，將[External_EnableClassicVMs](#variables)變數設為 False。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>啟用解決方案

若要開始使用解決方案，請遵循[啟用啟動/停止 vm 解決方案](automation-solution-vm-management-enable.md)中的步驟。

## <a name="view-the-solution"></a>檢視解決方案

當您啟用解決方案之後，請使用下列其中一種機制來存取它：

* 從您的自動化帳戶中，選取 [**相關資源**] 底下的 [**啟動/停止 VM** ]。 在 [啟動/停止 VM] 頁面的 [管理] [**啟動/停止 Vm 解決方案**] 底下，選取頁面右側的 [**管理解決方案**]。

* 流覽至與您的自動化帳戶連結的 Log Analytics 工作區。 選取工作區之後，請從左窗格中選擇 [**方案**]。 在 [解決方案] 頁面上，從清單中選取 **[啟動-停止 VM [工作區]]** 解決方案。  

選取該解決方案會顯示 [Start-Stop-VM[工作區]]**** 解決方案頁面。 您可以在這裡查看重要的詳細資料，例如 [ **StartStopVM** ] 磚中的資訊。 如同在 Log Analytics 工作區中，此圖格會顯示解決方案中已啟動並已順利完成的 Runbook 作業計數和圖形表示。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

您可以藉由按一下環圈圖格，來執行作業記錄的進一步分析。 解決方案儀表板會顯示作業歷程記錄和預先定義的記錄搜尋查詢。 切換至 log analytics advanced portal 以根據您的搜尋查詢進行搜尋。

## <a name="update-the-solution"></a>更新解決方案

如果您已部署此解決方案的先前版本，請先從帳戶中刪除它，再部署更新的版本。 遵循步驟來[移除解決方案](#remove-the-solution)，然後遵循步驟來[部署解決方案](automation-solution-vm-management-enable.md)。

## <a name="remove-the-solution"></a>移除解決方案

如果您不再需要使用解決方案，您可以從自動化帳戶中刪除它。 刪除解決方案只會移除 Runbook。 不會刪除新增解決方案時所建立的排程或變數。 如果您不使用其他 runbook，請手動移除這些資產。

若要刪除解決方案：

1. 從您的自動化帳戶，選取 [**相關資源**] 底下的 [**連結的工作區**]。

2. 選取 [**移至工作區**]。

3. 按一下 **[一般**] 下的 [**方案**]。 

4. 在 [解決方案] 頁面上，選取 [Start-Stop-VM[工作區]]**** 解決方案。 

5. 在 **[VMManagementSolution [工作區]]** 頁面上，從功能表中選取 [**刪除**]。<br><br> ![刪除 VM 管理解決方案](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在 [**刪除解決方案**] 視窗中，確認您想要刪除解決方案。

7. 在驗證資訊並刪除解決方案時，您可以在 [**通知**] 底下追蹤從功能表中選擇的進度。 在移除解決方案的程式啟動之後，您就會返回 [方案] 頁面。

此程序並不會刪除自動化帳戶和 Log Analytics 工作區。 如果您不想保留 Log Analytics 工作區，您必須從 Azure 入口網站手動將它刪除：

1. 搜尋並選取 [ **Log Analytics 工作區**]。

2. 在 [Log Analytics 工作區] 頁面上，選取工作區。

3. 在工作區 [設定] 頁面的功能表中選取 [刪除]****。

4. 如果您不想保留 Azure 自動化帳戶[解決方案元件](#solution-components)，可以手動刪除每個元件。

## <a name="next-steps"></a>後續步驟

針對您的 Azure Vm[啟用](automation-solution-vm-management-enable.md)[**在離峰期間啟動/停止 vm** ] 解決方案。
