---
title: 使用 Azure 更新管理排除錯誤
description: 瞭解如何使用 Azure 中的更新管理解決方案來排除和解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c9ff05591c98fda8be39e32f26da484f56e0831b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984618"
---
# <a name="troubleshooting-issues-with-update-management"></a>針對更新管理問題進行疑難排解

本文討論在使用更新管理時可能會遇到的問題的解決方案。

混合輔助角色代理有一個代理疑難解答,用於確定根本問題。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。 對於所有其他問題,請使用以下故障排除指南。

如果在虛擬機 (VM) 上加入解決方案時發現問題,請查看本地電腦上的 **「應用程式和服務日誌**」下**的操作管理員**日誌。 尋找事件 ID 4502 的事件`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`和包含的事件詳細資訊。

以下部分重點介紹特定錯誤消息和每個錯誤消息的可能解決方法。 有關其他載入問題,請參閱[載入的疑難排解解決方案](onboarding.md)。

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>方案:您會收到錯誤"無法啟用更新解決方案"

### <a name="issue"></a>問題

當您試著在自動化帳戶中啟用更新管理解決方案時,您會收到以下錯誤:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* 日誌分析代理的網路防火牆要求可能未正確配置。 此情況可能導致代理在解決 DNS URL 時失敗。

* 解決方案定位配置錯誤,計算機未按預期接收更新。

* 您可能還注意到,計算機顯示的`Non-compliant`「**符合性」** 狀態。 同時,**代理桌面分析**將代理`Disconnected`報告為 。

### <a name="resolution"></a>解決方案

* 運行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解,具體取決於作業系統。

* 轉到[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)以瞭解必須允許哪些位址和埠才能更新管理工作。  

* 轉到[網路規劃](../../azure-monitor/platform/log-analytics-agent.md#network-requirements)以瞭解日誌分析代理必須允許哪些位址和埠工作。

* 檢查範圍配置問題。 [範圍配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)確定為解決方案配置了哪些計算機。 如果電腦顯示在工作區中,但未顯示在 [更新管理門戶』中,則需要設置範圍配置以定位計算機。 要瞭解作用網域設定,請參閱[工作區中的板載入電腦](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 按照[刪除混合 Runbook 輔助角色](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)的步驟刪除輔助角色配置。 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>方案:在更新管理中指示缺少的已取代更新

### <a name="issue"></a>問題

對於自動化帳戶,舊更新顯示為丟失,即使它們已被取代。 取代的更新是您不必安裝的更新,因為稍後的更新可以更正相同的漏洞。 更新管理忽略被取代的更新,使其不適用於取代的更新。 有關相關問題的資訊,請參閱[更新被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="cause"></a>原因

被取代的更新未正確指示為已拒絕,因此可以認為它們不適用。

### <a name="resolution"></a>解決方案

當被取代的更新變為 100% 不適用時,應將該更新的審批狀態`Declined`更改為 。 要更改所有更新的審批狀態,

1. 在"自動化"帳戶中,選擇 **"更新管理**"以查看計算機狀態。 請參考[查詢更新 。](../manage-update-multi.md#view-an-update-assessment)

2. 檢查被取代的更新,以確保它 100% 不適用。 

3. 除非對更新有疑問,否則將更新標記為已拒絕。 

4. 選擇 **「電腦**」,並在 **「合規性」** 列中強制重新掃描符合性。 請參考[管理多台電腦的更新](../manage-update-multi.md)。

5. 對其他被取代的更新重複上述步驟。

6. 運行清理嚮導以從已拒絕的更新中刪除檔。 

7. 對於 Windows 伺服器更新服務 (WSUS),手動清理所有被取代的更新以刷新基礎結構。

8. 定期重複此過程以更正顯示問題並最小化用於更新管理的磁碟空間量。

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>配置:電腦不會顯示在更新管理下的門戶中

### <a name="issue"></a>問題

您的機器有以下症狀:

* 您的電腦從`Not configured`VM 的更新管理檢視中顯示。

* Azure 自動化帳戶的更新管理檢視中缺少計算機。

* 您有顯示在**合規**下的`Not assessed`電腦 。 但是,在 Azure 監視器日誌中看到混合 Runbook 輔助角色中的檢測信號數據,但不適用於更新管理。

### <a name="cause"></a>原因

此問題可能由本地配置問題或配置不正確的範圍配置引起。 可能的具體原因是:

* 您可能需要重新註冊並重新安裝混合 Runbook 輔助角色。

* 您可能已在工作區中定義了已達到的配額,這阻止了進一步的數據存儲。

### <a name="resolution"></a>解決方案

1. 運行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解,具體取決於作業系統。

2. 確保計算機報告到正確的工作區。 有關如何驗證此方面的指導,請參閱[驗證代理連線到日誌分析](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)。 還要確保此工作區連結到 Azure 自動化帳戶。 要確認,請轉到您的自動化帳戶,並在 **「相關資源**」下選擇 **「連結工作區**」。

3. 確保電腦顯示在連結到您的自動化帳戶的日誌分析工作區中。 在日誌分析工作區中運行以下查詢。

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. 如果在查詢結果中看不到計算機,則計算機最近未簽入。 可能存在本地端設定問題,[您應該重新安裝代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)程式 。 

5. 如果電腦顯示在查詢結果中,請檢查範圍配置問題。 [範圍配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)確定為解決方案配置了哪些計算機。 

6. 如果電腦顯示在工作區中,但未顯示在更新管理中,則必須將範圍配置配置為以計算機為目標。 要瞭解如何執行此操作,請參閱[工作區中的板載入電腦](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

7. 在工作區中,運行此查詢。

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. 如果得到結果`Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`,則已達到工作區上定義的配額,從而阻止保存數據。 在工作區中,轉到 **"使用方式"和"估計成本**"下**的數據卷管理**,然後更改或刪除配額。

9. 如果問題仍未解決,請按照[「部署 Windows 混合 Runbook 輔助角色」](../automation-windows-hrw-install.md)中的步驟重新安裝 Windows 的混合輔助角色。 對於 Linux,請按照[部署 Linux 混合 Runbook 輔助手冊 中](../automation-linux-hrw-install.md)的步驟操作。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>專案:無法註冊訂閱的自動化資源提供者

### <a name="issue"></a>問題

當您使用自動化帳戶中的解決方案時,會發生以下錯誤:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>原因

未在訂閱中註冊自動化資源提供程式。

### <a name="resolution"></a>解決方案

要註冊自動化資源提供程式,請在 Azure 門戶中執行以下步驟。

1. 在門戶底部的 Azure 服務清單中,選擇 **「所有服務**」,然後在「常規服務」群組中選擇 **「訂閱**」。

2. 選取您的訂用帳戶。

3. 在 **「設定」** 下,選擇 **「資源提供程式**」 。。

4. 從資源提供程式清單中驗證 Microsoft.自動化資源提供程式是否註冊。

5. 如果未列出,請按照[「解決資源提供程序註冊錯誤」](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步驟註冊 Microsoft.自動化提供程式。

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>方案:計劃更新,動態計劃未命中某些電腦

### <a name="issue"></a>問題

更新預覽中包含的電腦並不都顯示在計劃運行期間修補的計算機清單中。

### <a name="cause"></a>原因

此問題可能有以下原因之一:

* 動態查詢中定義的訂閱未為註冊的自動化資源提供程式配置。

* 執行計劃時,計算機不可用或沒有適當的標記。

### <a name="resolution"></a>解決方案

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>未為已註冊的自動化資源提供程式設定的訂閱

如果您的訂閱未為自動化資源提供程式配置,則無法查詢或獲取該訂閱中的計算機上的資訊。 使用以下步驟驗證訂閱的註冊。

1. 在[Azure 門戶](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)中,訪問 Azure 服務清單。

2. 選擇 **"所有服務**",然後在"常規服務"組中選擇 **"訂閱**"。 

3. 查找部署範圍中定義的訂閱。

4. 在 **「設定」** 下,選擇 **「資源提供程式**」 。。

5. 驗證Microsoft.自動化資源提供程式是否已註冊。

6. 如果未列出,請按照[「解決資源提供程序註冊錯誤」](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步驟註冊 Microsoft.自動化提供程式。

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>執行計畫時電腦無法使用或未正確標記

如果您的訂閱已配置為自動化資源提供程式,但使用指定的[動態組](../automation-update-management-groups.md)運行更新計畫時錯過了某些計算機,請使用以下過程。

1. 在 Azure 門戶中,打開自動化帳戶並選擇 **「更新管理**」。

2. 檢查[更新管理歷史記錄](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment)以確定運行更新部署的確切時間。 

3. 對於您懷疑更新管理遺漏的電腦,請使用 Azure 資源圖 (ARG)[尋找電腦變更](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details)。 

4. 在運行更新部署之前,搜索相當長一段時間內的更改,例如一天。

5. 檢查搜尋結果中是否有任何系統更改(如刪除或更新更改)到此時間段內的電腦。 這些更改可以更改計算機狀態或標記,以便在部署更新時不會在電腦清單中選擇計算機。

6. 根據需要調整計算機和資源設置,以更正計算機狀態或標記問題。

7. 重新執行更新計畫,以確保具有指定動態組的部署包括所有計算機。

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>方案:動態組的預覽中未顯示預期電腦

### <a name="issue"></a>問題

動態組選定作用域的虛擬機未顯示在 Azure 門戶預覽清單中。 此清單由所選作用域的 ARG 查詢檢索的所有電腦組成。 作用域將篩選為安裝了混合 Runbook 工作台且您具有訪問許可權的計算機。 

### <a name="cause"></a>原因
 
以下是此問題的可能原因:

* 所選作用域上沒有正確的訪問許可權。
* ARG 查詢不檢索預期計算機。
* 計算機上未安裝混合 Runbook 工作線程。

### <a name="resolution"></a>解決方案 

#### <a name="incorrect-access-on-selected-scopes"></a>對選取的範圍的存取不正確

Azure 門戶僅顯示在給定作用域中具有寫入訪問許可權的計算機。 如果作用網域沒有正確的存取權限,請參閱[教學:使用 RBAC 和 Azure 入口授予使用者對 Azure 資源的存取權限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG 查詢不傳回預期電腦

按照以下步驟了解查詢是否正常工作。

1. 運行在 Azure 門戶中的「資源圖資源管理器」邊欄選項卡中格式化的 ARG 查詢。 此查詢模擬您在更新管理中創建動態群組時選擇的篩選器。 請參考[動態群組與更新管理一起使用](https://docs.microsoft.com/azure/automation/automation-update-management-groups)。 

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
 
2. 檢查要查找的計算機是否列在查詢結果中。 

3. 如果未列出計算機,則動態組中選擇的篩選器可能存在問題。 根據需要調整組配置。

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>未安裝在電腦上的混合 Runbook 工作線程

計算機確實顯示在 ARG 查詢結果中,但仍未顯示在動態組預覽中。 在這種情況下,計算機可能無法指定為混合工作,因此無法運行 Azure 自動化和更新管理作業。 為確保您希望看到的電腦設定為混合 Runbook 工作簿:

1. 在 Azure 門戶中,轉到未正確顯示的電腦的自動化帳戶。

2. 在 **「過程自動化**」 選擇**混合工作群組**。

3. 選擇 **「系統混合輔助角色群組**」選項卡。

4. 驗證該計算機是否存在混合工作線程。

5. 如果電腦未設置為混合輔助角色,請使用[「混合 Runbook 輔助角色」在資料中心或雲端中的自動化資源處使用](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)說明進行調整。

6. 將電腦加入混合 Runbook 工作群體。

7. 對於未在預覽中顯示的所有計算機,請重複上述步驟。

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>方案:啟用了用於更新管理解決方案的元件,而 VM 繼續顯示為已配置

### <a name="issue"></a>問題

上架後 15 分鐘，您會繼續在虛擬機器上看見下列訊息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* 與自動化帳戶的通信被阻止。

* 有一個重複的計算機名稱與不同的源計算機指示。 當在不同的資源組中創建具有特定計算機名稱的 VM 並報告到訂閱中的同一物流代理工作區時,將發生此情況。

* 正在載入的 VM 映像可能來自未準備好安裝 Microsoft 監視代理 (MMA) 的系統準備 (sysprep) 的複製電腦。

### <a name="resolution"></a>解決方案

為了説明確定 VM 的確切問題,請在連結到自動化帳戶的日誌分析工作區中運行以下查詢。

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>與自動化帳戶的通訊被阻止

轉到[網路規劃](../automation-update-management.md#ports)以瞭解必須允許哪些位址和埠才能更新管理工作。

#### <a name="duplicate-computer-name"></a>重覆的電腦名稱

重新命名 VM 以確保其環境中的唯一名稱。

#### <a name="onboarded-image-from-cloned-machine"></a>隆卡電腦的板載映像

如果使用複製檔,則不同的電腦名稱具有相同的源電腦 ID。 在此案例中：

1. 在日誌分析工作區中,如果顯示`MicrosoftDefaultScopeConfig-Updates`範圍配置,則從保存的搜索中刪除 VM。 您可以在工作區中的 [一般]**** 底下找到儲存的搜尋。

2. 運行以下 cmdlet。

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. 運行`Restart-Service HealthService`以重新啟動運行狀況服務。 此操作將重新建立金鑰並生成新的 UUID。

4. 如果此方法不起作用,請先在映射上運行 Sysprep,然後安裝 MMA。

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>方案:在為另一個 Azure 租戶中的電腦建立更新部署時,您會收到連結的訂閱錯誤

### <a name="issue"></a>問題

當您嘗試為另一個 Azure 租戶中的電腦建立更新部署時,您會遇到以下錯誤:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

當您創建的更新部署時,將 Azure VM 包含在更新部署中的另一個租戶中,將發生此錯誤。

### <a name="resolution"></a>解決方案

使用以下解決方法來安排這些專案。 您可以使用`ForUpdate`[帶有參數的「新建 AzureRm自動化計畫](/powershell/module/azurerm.automation/new-azurermautomationschedule)cmdlet」創建計畫。 然後,使用[New-AzureRm 自動化軟體更新配置](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
)cmdlet 並將另一個租戶`NonAzureComputer`中的計算機傳遞給 參數。 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>配置:無法解釋的重新啟動

### <a name="issue"></a>問題

即使您將 **「重新啟動控制」** 選項設定為 **「從不重新啟動**」,但安裝更新後,電腦仍會重新啟動。

### <a name="cause"></a>原因

Windows 更新可以由多個註冊表項修改,其中任一註冊表項都可以修改重新啟動行為。

### <a name="resolution"></a>解決方案

通過編輯[用於管理重新啟動的註冊表和註冊表項](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart),以確保電腦配置正確,查看[配置自動更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)下的註冊表項。

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>方案:計算機在更新部署中顯示"無法啟動"

### <a name="issue"></a>問題

電腦顯示狀態`Failed to start`。 檢視電腦的特定詳細資訊時,您將看到以下錯誤:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

此錯誤可能會因為下列其中一個原因而發生：

* 這台機器已經不存在了。
* 機器已關閉且無法訪問。
* 計算機有網路連接問題,因此無法聯繫到電腦上的混合工作線程。
* 有一個更新的MMA,改變了源計算機 ID。
* 如果達到自動化帳戶中 2000 個併發作業的限制,則更新運行將受到限制。 每個部署都被視為作業,更新部署中的每台電腦都算作作業。 自動化帳戶中當前運行的任何其他自動化作業或更新部署都計入併發作業限制。

### <a name="resolution"></a>解決方案

如果適用,請對更新部署使用[動態群組](../automation-update-management-groups.md)。 此外,您可以執行以下步驟。

1. 驗證機器是否仍然存在且可到達。 
2. 如果電腦不存在,請編輯部署並刪除電腦。
3. 有關更新管理所需的埠和位址的清單,請參閱[網路規劃](../automation-update-management.md#ports)部分,然後驗證計算機是否滿足這些要求。
4. 使用混合 Runbook 工作線程代理疑難解答驗證與混合 Runbook 輔助角色的連接。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。
5. 在日誌分析中運行以下查詢,以查找源計算機 ID 已更改的環境中的電腦。 查找具有相同`Computer`值但值`SourceComputerId`不同的 電腦。

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. 找到受影響的電腦後,編輯針對這些電腦的更新部署,然後刪除並讀取它們,`SourceComputerId`以反映正確的值。

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>方案:在不部署的情況下安裝更新

### <a name="issue"></a>問題

在更新管理中註冊 Windows 計算機時,您將看到未部署即可安裝的更新。

### <a name="cause"></a>原因

在 Windows 上,更新一旦可用即自動安裝。 如果不計劃將更新部署到計算機,則此行為可能會導致混淆。

### <a name="resolution"></a>解決方案

註冊表`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`項目預設為 4: `auto download and install`。

對於更新管理客戶端,我們建議將此鍵設定為`auto download but do not auto install`3: 。

有關詳細資訊,請參考[設定自動更新](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>案例：電腦已經註冊至不同的帳戶

### <a name="issue"></a>問題

您收到下列錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

計算機已連接到另一個工作區以進行更新管理。

### <a name="resolution"></a>解決方案

1. 按照[「電腦」下的步驟不顯示在"更新管理"下的門戶中](#nologs),以確保電腦報告到正確的工作區。
2. 通過[刪除混合 Runbook 組](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)來清理計算機上的工件,然後重試。

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>配置:電腦無法與服務通訊

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

代理、閘道或防火牆可能阻止網路通訊。 

### <a name="resolution"></a>解決方案

查看您的網路並確保允許適當的埠和位址。 有關更新管理和混合 Runbook Worker 所需的連接埠和位址的清單,請參考[網路要求](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>案例：無法建立自我簽署憑證

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合 Runbook 工作線程無法生成自簽名證書。

### <a name="resolution"></a>解決方案

驗證系統帳戶是否對**C:_程式資料\Microsoft_加密\RSA**資料夾具有讀取存取許可權,然後重試。

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>方案:計劃更新失敗,出現超出維護視窗的錯誤

### <a name="issue"></a>問題

更新的默認維護視窗為 120 分鐘。 您可以將維護視窗增加到最多 6 小時或 360 分鐘。

### <a name="resolution"></a>解決方案

編輯任何失敗的計畫更新部署,並增加維護視窗。

有關維護視窗的詳細資訊,請參考[安裝更新](../automation-tutorial-update-management.md#schedule-an-update-deployment)。

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>方案:機器顯示為「未評估」並顯示 HRESULT 異常

### <a name="issue"></a>問題

* 您有顯示在`Not assessed`**「符合性」** 下的電腦,並在它們下方看到一條異常消息。
* 在門戶中可以看到 HRESULT 錯誤代碼。

### <a name="cause"></a>原因

更新代理(Windows 上的 Windows 更新代理;Linux 發行版的包管理員)配置不正確。 更新管理依賴於電腦的更新代理提供所需的更新、修補程式的狀態以及已部署修補程序的結果。 如果沒有此資訊,更新管理將無法正確報告需要或安裝的修補程式。

### <a name="resolution"></a>解決方案

嘗試在電腦上本地執行更新。 如果此操作失敗,則通常意味著存在更新代理配置錯誤。

此問題通常是由網路配置和防火牆問題引起的。 使用以下檢查更正問題。

* 對於 Linux,請檢查適當的文件,以確保您可以到達包存儲庫的網路終結點。

* 對 Windows,請檢查「更新」 中列的代理設定[未從 Intranet 的終結點 (WSUS/SCCM) 下載](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)。

  * 如果電腦配置為 Windows 更新,請確保可以到達[HTTP/代理問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中描述的終結點。
  * 如果電腦設定為 Windows 伺服器更新服務 (WSUS),請確保可以到達[由 WUServer 註冊表項](/windows/deployment/update/waas-wu-settings)配置的 WSUS 伺服器。

如果看到 HRESULT,按兩下以紅色顯示的異常以查看整個異常消息。 查看下表,查看潛在的解決方案或建議的操作。

|例外狀況  |解決或行動  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在[Windows 更新錯誤代碼清單中](https://support.microsoft.com/help/938205/windows-update-error-code-list)搜索相關錯誤代碼,以查找有關異常原因的其他詳細資訊。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 這些表示網路連接問題。 確保電腦具有與更新管理的網路連接。 有關所需埠和位址的清單,請參閱[網路規劃](../automation-update-management.md#ports)部分。        |
|`0x8024001E`| 更新操作未完成,因為服務或系統正在關閉。|
|`0x8024002E`| Windows 更新服務已禁用。|
|`0x8024402C`     | 如果使用 WSUS 伺服器,`WUServer``WUStatusServer``HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate`請確保 註冊表項的註冊表值和註冊表項下的註冊表值指定正確的 WSUS 伺服器。        |
|`0x80072EE2`|與配置的 WSUS 伺服器交談時存在網路連接問題或問題。 檢查 WSUS 設置,並確保可從用戶端訪問該服務。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 確保 Windows 更新服務 (wuauserv) 正在運行且未禁用。        |
|`0x80070005`| 存取被拒絕錯誤可能由以下任一原因引起:<br> 受感染的電腦<br> 未正確設定的 Windows 更新設定<br> 包含 %WinDir%_軟體分發資料夾的檔案權限錯誤<br> 系統驅動器 (C:) 上的磁碟空間不足。
|任何其他一般例外狀況     | 在網路上搜索可能的解決方案,並與您的本地 IT 支援合作。         |

檢視 **%Windir%*Windowsupdate.log**檔還可以幫助您確定可能的原因。 有關如何讀取紀錄的詳細資訊,請參閱[如何讀取 Windowsupdate.log 檔](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)。

您還可以下載並運行 Windows[更新疑難排解](https://support.microsoft.com/help/4027322/windows-update-troubleshooter),以檢查電腦上的 Windows 更新是否有任何問題。

> [!NOTE]
> [Windows 更新疑難排](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)解 文件指示它用於 Windows 用戶端,但它也適用於 Windows 伺服器。

## <a name="scenario-update-run-returns-failed-status-linux"></a>專案:更新執行回失敗狀態(Linux)

### <a name="issue"></a>問題

更新運行將啟動,但在運行過程中遇到錯誤。

### <a name="cause"></a>原因

可能的原因：

* 包管理器不正常。
* 更新代理(適用於 Windows 的 WUA,Linux 的特定於版本解的套件管理員)配置錯誤。
* 特定包干擾基於雲的修補。
* 機器無法到達。
* 更新具有未解決的依賴項。

### <a name="resolution"></a>解決方案

如果在更新成功啟動後執行期間發生故障,請在執行中檢查來自受影響電腦的[工作輸出](../manage-update-multi.md#view-results-of-an-update-deployment)。 您可能會從電腦中找到特定的錯誤訊息,您可以研究和執行操作。 「更新管理」必須在套件管理員狀況良好的情況下，才能進行成功的更新部署。

如果在作業失敗之前立即看到特定的修補程式、包或更新,則可以嘗試從下一個更新部署[中排除](../automation-tutorial-update-management.md#schedule-an-update-deployment)這些專案。 要從 Windows 更新收集紀錄資訊,請參閱[Windows 更新紀錄檔](/windows/deployment/update/windows-update-logs)。

如果無法解決修補問題,請複製 **/var/opt/microsoft/omsagent/運行/自動化工作人員/omsupdatemgmt.log**檔,並在下一次更新部署開始之前保留該檔以進行故障排除。

## <a name="patches-arent-installed"></a>未安裝修補程式

### <a name="machines-dont-install-updates"></a>機器未安裝更新

請嘗試直接在機器上執行更新。 若電腦無法應用更新,請參閱[故障排除指南中的潛在錯誤清單](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)。

如果更新在本地運行,請嘗試按照[更新管理中刪除 VM](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)中的指南在電腦上刪除和重新安裝代理。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道更新是可用的,但它們不能在我的電腦上顯示

如果電腦設定為從 WSUS 或 Microsoft 終結點設定管理器獲取更新,但 WSUS 和配置管理員尚未批准更新,則通常會發生這種情況。

您可以通過在本文`UseWUServer`的[註冊表部分](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)中將註冊表鍵交叉引用註冊表項來檢查計算機是否配置為 WSUS 和 SCCM。

如果 WSUS 中未批准更新,則未安裝更新。 您可以通過執行以下查詢來檢查日誌分析中的未批准更新。

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新會顯示為已安裝，但我在機器上找不到這些更新

更新常會被其他更新取代。 有關詳細資訊,請參閱在 Windows 更新故障排除指南中[取代更新](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>依分類在 Linux 上安裝更新

依分類 (「重大與安全性更新」) 將更新部署至 Linux 時有重要事項須留意，尤其是針對 CentOS。 這些限制紀錄在[更新管理概述頁上](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 始終缺失

KB2267602 是 [Windows Defender 定義更新](https://www.microsoft.com/wdsi/definitions)。 它每天更新。

## <a name="next-steps"></a>後續步驟

如果您沒有看到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援。

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,用於改善客戶體驗的官方 Microsoft Azure 帳戶。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
