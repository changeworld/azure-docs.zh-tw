---
title: 疑難排解 Azure 自動化更新管理
description: 瞭解如何在 Azure 自動化中疑難排解和解決更新管理解決方案的問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f936b62349a534e6193a3c628c66c49d1a58b681
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790825"
---
# <a name="troubleshoot-issues-with-the-update-management-solution"></a>針對更新管理解決方案的問題進行疑難排解

本文討論使用更新管理解決方案時可能會遇到的問題。 混合式 Runbook 背景工作代理程式的代理程式疑難排解員可判斷根本問題。 若要深入瞭解疑難排解員，請參閱針對[Windows update 代理程式問題進行疑難排解](update-agent-issues.md)和針對[Linux 更新代理程式問題進行疑難排解](update-agent-issues-linux.md)。 如需其他上線問題，請參閱針對[解決方案上架進行疑難排解](onboarding.md)。

>[!NOTE]
>如果您在虛擬機器（VM）上架解決方案時發現問題，請檢查本機電腦上 [**應用程式和服務記錄**檔] 下的**Operations Manager**記錄檔。 尋找事件識別碼為4502的事件，以及包含`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`的事件詳細資料。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>案例：您收到「無法啟用更新解決方案」錯誤

### <a name="issue"></a>問題

當您嘗試在您的自動化帳戶中啟用更新管理解決方案時，您會收到下列錯誤：

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* Log Analytics 代理程式的網路防火牆需求可能未正確設定。 這種情況可能會導致代理程式在解析 DNS Url 時失敗。

* 解決方案目標設定不正確，且電腦未如預期般接收更新。

* 您可能也會注意到，機器顯示的狀態`Non-compliant`為 [**符合規範**]。 同時， **agent 電腦分析**會將代理程式報告為`Disconnected`。

### <a name="resolution"></a>解決方案

* 執行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解員，視作業系統而定。

* 請移至 [[網路](../automation-hybrid-runbook-worker.md#network-planning)設定]，以瞭解必須允許哪些位址和埠，更新管理才能正常執行。  

* 請移至[網路](../../azure-monitor/platform/log-analytics-agent.md#network-requirements)設定，以瞭解 Log Analytics 代理程式必須允許哪些位址和埠才能正常執行。

* 檢查範圍設定問題。 [範圍](../automation-onboard-solutions-from-automation-account.md#scope-configuration)設定會決定哪些機器會針對解決方案進行設定。 如果您的電腦顯示在您的工作區中，但不在 * * 更新管理入口網站中，您必須將範圍設定設為以電腦為目標。 若要瞭解範圍設定，請參閱在[工作區中將電腦上架](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 遵循[移除 Windows 混合式 runbook 背景工作](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker)角色或[移除 Linux 混合式 Runbook 背景工作角色](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)中的步驟來移除背景工作設定。 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>案例：已取代的更新在更新管理中指出遺失

### <a name="issue"></a>問題

即使是已被取代的自動化帳戶，舊的更新還是會出現遺失。 已取代的更新是您不需要安裝的更新，因為後續的更新會修正相同的弱點。 更新管理會忽略被取代的更新，使其不適用取代更新。 如需相關問題的詳細資訊，請參閱[更新已被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="cause"></a>原因

已取代的更新未正確地表示為已拒絕，因此可視為不適用。

### <a name="resolution"></a>解決方案

當已取代的更新變成100% 不適用時，您應該將該更新的核准狀態`Declined`變更為。 若要變更所有更新的核准狀態：

1. 在自動化帳戶中，選取 [**更新管理**] 以查看機器狀態。 請參閱[查看更新評](../manage-update-multi.md#view-an-update-assessment)量。

2. 檢查已取代的更新，確定其為100% 不適用。 

3. 除非您有關于更新的問題，否則請將更新標示為已拒絕。 

4. 選取 [**電腦**]，然後在 [**相容性**] 欄中強制重新掃描合規性。 請參閱[管理多部電腦的更新](../manage-update-multi.md)。

5. 針對其他已取代的更新重複上述步驟。

6. 執行 [清除嚮導]，從拒絕的更新中刪除檔案。 

7. 若為 Windows Server Update Services （WSUS），請手動清除所有已取代的更新，以重新整理基礎結構。

8. 定期重複此程式來更正顯示問題，並將更新管理所使用的磁碟空間量降到最低。

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>案例：電腦不會顯示在入口網站的 [更新管理

### <a name="issue"></a>問題

您的電腦具有下列徵兆：

* 您的電腦`Not configured`會從 VM 的 [更新管理] 視圖中顯示。

* 您的 Azure 自動化帳戶的更新管理視圖遺漏您的電腦。

* 您有顯示為`Not assessed` [**合規性**] 的電腦。 不過，您會看到混合式 Runbook 背景工作角色 Azure 監視器記錄中的心跳資料，但不會更新管理。

### <a name="cause"></a>原因

此問題可能是因本機設定問題或設定不正確的範圍而造成。 可能的特定原因包括：

* 您可能必須重新註冊並重新安裝混合式 Runbook 背景工作角色。

* 您可能已在工作區中定義已達到的配額，因而導致無法進一步儲存資料。

### <a name="resolution"></a>解決方案

1. 執行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解員，視作業系統而定。

2. 請確定您的電腦已回報至正確的工作區。 如需如何驗證這方面的指引，請參閱[確認代理程式與 Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)的連線能力。 也請確定此工作區已連結至您的 Azure 自動化帳戶。 若要確認，請移至您的自動化帳戶，然後選取 [**相關資源**] 底下的 [**連結工作區**]

3. 請確定電腦出現在與您的自動化帳戶連結的 Log Analytics 工作區中。 在 Log Analytics 工作區中執行下列查詢。

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. 如果您在查詢結果中看不到您的電腦，它最近未簽入。 可能是本機設定問題，您應該[重新安裝代理程式](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。 

5. 如果您的電腦顯示在查詢結果中，請檢查是否有範圍設定問題。 [範圍](../automation-onboard-solutions-from-automation-account.md#scope-configuration)設定可決定要為解決方案設定的機器。 

6. 如果您的電腦顯示在您的工作區中，但不在更新管理中，您必須將範圍設定設定為以電腦為目標。 若要瞭解如何執行這項操作，請參閱在[工作區中將電腦上架](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

7. 在您的工作區中，執行此查詢。

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. 如果您得到`Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`結果，則表示您的工作區中定義的配額已達，而已停止儲存資料。 在您的工作區中，移至 [**使用量和估計成本**] 底下的 [**資料量管理**]，然後變更或移除配額。

9. 如果您的問題仍然無法解決，請遵循[部署 Windows 混合式 Runbook 背景工作角色](../automation-windows-hrw-install.md)中的步驟，重新安裝適用于 Windows 的混合式背景工作角色。 針對 Linux，請遵循[部署 Linux 混合式 Runbook 背景工作角色](../automation-linux-hrw-install.md)中的步驟。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>案例：無法註冊訂用帳戶的自動化資源提供者

### <a name="issue"></a>問題

當您在自動化帳戶中使用解決方案時，會發生下列錯誤：

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>原因

自動化資源提供者未在訂用帳戶中註冊。

### <a name="resolution"></a>解決方案

若要註冊自動化資源提供者，請遵循 Azure 入口網站中的下列步驟。

1. 在入口網站底部的 [Azure 服務] 清單中，選取 [**所有服務**]，然後選取 [一般] 服務群組**中的 [訂用帳戶**]。

2. 選取您的訂用帳戶。

3. 在 [**設定**] 下，選取 [**資源提供者**]。

4. 從資源提供者清單中，確認已註冊 Microsoft Automation 資源提供者。

5. 如果未列出，請遵循[解決資源提供者註冊錯誤](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步驟來註冊 Microsoft Automation 提供者。

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>案例：具有動態排程的排程更新錯過一些機器

### <a name="issue"></a>問題

更新預覽中包含的機器不會全部出現在排程執行期間修補的機器清單中。

### <a name="cause"></a>原因

此問題可能有下列其中一種原因：

* 未針對已註冊的自動化資源提供者設定動態查詢範圍中定義的訂閱。

* 執行排程時，電腦無法使用或沒有適當的標記。

### <a name="resolution"></a>解決方案

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>未針對已註冊的自動化資源提供者設定訂閱

如果您的訂用帳戶未設定為自動化資源提供者，您就無法查詢或提取該訂用帳戶中電腦的資訊。 使用下列步驟來驗證訂用帳戶的註冊。

1. 在 [ [Azure 入口網站](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)中，存取 [Azure 服務] 清單。

2. 選取 [**所有服務**]，然後選取 [一般] 服務群組**中的 [訂用帳戶**]。 

3. 尋找您的部署範圍中定義的訂用帳戶。

4. 在 [**設定**] 下，選擇 [**資源提供者**]。

5. 確認已註冊 Microsoft Automation 資源提供者。

6. 如果未列出，請遵循[解決資源提供者註冊錯誤](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步驟來註冊 Microsoft Automation 提供者。

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>執行排程時無法使用或未正確標記的電腦

如果您的訂用帳戶已設定為自動化資源提供者，但在指定的[動態群組](../automation-update-management-groups.md)中執行更新排程時遺失某些機器，請使用下列程式。

1. 在 [Azure 入口網站中，開啟自動化帳戶，然後選取 [**更新管理**]。

2. 請檢查[更新管理歷程記錄](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment)，以判斷執行更新部署的確切時間。 

3. 對於您懷疑已被更新管理遺漏的電腦，請使用 Azure Resource Graph （ARG）來[尋找電腦變更](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details)。 

4. 在執行更新部署之前，搜尋一段相當長的時間，例如一天的變更。

5. 在這段期間，檢查電腦的任何系統性變更（例如刪除或更新變更）的搜尋結果。 這些變更可以改變電腦狀態或標籤，以便在部署更新時，不會在機器清單中選取電腦。

6. 視需要調整機器和資源設定，以修正機器狀態或標記問題。

7. 重新執行更新排程，以確保具有指定動態群組的部署包含所有電腦。

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>案例：動態群組的預期機器不會顯示為預覽狀態

### <a name="issue"></a>問題

動態群組所選範圍的虛擬機器不會顯示在 [Azure 入口網站預覽] 清單中。 這份清單是由所選範圍的 ARG 查詢所抓取的所有電腦所組成。 系統會針對已安裝混合式 Runbook 背景工作角色，且您有存取權限的機器篩選範圍。 

### <a name="cause"></a>原因
 
此問題的可能原因如下：

* 您在選取的範圍上沒有正確的存取權。
* ARG 查詢不會取得預期的機器。
* 電腦上未安裝混合式 Runbook 背景工作角色。

### <a name="resolution"></a>解決方案 

#### <a name="incorrect-access-on-selected-scopes"></a>選取範圍的存取不正確

Azure 入口網站只會顯示您在給定範圍中具有寫入權限的電腦。 如果您沒有範圍的正確存取權，請參閱[教學課程：使用 RBAC 和 Azure 入口網站授與使用者對 Azure 資源的存取權](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG 查詢不會傳回預期的機器

請遵循下列步驟來找出您的查詢是否正常運作。

1. 在 Azure 入口網站的 Resource Graph explorer] 分頁中，執行如下所示的 ARG 查詢格式。 當您在更新管理中建立動態群組時，此查詢會模擬您選取的篩選。 請參閱搭配[使用動態群組與更新管理](https://docs.microsoft.com/azure/automation/automation-update-management-groups)。 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   範例如下：

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. 檢查您要尋找的電腦是否列在查詢結果中。 

3. 如果未列出電腦，在動態群組中選取的篩選可能會有問題。 視需要調整群組設定。

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>電腦上未安裝混合式 Runbook 背景工作角色

電腦會出現在 ARG 查詢結果中，但仍未顯示在動態群組預覽中。 在此情況下，機器可能不會指定為混合式背景工作角色，因此無法執行 Azure 自動化並更新管理作業。 若要確保您預期看到的機器已設定為混合式 Runbook 背景工作角色：

1. 在 Azure 入口網站中，移至未正確顯示之電腦的自動化帳戶。

2. 選取 [程式**自動化**] 下的 [混合式背景**工作角色群組**]

3. 選取 [**系統混合式背景工作角色群組**] 索引標籤。

4. 驗證該電腦的混合式背景工作角色是否存在。

5. 如果電腦未設定為混合式背景工作角色，請在[使用混合式 Runbook 背景工作角色將資料中心或雲端中的資源自動化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)時，使用指示進行調整。

6. 將電腦加入混合式 Runbook 背景工作角色群組。

7. 針對所有尚未顯示在預覽中的電腦，重複上述步驟。

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>案例：已啟用更新管理解決方案的元件，而 VM 會繼續顯示為已設定

### <a name="issue"></a>問題

上架後 15 分鐘，您會繼續在虛擬機器上看見下列訊息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* 正在封鎖與自動化帳戶的通訊。

* 有一部重複的電腦名稱稱具有不同的來源電腦識別碼。 當具有特定電腦名稱稱的 VM 是在不同的資源群組中建立，並回報給訂用帳戶中的相同物流代理程式工作區時，就會發生此情況。

* 所要上架的 VM 映射可能來自于已安裝適用于 Windows 的 Log Analytics 代理程式之系統準備（sysprep）未準備好的已複製電腦。

### <a name="resolution"></a>解決方案

若要協助判斷 VM 的確切問題，請在與您的自動化帳戶連結的 Log Analytics 工作區中執行下列查詢。

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>已封鎖自動化帳戶的通訊

請前往[網路規劃](../automation-update-management.md#ports)，瞭解必須允許哪些位址和埠，更新管理才能正常執行。

#### <a name="duplicate-computer-name"></a>重複的電腦名稱稱

重新命名您的 Vm，以確保其環境中的唯一名稱。

#### <a name="onboarded-image-from-cloned-machine"></a>從複製的電腦上架映射

如果您使用複製的映射，則不同的電腦名稱稱會有相同的來源電腦識別碼。 在此案例中：

1. 在您的 Log Analytics 工作區中，從已儲存的搜尋`MicrosoftDefaultScopeConfig-Updates`範圍設定中移除 VM （若有顯示）。 您可以在工作區中的 [一般]  底下找到儲存的搜尋。

2. 執行下列 Cmdlet。

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. 執行`Restart-Service HealthService`以重新開機健全狀況服務。 這項作業會重新建立金鑰，並產生新的 UUID。

4. 如果此方法無法運作，請先在映射上執行 Sysprep，然後再安裝 MMA。

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>案例：當您為另一個 Azure 租使用者中的電腦建立更新部署時，您會收到連結的訂用帳戶錯誤

### <a name="issue"></a>問題

當您嘗試在另一個 Azure 租使用者中建立電腦的更新部署時，您會遇到下列錯誤：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

當您在更新部署中所包含的另一個租使用者中建立具有 Azure Vm 的更新部署時，就會發生此錯誤。

### <a name="resolution"></a>解決方案

使用下列因應措施可讓這些專案排程。 您可以使用 [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) Cmdlet 搭配 `ForUpdateConfiguration` 參數來建立排程。 然後，使用[AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) Cmdlet，並將另一個租使用者中的機器傳遞給`NonAzureComputer`參數。 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>案例：無法解釋的重新開機

### <a name="issue"></a>問題

即使您已將 [**重新開機控制**] 選項設定為 [**永遠不重新開機**]，電腦仍會在安裝更新之後重新開機。

### <a name="cause"></a>原因

Windows Update 可以由數個登錄機碼修改，其中任何一個都可以修改重新開機行為。

### <a name="resolution"></a>解決方案

藉[由編輯](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)登錄和[用來管理重新開機](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的登錄機碼來檢查列于 [設定自動更新] 底下的登錄機碼，以確保您的電腦已正確設定。

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>案例：電腦在更新部署中顯示「無法啟動」

### <a name="issue"></a>問題

電腦會顯示`Failed to start`狀態。 當您查看機器的特定詳細資料時，您會看到下列錯誤：

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

此錯誤可能會因為下列其中一個原因而發生：

* 機器不再存在。
* 機器已關閉且無法連線。
* 電腦發生網路連線問題，因此無法連線到電腦上的混合式背景工作角色。
* 已變更來源電腦識別碼的 MMA 更新。
* 如果您達到自動化帳戶中2000個並行作業的限制，則您的更新執行已節流。 系統會將每個部署視為一項作業，而更新部署中的每部電腦會計算為一項作業。 目前在您的自動化帳戶中執行的任何其他自動化作業或更新部署都會計入並行作業限制。

### <a name="resolution"></a>解決方案

適用時，請使用[動態群組](../automation-update-management-groups.md)進行更新部署。 此外，您可以採取下列步驟。

1. 確認電腦仍然存在且可連線。 
2. 如果電腦不存在，請編輯您的部署，並移除電腦。
3. 請參閱[網路規劃](../automation-update-management.md#ports)一節，以取得更新管理所需的埠和地址清單，然後確認您的電腦符合這些需求。
4. 使用「混合式 Runbook 背景工作代理程式」疑難排解員驗證與混合式 Runbook 背景工作角色的連線。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。
5. 在 Log Analytics 中執行下列查詢，以在您的環境中尋找來源電腦識別碼已變更的電腦。 尋找具有相同`Computer`值但值不同`SourceComputerId`的電腦。

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. 在您找到受影響的機器之後，請編輯以這些機器為目標的更新部署，然後移除並`SourceComputerId`重新新增它們，以反映正確的值。

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>案例：已安裝不含部署的更新

### <a name="issue"></a>問題

當您在更新管理中註冊 Windows 機器時，您會看到已安裝但沒有部署的更新。

### <a name="cause"></a>原因

在 Windows 上，更新會在可用時立即安裝。 如果您未排程將更新部署到電腦上，這種行為可能會造成混淆。

### <a name="resolution"></a>解決方案

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`登錄機碼預設為4： `auto download and install`的設定。

針對更新管理用戶端，我們建議您將此機碼`auto download but do not auto install`設為3：。

如需詳細資訊，請參閱設定[自動更新](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>案例：電腦已經註冊至不同的帳戶

### <a name="issue"></a>問題

您收到下列錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

電腦已上架到另一個工作區以進行更新管理。

### <a name="resolution"></a>解決方案

1. 依照 [更新管理] 底下的 [[機器不會顯示在入口網站中](#nologs)] 底下的步驟，確認機器已回報至正確的工作區。
2. 藉由[刪除混合式 runbook 群組](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)來清除電腦上的成品，然後再試一次。

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>案例：電腦無法與服務通訊

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>原因

Proxy、閘道或防火牆可能封鎖網路通訊。 

### <a name="resolution"></a>解決方案

請檢查您的網路功能，並確定允許適當的埠和位址。 如需更新管理和混合式 Runbook 背景工作角色所需的埠和地址清單，請參閱[網路需求](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>案例：無法建立自我簽署憑證

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合式 Runbook 背景工作角色無法產生自我簽署憑證。

### <a name="resolution"></a>解決方案

請確認系統帳戶具有**C:\ProgramData\Microsoft\Crypto\RSA**資料夾的讀取權限，然後再試一次。

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>案例：排程更新失敗，發生 MaintenanceWindowExceeded 錯誤

### <a name="issue"></a>問題

更新的預設維護視窗是120分鐘。 您可以將維護期間增加到最多6小時或360分鐘。

### <a name="resolution"></a>解決方案

編輯任何失敗的排程更新部署，並增加維護時間範圍。

如需維護時段的詳細資訊，請參閱[安裝更新](../automation-tutorial-update-management.md#schedule-an-update-deployment)。

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>案例：電腦顯示為「未評估」，並顯示 HRESULT 例外狀況

### <a name="issue"></a>問題

* 您的電腦會顯示為`Not assessed` [**符合規範**]，而且您會在下方看到例外狀況訊息。
* 您會在入口網站中看到 HRESULT 錯誤碼。

### <a name="cause"></a>原因

更新代理程式（Windows 上的 Windows Update 代理程式、Linux 發行版本的套件管理員）未正確設定。 更新管理依賴電腦的更新代理程式來提供所需的更新、修補程式的狀態，以及已部署修補程式的結果。 若沒有此資訊，更新管理無法正確地報告所需或已安裝的修補程式。

### <a name="resolution"></a>解決方案

嘗試在本機電腦上執行更新。 如果此操作失敗，通常表示有「更新代理程式」設定錯誤。

此問題通常是因為網路設定和防火牆問題所造成。 請使用下列檢查來更正問題。

* 針對 Linux，請查看適當的檔，確定您可以連線到套件存放庫的網路端點。

* 針對 Windows，請檢查[[更新不會從內部網路端點下載（WSUS/SCCM）](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)] 中所列的代理程式設定。

  * 如果電腦已設定為 Windows Update，請確定您可以連線[到與 HTTP/proxy 相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中所述的端點。
  * 如果電腦設定為 Windows Server Update Services （WSUS），請確定您可以連線到 WUServer 登錄機[碼](/windows/deployment/update/waas-wu-settings)所設定的 WSUS 伺服器。

如果您看到 HRESULT，請按兩下以紅色顯示的例外狀況，以查看整個例外狀況訊息。 請參閱下表中的可能解決方案或建議的動作。

|例外狀況  |解決方式或動作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在[Windows update 錯誤碼清單](https://support.microsoft.com/help/938205/windows-update-error-code-list)中搜尋相關的錯誤碼，以尋找有關例外狀況原因的其他詳細資料。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 這表示網路連線問題。 請確定您的電腦具有更新管理的網路連線能力。 如需必要的埠和地址清單，請參閱[網路規劃](../automation-update-management.md#ports)一節。        |
|`0x8024001E`| 更新作業未完成，因為服務或系統已關閉。|
|`0x8024002E`| Windows Update 服務已停用。|
|`0x8024402C`     | 如果您使用的是 WSUS 伺服器，請確定登錄機碼底下`WUServer`的`WUStatusServer`登錄值`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` ，並指定正確的 wsus 伺服器。        |
|`0x80072EE2`|網路連線問題或與已設定的 WSUS 伺服器交談時發生問題。 檢查 WSUS 設定，並確定可從用戶端存取該服務。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 請確定 Windows Update 服務（wuauserv）正在執行且未停用。        |
|`0x80070005`| 拒絕存取的錯誤可能是由下列任何一項所造成：<br> 受感染的電腦<br> 未正確設定 Windows Update 設定<br> %WinDir%\SoftwareDistribution 資料夾的檔案許可權錯誤<br> 系統磁片磁碟機上的磁碟空間不足（C：）。
|任何其他一般例外狀況     | 在網際網路上執行搜尋以找出可能的解決方案，並與您當地的 IT 支援人員合作。         |

檢查 **%Windir%\Windowsupdate.log**檔案也可協助您判斷可能的原因。 如需如何讀取記錄檔的詳細資訊，請參閱[如何讀取 windowsupdate.log。](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)

您也可以下載並執行[Windows Update 疑難排解](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)員，以檢查電腦上 Windows Update 是否有任何問題。

> [!NOTE]
> [Windows Update 疑難排解](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)員檔會指出它是用於 windows 用戶端，但它也適用于 windows Server。

## <a name="scenario-update-run-returns-failed-status-linux"></a>案例：更新執行會傳回失敗狀態（Linux）

### <a name="issue"></a>問題

更新執行開始，但在執行期間遇到錯誤。

### <a name="cause"></a>原因

可能的原因：

* 套件管理員狀況不良。
* 更新代理程式（適用于 Windows 的 WUA，Linux 的散發版本特定套件管理員）設定錯誤。
* 特定套件會干擾以雲端為基礎的修補。
* 無法連線到電腦。
* 更新有無法解析的相依性。

### <a name="resolution"></a>解決方案

如果成功啟動後，在更新執行期間發生失敗，請檢查執行中受影響機器的[作業輸出](../manage-update-multi.md#view-results-of-an-update-deployment)。 您可能會發現來自您電腦的特定錯誤訊息，您可以調查並採取行動。 「更新管理」必須在套件管理員狀況良好的情況下，才能進行成功的更新部署。

如果在作業失敗之前立即看到特定的修補程式、套件或更新，您可以嘗試從下一個更新部署中[排除](../automation-tutorial-update-management.md#schedule-an-update-deployment)這些專案。 若要從 Windows Update 收集記錄資訊，請參閱[Windows Update 記錄](/windows/deployment/update/windows-update-logs)檔。

如果您無法解決修補問題，請在下一次更新部署開始之前，複製 **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log**檔案，並將它保留供疑難排解之用。

## <a name="patches-arent-installed"></a>未安裝修補程式

### <a name="machines-dont-install-updates"></a>機器未安裝更新

請嘗試直接在機器上執行更新。 如果電腦無法套用更新，請參閱[疑難排解指南中的潛在錯誤清單](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)。

如果更新在本機執行，請遵循[從更新管理移除 VM 中](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)的指引，在電腦上移除並重新安裝代理程式。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道有可用的更新，但它們在我的機器上並未顯示為可用

這通常會發生在電腦設定為從 WSUS 或 Microsoft Endpoint Configuration Manager 取得更新時，但 WSUS 和 Configuration Manager 尚未核准更新。

您可以藉由[編輯這篇文章的 registry 一節](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)，檢查是否已針對 WSUS 和`UseWUServer` SCCM 設定機器，藉由在 [設定自動更新

如果未在 WSUS 中核准更新，就不會安裝它們。 您可以藉由執行下列查詢，在 Log Analytics 中檢查未核准的更新。

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新會顯示為已安裝，但我在機器上找不到這些更新

更新常會被其他更新取代。 如需詳細資訊，請參閱 Windows Update 疑難排解指南中的[更新已被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>依分類在 Linux 上安裝更新

依分類 (「重大與安全性更新」) 將更新部署至 Linux 時有重要事項須留意，尤其是針對 CentOS。 這些限制記載于[更新管理總覽頁面](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 一直遺失

KB2267602 是 [Windows Defender 定義更新](https://www.microsoft.com/wdsi/definitions)。 它會每日更新。

## <a name="next-steps"></a>後續步驟

如果您看不到問題或無法解決問題，請嘗試下列其中一個通道以取得其他支援。

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶聯繫，以改善客戶體驗。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。
