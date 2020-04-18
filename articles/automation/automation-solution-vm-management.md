---
title: 非工作時間解決方案期間啟動/ 停止 VM
description: 此 VM 管理解決方案按計劃啟動和停止 Azure 虛擬機器,並從 Azure 監視器日誌主動監視。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 968e609772e08814a9943734d30c16bf6f5972e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604716"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>在 Azure 自動化中,在非工作時間解決方案期間啟動/停止 VM

**在非工作時間解決方案啟動/停止 VM**啟動或停止 Azure 虛擬機器。 它根據使用者定義的計劃啟動或停止計算機,通過 Azure 監視器日誌提供見解,並使用[操作組](../azure-monitor/platform/action-groups.md)發送可選電子郵件。 對於大多數方案,該解決方案同時支援 Azure 資源管理器和經典 VM。 

此解決方案可針對想要將 VM 成本最佳化的使用者，提供非集中式的低成本自動化選項。 使用此解決方案，您可以：

- [計畫 VM 啟動與停止](automation-solution-vm-management-config.md#schedule)。
- 使用[Azure 標記](automation-solution-vm-management-config.md#tags)(經典 VM 不支援)計劃 VM 以升序啟動和停止。
- 基於[低 CPU 使用率](automation-solution-vm-management-config.md#cpuutil)的自動停止 VM。

> [!NOTE]
> **已更新非工作時間解決方案期間的啟動/停止 VM,** 以支援可用的 Azure 模組的最新版本。

以下是目前解決方案的限制:

- 它管理任何區域中的 VM,但只能在與 Azure 自動化帳戶相同的訂閱中使用。
- 它在 Azure 和 Azure 政府中可用於支援日誌分析工作區、Azure 自動化帳戶和警報的任何區域。 Azure 政府區域當前不支援電子郵件功能。

## <a name="solution-prerequisites"></a>解決方案先決條件

此解決方案的 Runbook 會使用 [Azure 執行身分帳戶](automation-create-runas-account.md)。 運行 As 帳戶是首選身份驗證方法,因為它使用證書身份驗證而不是可能經常過期或更改的密碼。

我們建議您在**非工作時間解決方案期間為開始/停止 VM**使用單獨的自動化帳戶。 Azure 模組版本經常升級,其參數可能會更改。 解決方案不會以相同的節奏進行升級,並且可能無法與它使用的較新版本的cmdlet一起使用。 建議您在生產自動化帳戶中導入模組更新之前,先在測試自動化帳戶中測試這些更新。

## <a name="solution-permissions"></a>解決方案權限

您必須具有某些許可權才能**在非工作時間解決方案期間部署開始/停止 VM。** 如果解決方案使用預先創建的自動化帳戶和日誌分析工作區,則許可權因解決方案在部署期間創建新帳戶和工作區所需的許可權而異。 

如果您是訂閱的參與者,並且是 Azure 活動目錄租戶中的全域管理員,則無需配置許可權。 如果您沒有這些許可權或需要配置自定義角色,請確保具有下面描述的許可權。

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>預先存在的自動化帳戶和日誌分析工作區的許可權

要**將非工作時間解決方案的啟動/停止 VM**部署到現有的自動化帳戶和日誌分析工作區,部署該解決方案的使用者需要資源組作用域的以下許可權。 要瞭解有關角色的更多資訊,請參閱[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。

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

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>新自動化帳戶和新的紀錄分析工作區的權限

您可以將**非工作時間解決方案的「開始/停止 VM」** 部署到新的自動化帳戶和日誌分析工作區。 在這種情況下,部署解決方案的使用者需要上一節中定義的許可權以及本節中定義的許可權。 

部署解決方案的使用者需要以下角色:

- 訂閱時是共同管理員。 如果要管理經典 VM,則需要此角色來創建"經典運行即用"帳戶。 [默認情況下不再創建經典"運行帳戶"](automation-create-standalone-account.md#create-a-classic-run-as-account)
- [Azure 活動目錄](../active-directory/users-groups-roles/directory-assign-admin-roles.md)應用程式開發人員角色的成員身份。 有關設定「以帳戶身份執行」的詳細資訊,請參閱[設定「以帳戶身份執行」的權限](manage-runas-account.md#permissions)。
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

**非工作時間解決方案的啟動/停止 VM**包括預先設定的 Runbook、計畫和與 Azure 監視器日誌的整合。 您可以使用這些元素來客製化 VM 的啟動和關閉,以滿足您的業務需求。

### <a name="runbooks"></a>Runbook

下表列出了解決方案部署到自動化帳戶的 Runbook。 不要更改 Runbook 代碼。 相反地，為新功能撰寫自己的 Runbook。

> [!IMPORTANT]
> 不要直接運行任何附加其名稱**的子**簿。

所有父運行簿都包含`WhatIf`參數。 當設置為 True 時,參數支援詳細說明 Runbook 在沒有參數的情況下運行時所採取的確切行為,並驗證是否將正確的 VM 作為目標。 Runbook 僅`WhatIf`在 參數設置為 False 時執行其定義的操作。

|Runbook | 參數 | 描述|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 從父系 Runbook 呼叫。 此 Runbook 基於每個資源為自動停止方案創建警報。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目標訂用帳戶或資源群組中的 VM 上建立或更新 Azure 警示規則。 <br> `VMList`是 VM 的逗號分隔清單。 例如： `vm1, vm2, vm3` 。<br> `WhatIf`支援在不執行的情況下驗證 Runbook 邏輯。|
|AutoStop_Disable | None | 禁用自動停止警報和默認計劃。|
|AutoStop_VM_Child | WebHookData | 從父系 Runbook 呼叫。 警報規則調用此 Runbook 以停止經典 VM。|
|AutoStop_VM_Child_ARM | WebHookData |從父系 Runbook 呼叫。 警報規則調用此 Runbook 以停止 VM。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 動作：啟動或停止<br> VMList  | 按雲端服務在經典 VM 組中執行操作啟動或停止。 |
|ScheduledStartStop_Child | VMName <br> 動作：啟動或停止 <br> resourceGroupName | 從父系 Runbook 呼叫。 針對排程的停止執行啟動或停止動作。|
|ScheduledStartStop_Child_Classic | VMName<br> 動作：啟動或停止<br> resourceGroupName | 從父系 Runbook 呼叫。 為經典 VM 的計劃停止執行開始或停止操作。 |
|ScheduledStartStop_Parent | 動作：啟動或停止 <br>VMList <br> WhatIf：True 或 False | 啟動或停止訂閱中的所有 VM。 編輯變數`External_Start_ResourceGroupNames`,僅`External_Stop_ResourceGroupNames`在這些目標資源組上執行。 還可以通過更新`External_ExcludeVMNames`變數來排除特定的 VM。|
|SequencedStartStop_Parent | 動作：啟動或停止 <br> WhatIf：True 或 False<br>VMList| 在要為其排序開始/停止活動的每個 VM 上創建名為**序列啟動**和**序列停止**的標記。 這些標記名稱會區分大小寫。 標記值應為正整數 (1, 2, 3)，對應至您要啟動或停止的順序。 <br>**注意**:VM 必須`External_Start_ResourceGroupNames``External_Stop_ResourceGroupNames`位於 在 中定義`External_ExcludeVMNames`的資源組中 , 和變數中。 虛擬機器必須具有適當的標記以使動作生效。|

### <a name="variables"></a>變數

下表列出在您自動化帳戶中建立的變數。 僅修改預綴在`External`的變數。 修改預先固定的`Internal`變數會導致不良效果。

> [!NOTE]
> 對 VM 名稱和資源組的限制很大程度上是可變大小造成的。

|變數 | 描述|
|---------|------------|
|External_AutoStop_Condition | 設定觸發警示之條件所需的條件運算子。 可接受的值是`GreaterThan``GreaterThanOrEqual``LessThan`,`LessThanOrEqual`與 。|
|External_AutoStop_Description | 在 CPU 百分比超出閾值的情況下停止虛擬機器的警示。|
|External_AutoStop_Frequency | 規則的評估頻率。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。 |
|External_AutoStop_MetricName | 要設定 Azure 警示規則的效能計量名稱。|
|External_AutoStop_Severity | 指標警報的嚴重性,範圍從 0 到 4。 |
|External_AutoStop_Threshold | 變數`External_AutoStop_MetricName`中指定的 Azure 警報規則的閾值。 百分比值範圍從 1 到 100。|
|External_AutoStop_TimeAggregationOperator | 應用於所選視窗大小的時間聚合運算符用於評估條件。 可接受的值是`Average``Minimum``Maximum`、、`Total``Last`與 。|
|External_AutoStop_TimeWindow | Azure 分析用於觸發警報的選定指標的視窗的大小。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。|
|External_EnableClassicVMs| 指定解決方案是否針對經典 VM 的值。 預設值為 true。 為 Azure 雲端解決方案提供者 (CSP) 訂閱將此變數設定為 False。 傳統 VM 需要[經典執行為帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。|
|External_ExcludeVMNames | 要排除的 VM 名稱的 Comma 分隔清單,限制為 140 個 VM。 如果將超過 140 個 VM 添加到清單中,則設置為排除的 VM 可能會無意中啟動或停止。|
|External_Start_ResourceGroupNames | 針對啟動操作的一個或多個資源組的 Comma 分隔清單。|
|External_Stop_ResourceGroupNames | 一個或多個資源組的 Comma 分隔清單,這些資源組針對停止操作。|
|External_WaitTimeForVMRetrySeconds |在**SequencedStartStop_Parent** runbook 的 VM 上執行操作的等待時間(以秒為單位)。 此變數允許 Runbook 等待子操作指定秒數,然後再繼續執行下一個操作。 最長等待時間為 10800 或 3 小時。 默認值為 2100 秒。|
|Internal_AutomationAccountName | 指定自動化帳戶的名稱。|
|Internal_AutoSnooze_ARM_WebhookURI | Webhook URI 要求 VM 的自動停止方案。|
|Internal_AutoSnooze_WebhookUri | Webhook URI 要求為經典 VM 提供自動停止方案。|
|Internal_AzureSubscriptionId | Azure 訂用帳戶識別碼。|
|Internal_ResourceGroupName | 自動化帳戶資源組名稱。|

>[!NOTE]
>對於變數`External_WaitTimeForVMRetryInSeconds`,預設值從 600 更新到 2100。 

在所有方案中,`External_Start_ResourceGroupNames`變數和`External_Stop_ResourceGroupNames``External_ExcludeVMNames`是目標 VM 所必需的,但**AutoStop_CreateAlert_Parent、SequencedStartStop_Parent**和**ScheduledStartStop_Parent** **SequencedStartStop_Parent**runbook 的逗號分隔 VM 清單除外。 也就是說,VM 必須屬於目標資源組,才能啟動和停止執行操作。 此邏輯的運作方式類似 Azure 原則，其中您可以將訂用帳戶或資源群組設為目標，並讓新建立的 VM 繼承動作。 此方法可免去針對每部虛擬機器維護個別排程的必要，並可大規模地管理啟動和停止。

### <a name="schedules"></a>排程

下表列出在您的自動化帳戶中建立的各個預設排程。您可以修改它們，或建立自己的自訂排程。默認情況下,除**Scheduled_StartVM**和**Scheduled_StopVM**計畫外,所有計劃都已禁用。

不要啟用所有計劃,因為這樣做可能會創建重疊的計劃操作。 最好確定要執行哪些優化並相應地修改它們。 如需進一步說明，請參閱＜概觀＞一節中的範例案例。

|排程名稱 | 頻率 | 描述|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每 8 小時 | 每 8 小時運行**一次AutoStop_CreateAlert_Parent** runbook,這反過來`External_Start_ResourceGroupNames`又`External_Stop_ResourceGroupNames`會停止`External_ExcludeVMNames`中基於 VM 的值, 和變數。 或者,可以使用`VMList`參數指定逗號分隔的 VM 清單。|
|Scheduled_StopVM | 使用者定義的每日 | 在指定時間運行具有每天參數`Stop`**的ScheduledStopStart_Parent** runbook。自動停止滿足變數資產定義的規則的所有 VM。啟用相關計劃**計劃-啟動VM。**|
|Scheduled_StartVM | 使用者定義的每日 | 在指定時間運行具有`Start`每天參數值**的ScheduledStopStart_Parent** runbook。 自動啟動滿足變數資產定義的規則的所有 VM。啟用相關**計劃計劃-停止VM。**|
|Sequenced-StopVM | 上午 1:00 (UTC)，每星期五 | 在指定時間運行參數值為每週五`Stop`的 Sequenced_Parent runbook。會以循序方式 (遞增) 停止具有由適當變數定義之 **SequenceStop** 標記的所有虛擬機器。 如需標記值和資產變數的詳細資訊，請參閱 Runbook 一節。啟用相關排程 **Sequenced-StartVM**。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | **在指定**時間運行SequencedStopStart_Parent運行簿的參數值`Start`為每週一。 會以循序方式 (遞減) 啟動具有由適當變數定義之 **SequenceStart** 標記的所有虛擬機器。 有關標記值和變數資產的詳細資訊,請參閱[Runbook](#runbooks)。 啟用相關排程 **Sequenced-StopVM**。

## <a name="use-of-the-solution-with-classic-vms"></a>將解決方案與經典 VM 一起使用

如果您在**非工作時間使用針對經典 VM 的啟動/停止 VM**解決方案,則自動化將按每個雲端服務順序處理所有 VM。 VM 仍在不同的雲端服務中並行處理。 

要將解決方案與經典 VM 一起使用,您需要一個經典運行作為帳戶,預設情況下不創建該帳戶。 有關創建經典"作為"帳戶的說明,請參閱[創建經典運行作為帳戶](automation-create-standalone-account.md#create-a-classic-run-as-account)。

如果每個雲服務有超過 20 個 VM,以下是一些建議:

* 使用父 runbook 創建多個計畫**ScheduledStartStop_Parent**並為每個計畫指定 20 個 VM。 
* 在計劃屬性中,使用`VMList`參數將 VM 名稱指定為逗號分隔清單。 

否則,如果此解決方案的自動化作業運行超過三個小時,則根據[公平共用](automation-runbook-execution.md#fair-share)限制暫時卸載或停止該作業。

Azure CSP 訂閱僅支援 Azure 資源管理器模型。 非 Azure 資源管理員服務在程式中不可用。 當**非工作時間解決方案執行時啟動/停止 VM**執行時,您可能會收到錯誤,因為它具有管理經典資源的 cmdlet。 若要深入了解 CSP，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果使用 CSP 訂閱,則應在部署後將[External_EnableClassicVMs](#variables)變數設置為 False。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>啟用解決方案

要開始使用該解決方案,請按照[啟用開始/停止 VM 解決方案](automation-solution-vm-management-enable.md)中的步驟操作。

## <a name="view-the-solution"></a>檢視解決方案

啟用解決方案後,請使用以下機制之一存取解決方案:

* 從自動化帳戶中,選擇 **「相關資源**」下的 **「開始/停止 VM」。。** 在"開始/停止 VM"頁上,選擇"從頁面右側**管理解決方案**",在 **"管理開始/停止 VM 解決方案**"下。

* 導航到連結到自動化帳戶的日誌分析工作區。 選擇工作區后,從左側窗格中選擇 **"解決方案**"。 在「解決方案」頁上,從清單中選擇 **「開始-停止-VM_工作區]** 解決方案。  

選取該解決方案會顯示 [Start-Stop-VM[工作區]]**** 解決方案頁面。 在這裡,您可以查看重要詳細資訊,例如**StartStopVM**磁貼中的資訊。 如同在 Log Analytics 工作區中，此圖格會顯示解決方案中已啟動並已順利完成的 Runbook 作業計數和圖形表示。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

您可以通過單擊圓環磁貼對作業記錄執行進一步分析。 解決方案儀錶板顯示作業歷史記錄和預定義的日誌搜索查詢。 切換到日誌分析高級門戶,以便根據您的搜索查詢進行搜索。

## <a name="update-the-solution"></a>更新解決方案

如果您已部署此解決方案的早期版本,請將其從帳戶中刪除,然後再部署更新的版本。 依步驟[刪除解決方案,](#remove-the-solution)然後按照步驟[部署解決方案](automation-solution-vm-management-enable.md)。

## <a name="remove-the-solution"></a>移除解決方案

如果不再需要使用解決方案,可以從自動化帳戶中刪除它。 刪除解決方案只會移除 Runbook。 不會刪除新增解決方案時所建立的排程或變數。 如果不將這些資產與其他 Runbook 一起使用,請手動刪除這些資產。

要移除解決方案:

1. 從自動化帳戶中,在 **「相關資源**」下選擇 **「連結工作區**」。。

2. 選擇 **「轉到工作區**」。

3. 點選 **「一般**」**的解決方案**。 

4. 在 [解決方案] 頁面上，選取 [Start-Stop-VM[工作區]]**** 解決方案。 

5. 在**VM 管理解決方案[工作區]** 頁上,選擇"從功能表**中刪除**"。<br><br> ![刪除 VM 管理解決方案](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在 **「刪除解決方案」** 視窗中,確認要刪除解決方案。

7. 當資訊被驗證並刪除解決方案時,您可以在從功能表中選擇**的「通知**」下追蹤進度。 刪除解決方案的過程開始後,您將返回到"解決方案"頁。

此程序並不會刪除自動化帳戶和 Log Analytics 工作區。 如果不想保留日誌分析工作區,則必須手動將其從 Azure 門戶中刪除:

1. 搜尋並選擇**紀錄分析工作區**。

2. 在日誌分析工作區頁面上,選擇工作區。

3. 在工作區 [設定] 頁面的功能表中選取 [刪除]****。

4. 如果不想保留 Azure 自動化帳戶[解決方案元件](#solution-components),可以手動刪除每個元件。

## <a name="next-steps"></a>後續步驟

為 Azure VM[在](automation-solution-vm-management-enable.md)**非工作時間解決方案啟用開始/停止 VM。**
