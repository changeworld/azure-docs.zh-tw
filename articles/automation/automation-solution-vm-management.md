---
title: 「停機期間啟動/停止 VM」解決方案
description: 此 VM 管理解決方案按計劃啟動和停止 Azure 虛擬機器,並從 Azure 監視器日誌主動監視。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 906c7728365cc902549bd46c57972e1c90af979c
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607484"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure 自動化中的「停機期間啟動/停止 VM」解決方案

在非工作時間啟動/停止 VM 解決方案啟動並停止 Azure 虛擬機器的使用者定義計畫,透過 Azure 監視器日誌提供見解,並使用[操作組](../azure-monitor/platform/action-groups.md)發送可選電子郵件。 針對大多數的案例，它皆能同時支援 Azure Resource Manager 和傳統 VM。 

要將此解決方案與經典 VM 一起使用,您需要一個經典 RunAs 帳戶,預設情況下不創建該帳戶。 有關創建經典 RunAs 帳號的說明,請參閱[經典執行帳戶](automation-create-standalone-account.md#classic-run-as-accounts)。

> [!NOTE]
> 已更新非工作時間解決方案期間的啟動/停止 VM,以支援可用的 Azure 模組的最新版本。

此解決方案可針對想要將 VM 成本最佳化的使用者，提供非集中式的低成本自動化選項。 使用此解決方案，您可以：

- [計畫 VM 啟動與停止](automation-solution-vm-management-config.md#schedule)。
- 使用[Azure 標記](automation-solution-vm-management-config.md#tags)(經典 VM 不支援)計劃 VM 以升序啟動和停止。
- 基於[低 CPU 使用率](automation-solution-vm-management-config.md#cpuutil)的自動停止 VM。

以下是目前解決方案的限制:

- 此解決方案可管理任何區域中的 VM，但是只能使用於與 Azure 自動化帳戶相同的訂用帳戶中。
- 此解決方案在 Azure 和 Azure 政府中可用於支援日誌分析工作區、Azure 自動化帳戶和警報的任何區域。 Azure 政府區域當前不支援電子郵件功能。

> [!NOTE]
> 如果您對傳統 VM 使用此解決方案，那麼您所有的 VM 將會針對每個雲端服務依序進行處理。 若是跨不同的雲端服務，則仍會以平行方式處理虛擬機器。 如果每個雲服務的 VM 超過 20 個,我們建議使用父 runbook 創建多個計畫**ScheduledStartStop_Parent**並指定每個計劃 20 個 VM。 在計畫屬性中,在**VMList**參數中指定為逗號分隔清單的 VM 名稱。 否則,如果此解決方案的自動化作業運行超過三個小時,則根據[公平共用](automation-runbook-execution.md#fair-share)限制暫時卸載或停止該作業。
>
> 「Azure 雲端解決方案提供者」(Azure CSP) 訂用帳戶僅支援 Azure Resource Manager 模型，因此本方案未提供非 Azure Resource Manager 服務。 執行「啟動/停止」解決方案時，您可能會收到錯誤，因為它具有可管理傳統資源的 Cmdlet。 若要深入了解 CSP，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果您使用 CSP 訂用帳戶，應該在部署之後，將 [**External_EnableClassicVMs**](#variables) 變數修改為 **False**。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

此解決方案的 Runbook 會使用 [Azure 執行身分帳戶](automation-create-runas-account.md)。 執行身分帳戶是慣用的驗證方法，因為它使用憑證驗證，而不是會過期或經常變更的密碼。

我們建議您對啟動/停止 VM 解決方案使用單獨的自動化帳戶。 這是因為 Azure 模組版本經常升級,並且其參數可能會更改。 啟動/停止 VM 解決方案不會以相同的節奏升級,因此它可能無法使用它使用的較新版本的 cmdlet。 我們還建議您在在生產自動化帳戶中導入模組更新之前,在測試自動化帳戶中測試這些更新。

### <a name="permissions"></a>權限

用戶必須在非工作時間解決方案期間部署"開始/停止 VM"的某些許可權。 如果使用預先創建的自動化帳戶和日誌分析工作區或在部署期間創建新許可權,則這些許可權是不同的。 如果您是訂閱的參與者,並且是 Azure 活動目錄租戶中的全域管理員,則無需配置以下許可權。 如果您沒有這些許可權或需要配置自定義角色,請參閱下面的許可權。

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>預先存在的自動化帳戶和日誌分析工作區

要將「開始/停止 VM」解決方案在非工作時間將解決方案部署到現有的自動化帳戶和日誌分析工作區,部署該解決方案的使用者需要在**資源組**上具有以下許可權。 要瞭解有關角色的更多資訊,請參閱[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。

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
| 微軟.自動化/自動化會計/作業計劃/寫入 | 資源群組 |
| Microsoft.Automation/automationAccounts/jobs/write | 資源群組 |
| Microsoft.Automation/automationAccounts/jobs/read | 資源群組 |
| Microsoft.OperationsManagement/solutions/write | 資源群組 |
| 微軟.操作見解/工作空間/* | 資源群組 |
| 微軟.見解/診斷設置/寫入 | 資源群組 |
| Microsoft.Insights/ActionGroups/Write | 資源群組 |
| 微軟.見解/行動組/閱讀 | 資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 資源群組 |
| Microsoft.Resources/deployments/* | 資源群組 |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>新的自動化帳戶和新紀錄分析工作區

要將非工作時間啟動/停止 VM 解決方案部署到新的自動化帳戶和日誌分析工作區,部署解決方案的使用者需要上一節中定義的許可權以及以下許可權:

- 訂閱時的共同管理員 - 僅當您要管理經典 VM 時,才需要創建經典運行帳戶。 預設使用預設的[RunAs 帳號](automation-create-standalone-account.md#classic-run-as-accounts)。
- [Azure 活動目錄](../active-directory/users-groups-roles/directory-assign-admin-roles.md)**應用程式開發人員**角色的成員。 有關設定「以帳戶身份執行」的詳細資訊,請參閱[設定「以帳戶身份執行」的權限](manage-runas-account.md#permissions)。
- 訂閱或以下許可權上的參與者。

| 權限 |影響範圍|
| --- | --- |
| 微軟.授權/操作/讀取 | 訂用帳戶|
| Microsoft.Authorization/permissions/read |訂用帳戶|
| Microsoft.Authorization/roleAssignments/read | 訂用帳戶 |
| Microsoft.Authorization/roleAssignments/write | 訂用帳戶 |
| Microsoft.Authorization/roleAssignments/delete | 訂用帳戶 |
| Microsoft.Automation/automationAccounts/connections/read | 資源群組 |
| Microsoft.Automation/automationAccounts/certificates/read | 資源群組 |
| Microsoft.Automation/automationAccounts/write | 資源群組 |
| Microsoft.OperationalInsights/workspaces/write | 資源群組 |

## <a name="solution-components"></a>方案元件

此解決方案包括預先設定的 Runbook、計畫和與 Azure 監視器日誌的整合,以便您可以客製化虛擬機器的啟動和關閉以滿足業務需求。

### <a name="runbooks"></a>Runbook

下表列出透過此解決方案部署至您自動化帳戶的 Runbook。 請勿變更 Runbook 程式碼。 相反地，為新功能撰寫自己的 Runbook。

> [!IMPORTANT]
> 不要直接運行任何附加其名稱*的子*簿。

所有父代 Runbook 皆包含 _WhatIf_ 參數。 將 _WhatIf_ 設為 **True** 時，即可支援詳述在不使用 _WhatIf_ 參數執行的情況下，該 Runbook 會採取的確切行為，並驗證是否已將正確的虛擬機器作為目標。 _WhatIf_ 參數設為 **False** 時，Runbook 只會執行其定義的動作。

|Runbook | 參數 | 描述|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 從父系 Runbook 呼叫。 此 Runbook 會針對 AutoStop 案例以每個資源為基礎建立警示。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目標訂用帳戶或資源群組中的 VM 上建立或更新 Azure 警示規則。 <br> VMList：以逗號分隔的虛擬機器清單。 例如，vm1、vm2、vm3__。<br> WhatIf** 會驗證 Runbook 邏輯而不會執行。|
|AutoStop_Disable | 無 | 停用 AutoStop 警示和預設排程。|
|AutoStop_VM_Child | WebHookData | 從父系 Runbook 呼叫。 警報規則調用此 Runbook 以停止經典 VM。|
|AutoStop_VM_Child_ARM | WebHookData |從父系 Runbook 呼叫。 警示規則會呼叫此 Runbook 以停止虛擬機器。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 動作：啟動或停止<br> VMList  | 此 Runbook 用於在雲端服務的經典 VM 組中執行操作啟動或停止。<br> VMList：以逗號分隔的虛擬機器清單。 例如，vm1、vm2、vm3__。 |
|ScheduledStartStop_Child | VMName <br> 動作：啟動或停止 <br> resourceGroupName | 從父系 Runbook 呼叫。 針對排程的停止執行啟動或停止動作。|
|ScheduledStartStop_Child_Classic | VMName<br> 動作：啟動或停止<br> resourceGroupName | 從父系 Runbook 呼叫。 為經典 VM 的計劃停止執行開始或停止操作。 |
|ScheduledStartStop_Parent | 動作：啟動或停止 <br>VMList <br> WhatIf：True 或 False | 此設定會影響訂用帳戶中的所有虛擬機器。 編輯 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames**，以便只在這些目標資源群組上執行。 您也可以更新 **External_ExcludeVMNames** 變數來排除特定的 VM。<br> VMList：以逗號分隔的虛擬機器清單。 例如，vm1、vm2、vm3__。<br> WhatIf__ 會驗證 Runbook 邏輯而不會執行。|
|SequencedStartStop_Parent | 動作：啟動或停止 <br> WhatIf：True 或 False<br>VMList| 在要為其排序開始/停止活動的每個 VM 上創建名為**序列啟動**和**序列停止**的標記。 這些標記名稱會區分大小寫。 標記值應為正整數 (1, 2, 3)，對應至您要啟動或停止的順序。 <br> VMList：以逗號分隔的虛擬機器清單。 例如，vm1、vm2、vm3__。 <br> WhatIf__ 會驗證 Runbook 邏輯而不會執行。 <br> **注意**：虛擬機器必須位於定義於 Azure 自動化變數中 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 的資源群組內。 虛擬機器必須具有適當的標記以使動作生效。|

### <a name="variables"></a>變數

下表列出在您自動化帳戶中建立的變數。 僅修改前面加上 **External** 的變數。 修改前面加上 **Internal** 的變數會造成非預期的結果。

|變數 | 描述|
|---------|------------|
|External_AutoStop_Condition | 設定觸發警示之條件所需的條件運算子。 可接受的值為 **GreaterThan**、**GreaterThanOrEqual**、**LessThan** 和 **LessThanOrEqual**。|
|External_AutoStop_Description | 在 CPU 百分比超出閾值的情況下停止虛擬機器的警示。|
|External_AutoStop_Frequency | 規則的評估頻率。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。 |
|External_AutoStop_MetricName | 要設定 Azure 警示規則的效能計量名稱。|
|External_AutoStop_Severity | 指標警報的嚴重性,範圍從 0 到 4。 |
|External_AutoStop_Threshold | 適用於在變數 _External_AutoStop_MetricName_ 中指定之 Azure 警示規則的閾值。 百分比值的範圍為 1 至 100。|
|External_AutoStop_TimeAggregationOperator | 會套用至選取的視窗大小以評估條件的時間彙總運算子。 可接受的值為 **Average**、**Minimum**、**Maximum**、**Total** 和 **Last**。|
|External_AutoStop_TimeWindow | Azure 分析選取之計量以觸發警示的視窗大小。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。|
|External_EnableClassicVMs| 指定傳統虛擬機器是否為解決方案設定的目標。 預設值為 true。 對於 CSP 訂用帳戶，應該設為 False。 經典 VM 需要[傳統執行帳號](automation-create-standalone-account.md#classic-run-as-accounts)。|
|External_ExcludeVMNames | 輸入要排除的虛擬機器名稱，請使用不含空格的逗號來分隔名稱。 此上限為 140 個 VM。 如果將超過 140 個 VM 添加到此逗號分隔清單中,則設置為排除的 VM 可能會無意中啟動或停止。|
|External_Start_ResourceGroupNames | 使用逗號分隔值指定一或多個作為啟動動作目標的資源群組。|
|External_Stop_ResourceGroupNames | 使用逗號分隔值指定一或多個作為停止動作目標的資源群組。|
|External_WaitTimeForVMRetrySeconds |在序列啟動/停止執行簿的 VM 上執行操作的等待時間(以秒為單位)。<br> 默認值為 2100 秒,支援配置為最大值 10800 或 3 小時。|
|Internal_AutomationAccountName | 指定自動化帳戶的名稱。|
|Internal_AutoSnooze_ARM_WebhookURI | 為 VM 的自動停止方案指定 Webhook URI。|
|Internal_AutoSnooze_WebhookUri | 為經典 VM 的自動停止方案指定 Webhook URI。|
|Internal_AzureSubscriptionId | 指定 Azure 訂用帳戶識別碼。|
|Internal_ResourceGroupName | 指定自動化帳戶資源群組名稱。|

>[!NOTE]
>對於變數**External_WaitTimeForVMRetryInSeconds**,預設值已從 600 更新到 2100。 此變數允許**序列啟動/停止方案**執行簿等待子操作指定秒數,然後再繼續執行下一個操作。
>

在所有情況下，**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 變數皆為設定目標虛擬機器的必要項目 (除了為 **AutoStop_CreateAlert_Parent**、**SequencedStartStop_Parent** 和 **ScheduledStartStop_Parent** Runbook 提供以逗號分隔的虛擬機器清單以外)。 也就是說，您的虛擬機器必須位於目標資源群組中，才會發生啟動和停止動作。 此邏輯的運作方式類似 Azure 原則，其中您可以將訂用帳戶或資源群組設為目標，並讓新建立的 VM 繼承動作。 此方法可免去針對每部虛擬機器維護個別排程的必要，並可大規模地管理啟動和停止。

### <a name="schedules"></a>排程

下表列出在您的自動化帳戶中建立的各個預設排程。您可以修改它們，或建立自己的自訂排程。除了 **Scheduled_StartVM** 和 **Scheduled_StopVM** 以外，所有排程皆預設為停用。

您不應啟用所有排程，因為這樣可能會產生重疊的排程動作。 最好能先判斷要執行哪些最佳化，並據以做出相對應的修改。 如需進一步說明，請參閱＜概觀＞一節中的範例案例。

|排程名稱 | 頻率 | 描述|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每 8 小時 | 每隔 8 小時會執行 AutoStop_CreateAlert_Parent Runbook，這會停止在 Azure 自動化變數中 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 中的虛擬機器基底值。 或者，您可以使用 VMList 參數指定以逗號分隔的虛擬機器清單。|
|Scheduled_StopVM | 使用者定義，每日 | 每天會在指定時間搭配 _Stop_ 參數執行 Scheduled_Parent Runbook。會自動停止符合由資產變數所定義之規則的所有虛擬機器。啟用相關排程 **Scheduled-StartVM**。|
|Scheduled_StartVM | 使用者定義，每日 | 每天會在指定時間搭配 _Start_ 參數執行 Scheduled_Parent Runbook。 會自動啟動符合由適當變數所定義之規則的所有虛擬機器。啟用相關排程 **Scheduled-StopVM**。|
|Sequenced-StopVM | 上午 1:00 (UTC)，每星期五 | 每星期五會在指定時間搭配參數 _Stop_ 執行 Sequenced_Parent Runbook。會以循序方式 (遞增) 停止具有由適當變數定義之 **SequenceStop** 標記的所有虛擬機器。 如需標記值和資產變數的詳細資訊，請參閱 Runbook 一節。啟用相關排程 **Sequenced-StartVM**。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 每星期一會在指定時間搭配參數 _Start_ 執行 Sequenced_Parent Runbook。 會以循序方式 (遞減) 啟動具有由適當變數定義之 **SequenceStart** 標記的所有虛擬機器。 如需標記值和資產變數的詳細資訊，請參閱 Runbook 一節。 啟用相關排程 **Sequenced-StopVM**。|

## <a name="enable-the-solution"></a>啟用解決方案

要開始使用該解決方案,請使用[啟用開始/停止 VM 解決方案](automation-solution-vm-management-enable.md)中的步驟。

## <a name="viewing-the-solution"></a>檢視解決方案

在透過以下方式之一啟用解決方案後,可以存取該解決方案:

* 從自動化帳戶中,選擇 **「相關資源**」下的 **「開始/停止 VM」。。** 在 **「開始/停止 VM」** 頁上,選擇「從頁面右側**管理解決方案**」,在 **「管理開始/停止 VM 解決方案**」一節中。

* 導航到連結到自動化帳戶的日誌分析工作區,選擇工作區後,從左側窗格中選擇 **"解決方案**"。 在 **「解決方案**」頁上,從清單中選擇 **「開始-停止-VM_工作區]** 解決方案。  

選取該解決方案會顯示 [Start-Stop-VM[工作區]]**** 解決方案頁面。 在這裡您可以檢閱重要的詳細資料，如 [StartStopVM]**** 圖格。 如同在 Log Analytics 工作區中，此圖格會顯示解決方案中已啟動並已順利完成的 Runbook 作業計數和圖形表示。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

您可以從這裡按一下環圈圖格，執行進一步的作業記錄分析。 解決方案儀表板會顯示作業記錄和預先定義的記錄搜尋查詢。 切換到日誌分析高級門戶,以便根據您的搜索查詢進行搜索。

## <a name="update-the-solution"></a>更新解決方案

如果您已部署此解決方案的舊版，就必須先從帳戶中刪除它，才能部署更新的版本。 依步驟[刪除解決方案,](#remove-the-solution)然後按照步驟[部署解決方案](automation-solution-vm-management-enable.md)。

## <a name="remove-the-solution"></a>移除解決方案

如果您決定不再需要使用解決方案，您可以將它從自動化帳戶中刪除。 刪除解決方案只會移除 Runbook。 不會刪除新增解決方案時所建立的排程或變數。 如果您不搭配其他 Runbook 使用這些資產，則必須以手動方式加以刪除。

若要刪除解決方案，請執行下列步驟：

1. 從您的自動化帳戶中,在 **「相關資源**」下,選擇 **「連結工作區**」。

2. 選擇 **「轉到工作區**」。

3. 在 **「常規」** 下,選擇 **「解決方案**」。 

4. 在 [解決方案]**** 頁面上，選取 [Start-Stop-VM[工作區]]**** 解決方案。 在 [VMManagementSolution[工作區]]**** 頁面上，選取功能表中的 [刪除]****。<br><br> ![刪除 VM 管理解決方案](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. 在 [刪除解決方案]**** 視窗中，確認您要刪除解決方案。

6. 在驗證資訊並刪除解決方案後，您可以在功能表的 [通知]**** 底下追蹤其進度。 移除解決方案的程序啟動之後，您會返回 [解決方案]**** 頁面。

此程序並不會刪除自動化帳戶和 Log Analytics 工作區。 如果不想保留 Log Analytics 工作區，則必須手動刪除它。 此作業可以從 Azure 入口網站完成：

1. 在 Azure 門戶中,搜尋並選擇**日誌分析工作區**。

2. 在**日誌分析工作區**頁面上,選擇工作區。

3. 在工作區 [設定] 頁面的功能表中選取 [刪除]****。

如果您不想保留 Azure 自動化帳戶元件，您可以手動加以刪除。 如需解決方案所建立的 Runbook、變數和排程清單，請參閱[解決方案元件](#solution-components)。

## <a name="next-steps"></a>後續步驟

為 Azure VM[啟用](automation-solution-vm-management-enable.md)在非工作時間開始/停止解決方案。
