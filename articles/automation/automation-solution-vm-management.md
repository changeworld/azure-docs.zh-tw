---
title: 「停機期間啟動/停止 VM」解決方案
description: 此 VM 管理解決方案會依排程啟動和停止您的 Azure Resource Manager 虛擬機器，並從 Azure 監視器記錄中主動進行監視。
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373182"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure 自動化中的「停機期間啟動/停止 VM」解決方案

[在離峰期間啟動/停止 Vm] 解決方案會依照使用者定義的排程啟動和停止 Azure 虛擬機器、提供 Azure 監視器記錄的深入解析，並使用[動作群組](../azure-monitor/platform/action-groups.md)來傳送選用的電子郵件。 針對大多數的案例，它皆能同時支援 Azure Resource Manager 和傳統 VM。 若要搭配傳統 Vm 使用此解決方案，您需要傳統的 RunAs 帳戶，預設不會建立。 如需建立傳統 RunAs 帳戶的指示，請參閱[傳統執行身分帳戶](automation-create-standalone-account.md#classic-run-as-accounts)。

> [!NOTE]
> 當您部署解決方案時，會使用匯入到您的自動化帳戶的 Azure 模組來測試在離峰期間啟動/停止 Vm 解決方案。 此解決方案目前無法與較新版本的 Azure 模組搭配使用。 這只會影響您用來在離峰時間執行啟動/停止 Vm 解決方案的自動化帳戶。 您仍然可以在其他自動化帳戶中使用較新版本的 Azure 模組，如[如何更新中的 Azure PowerShell 模組中](automation-update-azure-modules.md)所述 Azure 自動化

此解決方案可針對想要將 VM 成本最佳化的使用者，提供非集中式的低成本自動化選項。 使用此解決方案，您可以：

- 對虛擬機器進行排程以啟動和停止。
- 使用 Azure 標記以遞增順序對虛擬機器進行排程以啟動和停止 (不支援傳統虛擬機器)。
- 根據低 CPU 使用率自動停止虛擬機器。

下列是目前解決方案的限制：

- 此解決方案可管理任何區域中的 VM，但是只能使用於與 Azure 自動化帳戶相同的訂用帳戶中。
- 在支援 Log Analytics 工作區、Azure 自動化帳戶及警示的任何地區中，此解決方案可在 Azure 和 AzureGov 中使用。 AzureGov 區域目前不支援電子郵件功能。

> [!NOTE]
> 如果您對傳統 VM 使用此解決方案，那麼您所有的 VM 將會針對每個雲端服務依序進行處理。 若是跨不同的雲端服務，則仍會以平行方式處理虛擬機器。 如果您的每個雲端服務有20部以上的 Vm，建議您建立多個具有父 runbook **ScheduledStartStop_Parent**的排程，並指定每個排程20個 vm。 在 [排程] 屬性中，將**VMList**參數中的 VM 名稱指定為逗號分隔清單。 否則，如果此解決方案的自動化作業執行超過三個小時，則會根據[公平共用](automation-runbook-execution.md#fair-share)限制暫時卸載或停止該工作。
>
> 「Azure 雲端解決方案提供者」(Azure CSP) 訂用帳戶僅支援 Azure Resource Manager 模型，因此本方案未提供非 Azure Resource Manager 服務。 執行「啟動/停止」解決方案時，您可能會收到錯誤，因為它具有可管理傳統資源的 Cmdlet。 若要深入了解 CSP，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果您使用 CSP 訂用帳戶，應該在部署之後，將 [**External_EnableClassicVMs**](#variables) 變數修改為 **False**。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

此解決方案的 Runbook 會使用 [Azure 執行身分帳戶](automation-create-runas-account.md)。 執行身分帳戶是慣用的驗證方法，因為它使用憑證驗證，而不是會過期或經常變更的密碼。

我們建議您針對啟動/停止 VM 解決方案使用不同的自動化帳戶。 這是因為 Azure 模組版本經常升級，而且其參數可能會變更。 啟動/停止 VM 解決方案不會以相同的步調升級，因此它可能無法與它所使用的較新版本 Cmdlet 搭配使用。 我們也建議您先在測試自動化帳戶中測試模組更新，再將它們匯入您的生產自動化帳戶。

### <a name="permissions-needed-to-deploy"></a>部署所需的許可權

在下班時間解決方案中，使用者必須擁有特定許可權，才能部署啟動/停止 Vm。 如果使用預先建立的自動化帳戶和 Log Analytics 工作區，或在部署期間建立新的，則這些許可權會不同。 如果您是訂用帳戶的參與者，以及 Azure Active Directory 租使用者中的全域管理員，則不需要設定下列許可權。 如果您沒有這些許可權，或需要設定自訂角色，請參閱下面所需的許可權。

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>既有的自動化帳戶和 Log Analytics 工作區

若要在下班時間將啟動/停止 Vm 解決方案部署到現有的自動化帳戶和 Log Analytics 工作區，部署解決方案的使用者需要**資源群組**的下列許可權。 若要深入瞭解角色，請參閱[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。

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

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>新的自動化帳戶和新的 Log Analytics 工作區

若要在下班時間將啟動/停止 Vm 解決方案部署到新的自動化帳戶和 Log Analytics 工作區，部署解決方案的使用者需要在上一節中定義的許可權，以及下列許可權：

- 訂用帳戶上的共同管理員-只有在您即將管理傳統 Vm 時，才需要建立傳統執行身分帳戶。 預設不會再建立[傳統 RunAs 帳戶](automation-create-standalone-account.md#classic-run-as-accounts)。
- [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **應用程式開發人員**角色的成員。 如需設定執行身分帳戶的詳細資訊，請參閱[設定執行身分帳戶的許可權](manage-runas-account.md#permissions)。
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

## <a name="deploy-the-solution"></a>部署解決方案

執行下列步驟，將「停機期間啟動/停止 VM」解決方案新增至您的自動化帳戶，然後設定變數以自訂解決方案。

1. 從 [自動化帳戶]，選取 [相關資源] 之下的 [啟動/停止 VM]。 您可以在此按一下**深入了解並啟用解決方案**。 如果您已經部署「啟動/停止 VM」解決方案，按一下 [管理解決方案] 並且在清單中選找它，即可加以選取。

   ![從自動化帳戶啟用](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > 您也可以按一下 [建立資源]，在 Azure 入口網站中的任何地方建立它。 在 [Marketplace] 頁面中輸入關鍵字，例如**啟動**或**啟動/停止**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 或者，您可以輸入解決方案完整名稱中的一或多個關鍵字，然後按 Enter 鍵。 從搜尋結果中選取 [停機期間啟動/停止 VM]。

2. 在所選解決方案的 [停機期間啟動/停止 VM] 頁面中，檢閱摘要資訊，然後按一下 [建立]。

   ![Azure 入口網站](media/automation-solution-vm-management/azure-portal-01.png)

3. [新增解決方案] 頁面隨即出現。 系統會在其中提示您設定解決方案，以將它匯入您的自動化訂用帳戶。

   ![VM 管理的新增解決方案頁面](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. 在 [新增解決方案] 頁面中，選取 [工作區]。 選取連結到自動化帳戶所在之同一 Azure 訂用帳戶的 Log Analytics 工作區。 如果您沒有工作區，請選取 [建立新工作區]。 在 [ **Log Analytics 工作區**] 頁面上，執行下列步驟：
   - 指定新**Log Analytics 工作區**的名稱，例如 "ContosoLAWorkspace"。
   - 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
   - 對於 [資源群組]，您可以建立新的資源群組，或選取現有的資源群組。
   - 選取 [位置]。
   - 選取 **定價層**。 選擇 [每 GB (獨立)] 選項。 Azure 監視器記錄具有更新的[價格](https://azure.microsoft.com/pricing/details/log-analytics/)，而每 GB 層是唯一的選項。

   > [!NOTE]
   > 啟用解決方案時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。
   >
   > 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

5. 在 [Log Analytics 工作區] 頁面上提供必要資訊之後，按一下 [建立]。 您可以在功能表的 [通知] 下追蹤其進度，這會在完成時帶您返回 [新增解決方案] 頁面。
6. 在 [新增解決方案] 頁面中，選取 [自動化帳戶]。 如果要建立新的 Log Analytics 工作區，您可以建立要與其相關聯的新自動化帳戶，或選取尚未連結至 Log Analytics 工作區的現有自動化帳戶。 選取現有自動化帳戶或按一下 [建立自動化帳戶]，然後在 [新增自動化帳戶] 頁面上提供下列資訊︰
   - 在 [名稱] 欄位中輸入自動化帳戶的名稱。

     系統會根據所選的 Log Analytics 工作區自動填入所有其他選項。 這些選項無法修改。 Azure 執行身分帳戶是此方案內含 Runbook 的預設驗證方法。 按下 [確定] 後，就會驗證設定選項並建立自動化帳戶。 您可以在功能表的 [通知] 底下追蹤其進度。

7. 最後，在 [新增解決方案] 頁面中，選取 [設定]。 [參數] 頁面隨即出現。

   ![解決方案的 [參數] 頁面](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   在這裡，系統會提示您：
   - 指定 [目標資源群組名稱]。 這些值是包含此解決方案所要管理的虛擬機器之資源群組名稱。 您可以輸入多個名稱，然後使用逗號加以分隔 (值不區分大小寫)。 如果您想要以訂用帳戶的所有資源群組中的 VM 為目標，則可使用萬用字元。 此值儲存在 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 變數中。
   - 指定 [虛擬機器排除清單 (字串)]。 此值是來自目標資源群組的一或多個虛擬機器名稱。 您可以輸入多個名稱，然後使用逗號加以分隔 (值不區分大小寫)。 支援使用萬用字元。 這個值會儲存在 **External_ExcludeVMNames** 變數中。
   - 選取**排程**。 選取排程的日期和時間。 將會從您選取的時間開始，建立每日重複發生的排程。 無法選取不同的區域。 在設定解決方案後，若要將排程設定為特定時區，請參閱[修改啟動和關機排程](#modify-the-startup-and-shutdown-schedules)。
   - 若要從動作群組接收**電子郵件通知**，請接受預設值 [是]，並提供有效的電子郵件地址。 如果您選取 [否]，但是日後決定想要收到電子郵件通知，您可以更新[動作群組](../azure-monitor/platform/action-groups.md)，該群組是以逗號分隔的有效電子郵件地址所建立。 您還需要啟用下列警示規則︰

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > 「目標資源群組名稱」的預設值為 **&ast;** 。 這樣會以訂用帳戶中的所有 VM 為目標。 如果您不想解決方案以您訂用帳戶中的所有 VM 為目標，在啟用排程之前，必須先將此值更新為資源群組名稱清單。

8. 設定好解決方案所需的初始設定後，按一下 [確定] 以關閉 [參數] 頁面，然後選取 [建立]。 驗證過所有設定之後，解決方案即會部署到您的訂用帳戶。 此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。

> [!NOTE]
> 如果您有 Azure 雲端解決方案提供者（Azure CSP）訂用帳戶，在部署完成之後，請移至 [**共用資源**] 底下的 [**變數**]，並將[**External_EnableClassicVMs**](#variables)變數設為**False**。 這會讓解決方案停止尋找傳統虛擬機器資源。

## <a name="scenarios"></a>案例

解決方案包含不同的案例。 這些案例為：

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>案例 1：依排程啟動/停止 VM

此案例是您首次部署解決方案時的預設設定。 例如，您可以將它設定為在您晚上離開公司時停止訂用帳戶上的所有虛擬機器，並在您早上回到辦公室時啟動它們。 當您在部署期間設定排程 **Scheduled-StartVM** 和 **Scheduled-StopVM** 時，它們會啟動和停止目標虛擬機器。 支援將此解決方案設定為僅停止 VM，請參閱[修改啟動和關機排程](#modify-the-startup-and-shutdown-schedules)以了解如何設定自訂的排程。

> [!NOTE]
> 時區是您設定排程表時間參數時所在的目前時區。 但它會以 UTC 格式儲存在 Azure 自動化中。 您不需要執行任何時區轉換，因為它會在部署期間處理。

您可藉由設定下列變數：**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 來控制有哪些虛擬機器包含於範圍中。

您可以將動作的目標設為某個訂用帳戶和資源群組，或設為特定的虛擬機器清單，但不可同時設定為兩者。

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. 設定 **External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 變數來指定目標 VM。
2. 啟用及更新 **Scheduled-StartVM** 和 **Scheduled-StopVM** 排程。
3. 執行 **ScheduledStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，然後將 WHATIF 參數設為 **True** 以預覽變更。

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 執行 **ScheduledStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，在 *VMList* 參數中新增以逗號分隔的 VM 清單，然後將 WHATIF 參數設為 **True**。 預覽變更。
1. 使用以逗號分隔的虛擬機器清單 (VM1、VM2、VM3) 設定 **External_ExcludeVMNames** 參數。
1. 此案例不會接受 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupnames** 變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

> [!NOTE]
> **Target ResourceGroup Names** 的值會儲存為 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 的值。 如需進一步的細微設定，您可以修改這些變數來以不同的資源群組為目標。 針對啟動動作請使用 **External_Start_ResourceGroupNames**，而針對停止動作請使用 **External_Stop_ResourceGroupNames**。 虛擬機器會自動新增至啟動和停止排程。

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>案例 2：使用標記依順序啟動/停止 VM

在多部虛擬機器上包含兩個或多個元件並支援分散式工作負載的環境中，支援元件的啟動和停止順序是非常重要的。 您可以執行下列步驟來完成此案例：

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. 針對於 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 變數中設為目標的虛擬機器，新增具有正整數值的 **sequencestart** 和 **sequencestop** 標記。 啟動和停止動作會依遞增順序執行。 若要了解如何標記虛擬機器，請參閱[在 Azure 中標記 Windows 虛擬機器](../virtual-machines/windows/tag.md)和[在 Azure 中標記 Linux 虛擬機器](../virtual-machines/linux/tag.md)。
1. 修改排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 以符合您所需的日期和時間，然後啟用排程。
1. 執行 **SequencedStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，然後將 WHATIF 參數設為 **True** 以預覽變更。
1. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 就緒後即可將參數設定為 **False** 並手動執行 Runbook，或是讓自動化排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 依照您指定的排程自動執行。

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 將使用正整數值的 **sequenceStart** 和 **sequenceStop** 標記，新增至您打算新增至 **VMList** 參數的 VM。
1. 執行 **SequencedStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，在 *VMList* 參數中新增以逗號分隔的虛擬機器清單，然後將 WHATIF 參數設為 **True**。 預覽變更。
1. 使用以逗號分隔的虛擬機器清單 (VM1、VM2、VM3) 設定 **External_ExcludeVMNames** 參數。
1. 此案例不會接受 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupnames** 變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。
1. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 就緒後即可將參數設定為 **False** 並手動執行 monitoring-and-diagnostics/monitoring-action-groupsrunbook，或是讓自動化排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 依照您指定的排程自動執行。

## <a name="solution-components"></a>方案元件

此解決方案包括預先設定的 runbook、排程，以及與 Azure 監視器記錄的整合，讓您可以量身打造虛擬機器的啟動和關閉，以符合您的商務需求。

### <a name="runbooks"></a>Runbook

下表列出透過此解決方案部署至您自動化帳戶的 Runbook。 請勿變更 Runbook 程式碼。 相反地，為新功能撰寫自己的 Runbook。

> [!IMPORTANT]
> 請勿直接執行任何有「子項目」附加至其名稱的 Runbook。

所有父代 Runbook 皆包含 _WhatIf_ 參數。 將 **WhatIf** 設為 _True_ 時，即可支援詳述在不使用 _WhatIf_ 參數執行的情況下，該 Runbook 會採取的確切行為，並驗證是否已將正確的虛擬機器作為目標。 _WhatIf_ 參數設為 **False** 時，Runbook 只會執行其定義的動作。

|Runbook | 參數 | 描述|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 從父系 Runbook 呼叫。 此 Runbook 會針對 AutoStop 案例以每個資源為基礎建立警示。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目標訂用帳戶或資源群組中的 VM 上建立或更新 Azure 警示規則。 <br> VMList：以逗號分隔的虛擬機器清單。 例如，vm1、vm2、vm3。<br> WhatIf 會驗證 Runbook 邏輯而不會執行。|
|AutoStop_Disable | 無 | 停用 AutoStop 警示和預設排程。|
|AutoStop_StopVM_Child | WebHookData | 從父系 Runbook 呼叫。 警示規則會呼叫此 Runbook 以停止虛擬機器。|
|Bootstrap_Main | 無 | 單次使用以設定啟動程序設定 (例如 webhookURI)，這些設定通常無法從 Azure Resource Manager 存取。 在部署成功之後會自動移除此 Runbook。|
|ScheduledStartStop_Child | VMName <br> 動作：啟動或停止 <br> resourceGroupName | 從父系 Runbook 呼叫。 針對排程的停止執行啟動或停止動作。|
|ScheduledStartStop_Parent | 動作：啟動或停止 <br>VMList <br> WhatIf：True 或 False | 此設定會影響訂用帳戶中的所有虛擬機器。 編輯 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames**，以便只在這些目標資源群組上執行。 您也可以更新 **External_ExcludeVMNames** 變數來排除特定的 VM。<br> VMList：以逗號分隔的虛擬機器清單。 例如，vm1、vm2、vm3。<br> WhatIf 會驗證 Runbook 邏輯而不會執行。|
|SequencedStartStop_Parent | 動作：啟動或停止 <br> WhatIf：True 或 False<br>VMList| 在您要序列啟動/停止活動的每部虛擬機器上建立名為 **SequenceStart** 和 **SequenceStop** 的標記。 這些標記名稱會區分大小寫。 標記值應為正整數 (1, 2, 3)，對應至您要啟動或停止的順序。 <br> VMList：以逗號分隔的虛擬機器清單。 例如，vm1、vm2、vm3。 <br> WhatIf 會驗證 Runbook 邏輯而不會執行。 <br> **注意**：虛擬機器必須位於定義於 Azure 自動化變數中 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 的資源群組內。 虛擬機器必須具有適當的標記以使動作生效。|

### <a name="variables"></a>變數

下表列出在您自動化帳戶中建立的變數。 僅修改前面加上 **External** 的變數。 修改前面加上 **Internal** 的變數會造成非預期的結果。

|變數 | 描述|
|---------|------------|
|External_AutoStop_Condition | 設定觸發警示之條件所需的條件運算子。 可接受的值為 **GreaterThan**、**GreaterThanOrEqual**、**LessThan** 和 **LessThanOrEqual**。|
|External_AutoStop_Description | 在 CPU 百分比超出閾值的情況下停止虛擬機器的警示。|
|External_AutoStop_MetricName | 要設定 Azure 警示規則的效能計量名稱。|
|External_AutoStop_Threshold | 適用於在變數 _External_AutoStop_MetricName_ 中指定之 Azure 警示規則的閾值。 百分比值的範圍為 1 至 100。|
|External_AutoStop_TimeAggregationOperator | 會套用至選取的視窗大小以評估條件的時間彙總運算子。 可接受的值為 **Average**、**Minimum**、**Maximum**、**Total** 和 **Last**。|
|External_AutoStop_TimeWindow | Azure 分析選取之計量以觸發警示的視窗大小。 此參數接受時間範圍格式的輸入。 可能的值為 5 分鐘到 6 小時。|
|External_EnableClassicVMs| 指定傳統虛擬機器是否為解決方案設定的目標。 預設值為 true。 對於 CSP 訂用帳戶，應該設為 False。 傳統 Vm 需要[傳統的執行身分帳戶](automation-create-standalone-account.md#classic-run-as-accounts)。|
|External_ExcludeVMNames | 輸入要排除的虛擬機器名稱，請使用不含空格的逗號來分隔名稱。 此上限為 140 個 VM。 如果您將超過140個 Vm 新增至此逗號分隔清單，則設定為 [已排除] 的 Vm 可能會不慎啟動或停止。|
|External_Start_ResourceGroupNames | 使用逗號分隔值指定一或多個作為啟動動作目標的資源群組。|
|External_Stop_ResourceGroupNames | 使用逗號分隔值指定一或多個作為停止動作目標的資源群組。|
|Internal_AutomationAccountName | 指定自動化帳戶的名稱。|
|Internal_AutoSnooze_WebhookUri | 指定針對 AutoStop 案例呼叫的 Webhook URI。|
|Internal_AzureSubscriptionId | 指定 Azure 訂用帳戶識別碼。|
|Internal_ResourceGroupName | 指定自動化帳戶資源群組名稱。|

在所有情況下，**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 變數皆為設定目標虛擬機器的必要項目 (除了為 **AutoStop_CreateAlert_Parent**、**SequencedStartStop_Parent** 和 **ScheduledStartStop_Parent** Runbook 提供以逗號分隔的虛擬機器清單以外)。 也就是說，您的虛擬機器必須位於目標資源群組中，才會發生啟動和停止動作。 此邏輯的運作方式類似 Azure 原則，其中您可以將訂用帳戶或資源群組設為目標，並讓新建立的 VM 繼承動作。 此方法可免去針對每部虛擬機器維護個別排程的必要，並可大規模地管理啟動和停止。

### <a name="schedules"></a>排程

下表列出在您的自動化帳戶中建立的各個預設排程。 您可以修改它們，或建立您自己的自訂排程。 根據預設，除了**Scheduled_StartVM**和**Scheduled_StopVM**以外，所有排程都會停用。

您不應啟用所有排程，因為這樣可能會產生重疊的排程動作。 最好能先判斷要執行哪些最佳化，並據以做出相對應的修改。 如需進一步說明，請參閱＜概觀＞一節中的範例案例。

|排程名稱 | 頻率 | 描述|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每 8 小時 | 每隔 8 小時會執行 AutoStop_CreateAlert_Parent Runbook，這會停止在 Azure 自動化變數中 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 中的虛擬機器基底值。 或者，您可以使用 VMList 參數指定以逗號分隔的虛擬機器清單。|
|Scheduled_StopVM | 使用者定義，每日 | 每天會在指定時間搭配 _Stop_ 參數執行 Scheduled_Parent Runbook。 自動停止符合資產變數所定義之規則的所有 Vm。 啟用相關排程（已**排程-StartVM**）。|
|Scheduled_StartVM | 使用者定義，每日 | 每天會在指定時間搭配 _Start_ 參數執行 Scheduled_Parent Runbook。 會自動啟動符合由適當變數所定義之規則的所有虛擬機器。 啟用相關排程（已**排程-scheduled-stopvm**）。|
|Sequenced-StopVM | 上午 1:00 (UTC)，每星期五 | 每星期五會在指定時間搭配參數 _Stop_ 執行 Sequenced_Parent Runbook。 順序（遞增）會停止所有具有適當變數所定義之**SequenceStop**標記的 vm。 如需標記值和資產變數的詳細資訊，請參閱 Runbook 一節。 啟用相關排程（已**排序-StartVM**）。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 每星期一會在指定時間搭配參數 _Start_ 執行 Sequenced_Parent Runbook。 會以循序方式 (遞減) 啟動具有由適當變數定義之 **SequenceStart** 標記的所有虛擬機器。 如需標記值和資產變數的詳細資訊，請參閱 Runbook 一節。 啟用相關排程 **Sequenced-StopVM**。|

## <a name="azure-monitor-logs-records"></a>Azure 監視器記錄檔記錄

自動化會在 Log Analytics 工作區中建立兩種類型的記錄：作業記錄和作業串流。

### <a name="job-logs"></a>作業記錄

|屬性 | 描述|
|----------|----------|
|呼叫者 |  起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。|
|類別 | 資料類型的分類。 對自動化來說，該值是 JobLogs。|
|CorrelationId | Runbook 作業之相互關聯識別碼的 GUID。|
|JobId | Runbook 作業之識別碼的 GUID。|
|operationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。|
|resourceId | 指定 Azure 中的資源類型。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
|ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。 對自動化來說，此值是 Azure 自動化。|
|ResourceType | 指定 Azure 中的資源類型。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|resultType | Runbook 作業的狀態。 可能的值包括：<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Succeeded|
|resultDescription | 說明 Runbook 作業的結果狀態。 可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed|
|RunbookName | 指定 Runbook 的名稱。|
|SourceSystem | 指定所提交資料的來源系統。 對自動化來說，該值是 OpsManager|
|StreamType | 指定事件的類型。 可能的值包括：<br>- Verbose<br>- Output<br>- Error (錯誤)<br>- Warning (警告)|
|SubscriptionId | 指定作業的訂用帳戶 ID。
|Time | Runbook 作業的執行日期和時間。|

### <a name="job-streams"></a>作業串流

|屬性 | 描述|
|----------|----------|
|呼叫者 |  起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。|
|類別 | 資料類型的分類。 對自動化來說，該值是 JobStreams。|
|JobId | Runbook 作業之識別碼的 GUID。|
|operationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。|
|ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
|resourceId | 指定 Azure 中的資源識別碼。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。 對自動化來說，此值是 Azure 自動化。|
|ResourceType | 指定 Azure 中的資源類型。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|resultType | Runbook 作業在產生事件時的結果。 可能的值為：<br>- InProgres|
|resultDescription | 包含來自 Runbook 的輸出串流。|
|RunbookName | Runbook 的名稱。|
|SourceSystem | 指定所提交資料的來源系統。 對自動化來說，該值是 OpsManager。|
|StreamType | 作業串流的類型。 可能的值包括：<br>- Progress (進度)<br>- Output<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose|
|Time | Runbook 作業的執行日期和時間。|

當您執行的記錄搜尋傳回 **JobLogs** 或 **JobStreams** 的類別記錄時，您可以選取 **JobLogs** 或 **JobStreams** 檢視，其中會顯示一組彙總搜尋所傳回更新的圖格。

## <a name="sample-log-searches"></a>記錄搜尋範例

下表提供此方案所收集之作業記錄的記錄檔搜尋範例。

|查詢 | 描述|
|----------|----------|
|尋找 ScheduledStartStop_Parent Runbook 已順利完成的作業 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|尋找 SequencedStartStop_Parent Runbook 已順利完成的作業 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>檢視解決方案

若要存取解決方案，請巡覽至自動化帳戶，在 [相關資源] 之下，選取 [工作區]。 在 [log analytics] 頁面上，選取 **[一般**] 底下的 [**解決方案**]。 在 [解決方案] 頁面上，從清單中選取 [Start-Stop-VM[工作區]] 解決方案。

選取該解決方案會顯示 [Start-Stop-VM[工作區]] 解決方案頁面。 在這裡您可以檢閱重要的詳細資料，如 [StartStopVM] 圖格。 如同在 Log Analytics 工作區中，此圖格會顯示解決方案中已啟動並已順利完成的 Runbook 作業計數和圖形表示。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

您可以從這裡按一下環圈圖格，執行進一步的作業記錄分析。 解決方案儀表板會顯示作業記錄和預先定義的記錄搜尋查詢。 切換至 log analytics advanced portal 以根據您的搜尋查詢進行搜尋。

## <a name="configure-email-notifications"></a>設定電子郵件通知

若要在部署解決方案之後變更電子郵件通知，請修改在部署期間建立的動作群組。  

> [!NOTE]
> Azure Government 雲端中的訂用帳戶不支援此解決方案的電子郵件功能。

在 Azure 入口網站中，巡覽至 [監視] -> [動作群組]。 選取標題為 **StartStop_VM_Notication** 的動作群組。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/azure-monitor.png)

在 [StartStop_VM_Notification] 頁面上，按一下 [詳細資料] 底下的 [編輯詳細資料]。 這樣會開啟 [電子郵件/SMS/推播/語音] 頁面。 請更新電子郵件地址，然後按一下 [確定] 以儲存變更。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/change-email.png)

或者，您也可以將其他動作新增至動作群組中；若要深入了解動作群組，請參閱[動作群組](../azure-monitor/platform/action-groups.md)

以下是當解決方案關閉虛擬機器時，所傳送的範例電子郵件。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>新增/排除 VM

此解決方案能夠新增 VM 成為解決方案的目標，或特別排除解決方案中的機器。

### <a name="add-a-vm"></a>新增 VM

有幾個選項可供您確保 VM 在執行時包含在啟動/停止解決方案。

* 解決方案的每個父[runbook](#runbooks)都有一個**VMList**參數。 針對您的情況排程適當的父系 runbook 時，您可以將以逗號分隔的 VM 名稱清單傳遞給此參數，並在解決方案執行時包含這些 Vm。

* 若要選取多個 VM，以包含您要啟動或停止之 VM 的資源群組名稱設定 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames**。 您也可以將此值設定為 `*`，讓解決方案針對訂用帳戶中的所有資源群組執行。

### <a name="exclude-a-vm"></a>排除 VM

若要從解決方案中排除 VM，您也可以將它新增至 **External_ExcludeVMNames** 變數。 此變數是要從啟動/停止解決方案排除的特定 Vm 清單（以逗號分隔）。 此清單的上限為 140 個 VM。 如果您將超過140個 Vm 新增至此逗號分隔清單，則設定為 [已排除] 的 Vm 可能會不慎啟動或停止。

## <a name="modify-the-startup-and-shutdown-schedules"></a>修改啟動和關機排程

在此解決方案中管理啟動和關機排程的步驟，與[在 Azure 自動化中排程 Runbook](automation-schedules.md) 中所概述的步驟相同。 需要個別的排程來啟動和停止 VM。

支援將解決方案設定為只在特定時間停止 VM。 在此案例中，您剛建立了**停止**排程，但未安排任何對應的**啟動**排程。 若要這樣做，您需要：

1. 請確定您已新增要在 **External_Stop_ResourceGroupNames** 變數中關閉之 VM 的資源群組。
2. 針對您需要關閉 VM 的時間建立您自己的排程。
3. 巡覽至 **ScheduledStartStop_Parent** Runbook，然後按一下 [排程]。 這可讓您選取上一個步驟中建立的排程。
4. 選取 [參數與回合設定] 並將 ACTION 參數設定為 "Stop"。
5. 按一下 [確定] 以儲存變更。

## <a name="update-the-solution"></a>更新解決方案

如果您已部署此解決方案的舊版，就必須先從帳戶中刪除它，才能部署更新的版本。 依照下列步驟[移除解決方案](#remove-the-solution)，然後依照上述步驟[部署解決方案](#deploy-the-solution)。

## <a name="remove-the-solution"></a>移除解決方案

如果您決定不再需要使用解決方案，您可以將它從自動化帳戶中刪除。 刪除解決方案只會移除 Runbook。 不會刪除新增解決方案時所建立的排程或變數。 如果您不搭配其他 Runbook 使用這些資產，則必須以手動方式加以刪除。

若要刪除解決方案，請執行下列步驟：

1. 從您的自動化帳戶的 [**相關資源**] 底下，選取 [**連結的工作區**]。
1. 選取 [**移至工作區**]。
1. 在 **[一般**] 底下，選取 [**解決方案**]。 
1. 在 [解決方案] 頁面上，選取 [Start-Stop-VM[工作區]] 解決方案。 在 [VMManagementSolution[工作區]] 頁面上，選取功能表中的 [刪除]。<br><br> ![刪除 VM 管理解決方案](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. 在 [刪除解決方案] 視窗中，確認您要刪除解決方案。
1. 在驗證資訊並刪除解決方案後，您可以在功能表的 [通知] 底下追蹤其進度。 移除解決方案的程序啟動之後，您會返回 [解決方案] 頁面。

此程序並不會刪除自動化帳戶和 Log Analytics 工作區。 如果不想保留 Log Analytics 工作區，則必須手動刪除它。 此作業可以從 Azure 入口網站完成：

1. 在 Azure 入口網站中，搜尋並選取 [ **Log Analytics 工作區**]。
1. 在 [ **Log Analytics 工作區**] 頁面上，選取工作區。
1. 在工作區 [設定] 頁面的功能表中選取 [刪除]。

如果您不想保留 Azure 自動化帳戶元件，您可以手動加以刪除。 如需解決方案所建立的 Runbook、變數和排程清單，請參閱[解決方案元件](#solution-components)。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解如何使用 Azure 監視器記錄來建立不同的搜尋查詢，並查看自動化作業記錄，請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
- 若要深入了解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱[追蹤 Runbook 作業](automation-runbook-execution.md)。
- 若要深入瞭解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料總覽](../azure-monitor/platform/collect-azure-metrics-logs.md)。
