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
ms.openlocfilehash: 91bcdc65a7ff3bcaf09f12d69ba4c7aaeb84ffa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132863"
---
# <a name="troubleshooting-issues-with-update-management"></a>針對更新管理問題進行疑難排解

本文討論在使用更新管理時可能會遇到的問題的解決方案。

混合輔助角色代理有一個代理疑難排解，用於確定根本問題。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。 對於所有其他問題，請使用以下故障排除指南。

如果在嘗試在虛擬機器 （VM） 上加入解決方案時遇到問題，請查看本地電腦上的 **"應用程式和服務日誌**"下`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`**的操作管理器**日誌，瞭解事件 ID 4502 的事件以及包含 的事件詳細資訊。

以下部分重點介紹特定錯誤訊息和每個錯誤訊息的可能解決方法。 有關其他載入問題，請參閱[載入的疑難排解解決方案](onboarding.md)。

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>方案：您會收到錯誤"無法啟用更新解決方案"

### <a name="issue"></a>問題

當您嘗試在自動化帳戶中啟用更新管理解決方案時，您會遇到以下錯誤：

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* 日誌分析代理的網路防火牆要求可能未正確配置，從而導致代理在解決 DNS URL 時失敗。

* 解決方案定位配置錯誤，電腦未按預期接收更新。

* 您可能還注意到，電腦顯示的`Non-compliant`"**符合性"** 狀態。 同時，**代理更新就緒將**代理報告為`Disconnected`。

### <a name="resolution"></a>解決方案

* 運行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解，具體取決於作業系統。

* 轉到[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)以瞭解必須允許哪些位址和埠才能更新管理工作。  

* 轉到[網路規劃](../../azure-monitor/platform/log-analytics-agent.md#network-requirements)以瞭解日誌分析代理必須允許哪些位址和埠工作。

* 檢查範圍配置問題。 [範圍配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)確定為解決方案配置了哪些電腦。 如果電腦顯示在工作區中，但未顯示在 **"更新管理"** 門戶中，則需要將範圍配置設置為目的電腦。 要瞭解作用域配置，請參閱[工作區中的板載電腦](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 按照[刪除混合 Runbook 輔助角色](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)的步驟刪除輔助角色配置。 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>方案：在更新管理中指示缺少的已取代更新

### <a name="issue"></a>問題

舊更新出現在自動化帳戶中的"更新管理"中，即使它們已被取代，也顯示為丟失。 取代的更新是不必安裝的更新，因為稍後的更新可以更正相同的漏洞。 更新管理忽略被取代的更新，使其不適用於取代的更新。 有關相關問題的資訊，請參閱[更新被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="cause"></a>原因

被取代的更新未正確指示為已拒絕，因此可以認為它們不適用。

### <a name="resolution"></a>解決方案

當被取代的更新變為 100% 不適用時，應將該更新的審批狀態更改為`Declined`。 要對所有更新執行此操作，請執行以下操作：

1. 在"自動化"帳戶中，選擇 **"更新管理**"以查看電腦狀態。 請參閱[查看更新評估](../manage-update-multi.md#view-an-update-assessment)。

2. 檢查被取代的更新，以確保它是 100% 不適用。 

3. 除非對更新有疑問，否則將更新標記為已拒絕。 

4. 選擇 **"電腦**"，並在 **"合規性"** 列中強制重新掃描符合性。 請參閱[管理多台電腦的更新](../manage-update-multi.md)。

5. 對其他被取代的更新重複上述步驟。

6. 運行清理嚮導以從已拒絕的更新中刪除檔。 

7. 對於 WSUS，手動清理所有被取代的更新以刷新基礎結構。

8. 定期重複此過程以更正顯示問題並最小化用於更新管理的磁碟空間量。

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>方案：電腦不會顯示在更新管理下的門戶中

### <a name="issue"></a>問題

您的機器有以下症狀：

* 您的電腦從`Not configured`VM 的更新管理視圖中顯示。

* Azure 自動化帳戶的更新管理視圖中缺少電腦。

* 您有顯示在**合規**下的電腦`Not assessed`。 但是，在 Azure 監視器日誌中看到混合 Runbook 輔助角色中的活動訊號資料，但不適用於更新管理。

### <a name="cause"></a>原因

此問題可能由本地配置問題或配置不正確的範圍配置引起。

您可能需要重新註冊並重新安裝混合 Runbook 輔助角色。

您可能已在工作區中定義了已達到的配額，這阻止了進一步的資料存儲。

### <a name="resolution"></a>解決方案

* 運行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解，具體取決於作業系統。

* 確保電腦報告到正確的工作區。 有關如何驗證此方面的指導，請參閱[驗證代理連接到日誌分析](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)。 還要確保此工作區連結到 Azure 自動化帳戶。 要確認，請轉到您的自動化帳戶，並在 **"相關資源**"下選擇 **"連結工作區**"。

* 確保電腦顯示在日誌分析工作區中。 在連結到自動化帳戶的日誌分析工作區中運行以下查詢：

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
 
  如果在查詢結果中看不到電腦，則電腦最近未簽入，這意味著可能存在本地配置問題，應[重新安裝代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。 如果電腦顯示在查詢結果中，則需要驗證此清單中下一個專案符號項中指定的範圍配置。

* 檢查範圍配置問題。 [範圍配置](../automation-onboard-solutions-from-automation-account.md#scope-configuration)確定為解決方案配置了哪些電腦。 如果電腦顯示在工作區中，但未顯示在 **"更新管理**"門戶中，則需要將範圍配置配置為以電腦為目標。 要瞭解如何執行此操作，請參閱[工作區中的板載電腦](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 在工作區中，運行以下查詢：

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

  如果得到結果`Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`，工作區上定義了已到達的配額，並且阻止保存資料。 在工作區中，轉到 **"使用方式"和"估計成本** > **"資料量管理**，並檢查配額或刪除配額。

* 如果這些步驟不能解決問題，請按照["部署 Windows 混合 Runbook 輔助角色"](../automation-windows-hrw-install.md)中的步驟重新安裝 Windows 的混合輔助角色。 對於 Linux，請按照[部署 Linux 混合 Runbook 輔助手冊 中](../automation-linux-hrw-install.md)的步驟操作。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>方案：無法註冊訂閱的自動化資來源提供者

### <a name="issue"></a>問題

當您使用自動化帳戶中的解決方案時，您會遇到以下錯誤：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

未在訂閱中註冊自動化資來源提供者。

### <a name="resolution"></a>解決方案

要註冊自動化資來源提供者，請在 Azure 門戶中執行以下步驟：

1. 在門戶底部的 Azure 服務清單中，選擇 **"所有服務**"，然後在"常規服務"組中選擇 **"訂閱**"。
2. 選取您的訂用帳戶。
3. 在 **"設置"** 下，選擇 **"資來源提供者**"。
4. 從資來源提供者清單中，驗證`Microsoft.Automation`資來源提供者是否已註冊。
5. 如果未列出，請按照`Microsoft.Automation`[資來源提供者註冊"解決錯誤"](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步驟註冊提供程式。

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>方案：啟用了用於更新管理解決方案的元件，而 VM 繼續顯示為已配置

### <a name="issue"></a>問題

上架後 15 分鐘，您會繼續在虛擬機器上看見下列訊息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

* 與自動化帳戶的通信被阻止。

* 有一個重複的電腦名稱稱與不同的源電腦指示。 當在不同的資源組中創建具有特定電腦名稱稱的 VM 並報告到訂閱中的同一物流代理工作區時，將發生此情況。

* 正在裝入的 VM 映射可能來自未準備好安裝 Microsoft 監視代理 （MMA） 的系統準備 （sysprep） 的克隆電腦。

### <a name="resolution"></a>解決方案

為了説明確定 VM 的確切問題，請在連結到自動化帳戶的日誌分析工作區中運行以下查詢：

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>與自動化帳戶的通信被阻止

轉到[網路規劃](../automation-update-management.md#ports)以瞭解必須允許哪些位址和埠才能更新管理工作。

#### <a name="duplicate-computer-name"></a>重複的電腦名稱稱

重命名 VM 以確保其環境中的唯一名稱。

#### <a name="onboarded-image-from-cloned-machine"></a>克隆電腦的板載映射

如果使用克隆映射，則不同的電腦名稱稱具有相同的源電腦 ID。 在此案例中：

1. 在日誌分析工作區中，如果顯示`MicrosoftDefaultScopeConfig-Updates`範圍配置，則從保存的搜索中刪除 VM。 您可以在工作區中的 [一般]**** 底下找到儲存的搜尋。

2. 執行下列 Cmdlet：

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. 運行`Restart-Service HealthService`以重新開機運行狀況服務。 此操作將重新創建金鑰並生成新的 UUID。

4. 如果此方法不起作用，請先在映射上運行 sysprep，然後安裝 MMA。

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>方案：在為另一個 Azure 租戶中的電腦創建更新部署時，您會收到連結的訂閱錯誤

### <a name="issue"></a>問題

當您嘗試為另一個 Azure 租戶中的電腦創建更新部署時，您會遇到以下錯誤：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

當您創建的更新部署時，將 Azure VM 包含在更新部署中的另一個租戶中，將發生此錯誤。

### <a name="resolution"></a>解決方案

使用以下解決方法來安排這些專案。 您可以使用`ForUpdate`[帶有參數的"新建 AzureRm自動化計畫](/powershell/module/azurerm.automation/new-azurermautomationschedule)Cmdlet"創建計畫。 然後，使用[New-AzureRm自動化軟體更新配置](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
)Cmdlet 並將另一個租戶中的電腦傳遞給`NonAzureComputer`參數。 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>方案：無法解釋的重新開機

### <a name="issue"></a>問題

即使您將 **"重新開機控制"** 選項設置為 **"從不重新開機**"，但安裝更新後，電腦仍會重新開機。

### <a name="cause"></a>原因

Windows 更新可以由多個登錄機碼修改，其中任一登錄機碼都可以修改重新開機行為。

### <a name="resolution"></a>解決方案

通過編輯[用於管理重新開機的註冊表和登錄機碼](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)，以確保電腦配置正確，查看[配置自動更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)下的登錄機碼。

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>方案：電腦在更新部署中顯示"無法啟動"

### <a name="issue"></a>問題

電腦顯示狀態`Failed to start`。 查看電腦的特定詳細資訊時，您將看到以下錯誤：

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

此錯誤可能會因為下列其中一個原因而發生：

* 這台機器已經不存在了。
* 機器已關閉且無法訪問。
* 電腦有網路連接問題，因此無法連絡到電腦上的混合工作執行緒。
* 有一個更新的MMA，改變了源電腦 ID。
* 如果達到自動化帳戶中 2000 個併發作業的限制，則更新運行將受到限制。 每個部署都被視為作業，更新部署中的每台電腦都算作作業。 自動化帳戶中當前運行的任何其他自動化作業或更新部署都計入併發作業限制。

### <a name="resolution"></a>解決方案

如果適用，請對更新部署使用[動態組](../automation-update-management-groups.md)。 此外：

* 驗證機器是否仍然存在且可到達。 
* 如果電腦不存在，請編輯部署並刪除電腦。
* 有關更新管理所需的埠和位址的清單，請參閱[網路規劃](../automation-update-management.md#ports)部分，然後驗證電腦是否滿足這些要求。
* 使用混合 Runbook 工作執行緒代理疑難排解驗證與混合 Runbook 輔助角色的連接。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。
* 在日誌分析中運行以下查詢，以查找源電腦 ID 已更改的環境中的電腦。 查找具有相同`Computer`值但值不同的`SourceComputerId`電腦。

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

* 找到受影響的電腦後，編輯針對這些電腦的更新部署，然後刪除並重新添加它們，`SourceComputerId`以反映正確的值。

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>方案：在不部署的情況下安裝更新

### <a name="issue"></a>問題

在更新管理中註冊 Windows 電腦時，您將看到未部署即可安裝的更新。

### <a name="cause"></a>原因

在 Windows 上，更新一旦可用即自動安裝。 如果不計畫將更新部署到電腦，則此行為可能會導致混淆。

### <a name="resolution"></a>解決方案

註冊表`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`項預設為 4： `auto download and install`。

對於更新管理用戶端，我們建議將此鍵設置為 3： `auto download but do not auto install`。

有關詳細資訊，請參閱[配置自動更新](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>案例：電腦已經註冊至不同的帳戶

### <a name="issue"></a>問題

您收到下列錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

電腦已連接到另一個工作區以進行更新管理。

### <a name="resolution"></a>解決方案

1. 按照["電腦"下的步驟不顯示在"更新管理"下的門戶中](#nologs)，以確保電腦報告到正確的工作區。
2. 通過[刪除混合 Runbook 組](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)來清理電腦上的工件，然後重試。

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>方案：電腦無法與服務通信

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

代理、閘道或防火牆可能阻止網路通信。 

### <a name="resolution"></a>解決方案

查看您的網路並確保允許適當的埠和位址。 有關更新管理和混合 Runbook Worker 所需的埠和位址的清單，請參閱[網路要求](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>案例：無法建立自我簽署憑證

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合 Runbook 工作執行緒無法生成自簽章憑證。

### <a name="resolution"></a>解決方案

驗證系統帳戶是否對**C：_程式資料\Microsoft_加密\RSA**資料夾具有讀取存取許可權，然後重試。

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>方案：計畫更新失敗，出現超出維護視窗的錯誤

### <a name="issue"></a>問題

更新的預設維護視窗為 120 分鐘。 您可以將維護視窗增加到最多 6 小時或 360 分鐘。

### <a name="resolution"></a>解決方案

編輯任何失敗的計畫更新部署，並增加維護視窗。

有關維護視窗的詳細資訊，請參閱[安裝更新](../automation-tutorial-update-management.md#schedule-an-update-deployment)。

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>方案：機器顯示為"未評估"並顯示 HRESULT 異常

### <a name="issue"></a>問題

* 您有顯示在`Not assessed`**"符合性"** 下的電腦，並在它們下方看到一條異常消息。
* 在門戶中可以看到 HRESULT 錯誤代碼。

### <a name="cause"></a>原因

更新代理（Windows 上的 Windows 更新代理;Linux 發行版本的包管理器）配置不正確。 更新管理依賴于電腦的更新代理提供所需的更新、修補程式的狀態以及已部署修補程式的結果。 如果沒有此資訊，更新管理將無法正確報告需要或安裝的修補程式。

### <a name="resolution"></a>解決方案

嘗試在電腦上本地執行更新。 如果失敗，它通常意味著更新代理存在配置錯誤。

此問題通常是由網路設定和防火牆問題引起的。 請嘗試下列作業：

* 對於 Linux，請檢查適當的文檔，以確保您可以到達包存儲庫的網路終結點。
* 對於 Windows，請檢查"更新"中列出的代理配置[未從 Intranet 終結點 （WSUS/SCCM） 下載](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)。
  * 如果電腦配置為 Windows 更新，請確保可以到達[HTTP/代理問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中描述的終結點。
  * 如果電腦配置為 Windows 伺服器更新服務 （WSUS），請確保可以到達[由 WUServer 登錄機碼](/windows/deployment/update/waas-wu-settings)配置的 WSUS 伺服器。

如果看到 HRESULT，按兩下以紅色顯示的異常以查看整個異常消息。 查看下表，查看潛在的解決方案或建議的操作：

|例外狀況  |解決或行動  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在[Windows 更新錯誤代碼清單中](https://support.microsoft.com/help/938205/windows-update-error-code-list)搜索相關錯誤代碼，以查找有關異常原因的其他詳細資訊。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 這些表示網路連接問題。 確保電腦具有與更新管理的網路連接。 有關所需埠和位址的清單，請參閱[網路規劃](../automation-update-management.md#ports)部分。        |
|`0x8024001E`| 更新操作未完成，因為服務或系統正在關閉。|
|`0x8024002E`| Windows 更新服務已禁用。|
|`0x8024402C`     | 如果使用 WSUS 伺服器，請確保`WUServer``WUStatusServer``HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate`登錄機碼的註冊表值和登錄機碼下的註冊表值指定正確的 WSUS 伺服器。        |
|`0x80072EE2`|與配置的 WSUS 伺服器交談時存在網路連接問題或問題。 檢查 WSUS 設置，並確保可從用戶端存取該服務。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 確保 Windows 更新服務 （wuauserv） 正在運行且未禁用。        |
|`0x80070005`| 訪問被拒絕錯誤可能由以下任一原因引起：<br> 受感染的電腦<br> 未正確配置的 Windows 更新設置<br> 包含 %WinDir%_軟體發佈資料夾的檔許可權錯誤<br> 系統磁碟機 （C：） 上的磁碟空間不足。
|任何其他一般例外狀況     | 在互聯網上搜索可能的解決方案，並與您的本地 IT 支援合作。         |

查看 %Windir%*Windowsupdate.log 檔還可以説明您確定可能的原因。 有關如何讀取日誌的詳細資訊，請參閱[如何讀取 Windowsupdate.log 檔](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)。

您還可以下載並運行 Windows[更新疑難排解](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，以檢查電腦上的 Windows 更新是否有任何問題。

> [!NOTE]
> [Windows 更新疑難排解](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)文檔指示它用於 Windows 用戶端，但它也適用于 Windows 伺服器。

## <a name="scenario-update-run-returns-failed-status-linux"></a>方案：更新運行返回"失敗"狀態（Linux）

### <a name="issue"></a>問題

更新運行將啟動，但在運行過程中遇到錯誤。

### <a name="cause"></a>原因

可能的原因：

* 包管理器不正常。
* 更新代理（適用于 Windows 的 WUA，Linux 的特定于版本解的套裝軟體管理器）配置錯誤。
* 特定包乾擾基於雲的修補。
* 機器無法到達。
* 更新具有未解決的依賴項。

### <a name="resolution"></a>解決方案

如果在更新成功啟動後運行期間發生故障，請在運行中檢查來自受影響電腦的[作業輸出](../manage-update-multi.md#view-results-of-an-update-deployment)。 您可能會從電腦中找到特定的錯誤訊息，您可以研究和執行操作。 「更新管理」必須在套件管理員狀況良好的情況下，才能進行成功的更新部署。

如果在作業失敗之前立即看到特定的修補程式、包或更新，則可以嘗試將修補程式、包或更新從下一個更新部署[中排除](../automation-tutorial-update-management.md#schedule-an-update-deployment)。 要從 Windows 更新收集日誌資訊，請參閱[Windows 更新日誌檔](/windows/deployment/update/windows-update-logs)。

如果無法解決修補問題，請複製以下日誌檔並將其保留以用於下一個更新部署開始之前：

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>未安裝修補程式

### <a name="machines-dont-install-updates"></a>機器未安裝更新

請嘗試直接在機器上執行更新。 如果電腦無法應用更新，請參閱[故障排除指南中的潛在錯誤清單](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)。

如果更新在本地運行，請嘗試按照[更新管理中刪除 VM](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)中的指南在電腦上刪除和重新安裝代理。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道更新是可用的，但它們不能在我的電腦上顯示

如果電腦配置為從 WSUS 或 Microsoft 終結點組態管理員獲取更新，但 WSUS 和組態管理員尚未批准更新，則通常會發生這種情況。

您可以通過在本文的`UseWUServer`[註冊表部分](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)中將註冊表鍵交叉引用登錄機碼來檢查電腦是否配置為 WSUS 和 SCCM。

如果 WSUS 中未批准更新，則不會安裝更新。 您可以通過運行以下查詢來檢查日誌分析中的未批准更新。

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新會顯示為已安裝，但我在機器上找不到這些更新

更新常會被其他更新取代。 有關詳細資訊，請參閱在 Windows 更新故障排除指南中[取代更新](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>依分類在 Linux 上安裝更新

依分類 (「重大與安全性更新」) 將更新部署至 Linux 時有重要事項須留意，尤其是針對 CentOS。 這些限制記錄在[更新管理概述頁上](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 始終缺失

KB2267602 是 [Windows Defender 定義更新](https://www.microsoft.com/wdsi/definitions)。 它每天更新。

## <a name="next-steps"></a>後續步驟

如果您沒有看到問題或無法解決問題，請嘗試以下管道之一以獲取其他支援：

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ，官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源（答案、支援和專家）來改善客戶體驗。
* 提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。
