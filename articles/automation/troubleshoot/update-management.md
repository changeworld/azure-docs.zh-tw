---
title: 針對 Azure 更新管理的錯誤進行疑難排解
description: 瞭解如何在 Azure 中疑難排解和解決更新管理解決方案的問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 359f78cabbe0372e6892695c092ae49b62df7bfa
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944189"
---
# <a name="troubleshooting-issues-with-update-management"></a>針對更新管理問題進行疑難排解

本文討論當您使用更新管理時可能會遇到之問題的解決方案。

混合式背景工作角色代理程式有一個代理程式疑難排解員可判斷根本問題。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。 針對所有其他問題，請使用下列疑難排解指引。

如果您在嘗試將解決方案上架到虛擬機器（VM）時遇到問題，請檢查本機電腦上 **應用程式和服務記錄**檔 下的  **Operations Manager**記錄檔中是否有事件識別碼為4502的事件，以及包含**AzureAutomation microsoft.enterprisemanagement.healthservice.azureautomation.hybridagent 事件**的事件詳細資料。

下一節將重點放在特定的錯誤訊息，以及各項的可能解決方法。 如需其他上線問題，請參閱針對[解決方案上架進行疑難排解](onboarding.md)。

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>案例：已取代的更新在更新管理中指出遺失

### <a name="issue"></a>問題

舊的更新會在 Azure 帳戶的更新管理中顯示為遺失，即使已被取代也一樣。 已取代的更新是一種不需要安裝的更新，因為更新會修正相同的弱點。 更新管理會忽略被取代的更新，使其不適用取代更新。 如需相關問題的詳細資訊，請參閱[更新已被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="cause"></a>原因

已取代的更新未正確地表示為已拒絕，因此可視為不適用。

### <a name="resolution"></a>解決方案

當已取代的更新變成100% 不適用時，您應該將該更新的核准狀態變更為 [已**拒絕**]。 若要針對您的所有更新執行此動作：

1. 在自動化帳戶中，選取 [**更新管理**] 以查看機器狀態。 請參閱[查看更新評](../manage-update-multi.md#view-an-update-assessment)量。

2. 檢查已取代的更新，確定其為100% 不適用。 

3. 除非您有關于更新的問題，否則請將更新標示為已拒絕。 

4. 選取 [電腦]，然後在 [相容性] 欄中強制重新掃描合規性。 請參閱[管理多部電腦的更新](../manage-update-multi.md)。

5. 針對其他已取代的更新重複上述步驟。

6. 執行 [清除嚮導]，從拒絕的更新中刪除檔案。 

7. 針對 WSUS，請手動清除所有已取代的更新，以重新整理基礎結構。

8. 定期重複此程式來更正顯示問題，並將更新管理所使用的磁碟空間量降到最低。

## <a name="nologs"></a>案例：電腦不會顯示在入口網站的 更新管理

### <a name="issue"></a>問題

您遇到下列徵兆：

* 您的機器顯示未從 VM 的更新管理視圖進行**設定**。

* 您的 Azure 自動化帳戶的更新管理視圖遺漏您的電腦。

* 您的機器在**合規性**之下會顯示為 [**未評估**]。 不過，您會看到混合式 Runbook 背景工作角色 Azure 監視器記錄中的心跳資料，但不會更新管理。

### <a name="cause"></a>原因

此問題可能是因本機設定問題或設定不正確的範圍而造成。

您可能需要重新註冊並重新安裝混合式 Runbook 背景工作角色。

您可能已在工作區中定義已達到的配額，因而導致無法進一步儲存資料。

### <a name="resolution"></a>解決方案

* 執行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解員，視作業系統而定。

* 請確定您的電腦已回報至正確的工作區。 如需如何驗證這方面的指引，請參閱[確認代理程式與 Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)的連線能力。 此外，請確定此工作區已連結至您的 Azure 自動化帳戶。 若要確認，請移至您的自動化帳戶，然後選取 [**相關資源**] 底下的 [**連結工作區**]

* 請確定電腦顯示在您的 Log Analytics 工作區中。 在與您的自動化帳戶連結的 Log Analytics 工作區中執行下列查詢：

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  如果您在查詢結果中看不到您的電腦，它最近未簽入，這表示可能是本機設定問題，您應該[重新安裝代理程式](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。 如果您的電腦顯示在查詢結果中，您必須確認此清單中下一個專案符號專案所指定的範圍設定。

* 檢查範圍設定問題。 [範圍](../automation-onboard-solutions-from-automation-account.md#scope-configuration)設定會決定哪些機器會針對解決方案進行設定。 如果您的電腦顯示在您的工作區中，但不在**更新管理**入口網站中，您將需要設定以電腦為目標的範圍設定。 若要瞭解如何執行這項操作，請參閱在[工作區中將電腦上架](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 在您的工作區中，執行下列查詢：

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  如果您得到 `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` 的結果，就會在您的工作區上定義配額，而且已停止儲存資料。 在您的工作區中，移至 [**使用量和估計成本**] > [**資料量管理**]，然後檢查您的配額或將其移除。

* 如果這些步驟無法解決您的問題，請遵循[部署 Windows 混合式 Runbook 背景工作角色](../automation-windows-hrw-install.md)中的步驟來重新安裝適用于 Windows 的混合式背景工作角色。 或者，針對 Linux，[部署 Linux 混合式 Runbook 背景工作角色](../automation-linux-hrw-install.md)。

## <a name="rp-register"></a>案例：無法註冊訂用帳戶的自動化資源提供者

### <a name="issue"></a>問題

當您在自動化帳戶中使用解決方案時，您會遇到下列錯誤：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

自動化資源提供者未在訂用帳戶中註冊。

### <a name="resolution"></a>解決方案

若要註冊自動化資源提供者，請遵循 Azure 入口網站中的下列步驟：

1. 在入口網站底部的 [Azure 服務] 清單中，選取 [**所有服務**]，然後選取 [一般] 服務群組**中的 [訂用帳戶**]。
2. 選取您的訂用帳戶。
3. 在 [**設定**] 下，選取 [**資源提供者**]。
4. 從資源提供者清單中，確認已註冊**Microsoft Automation**資源提供者。
5. 如果未列出，請遵循[解決資源提供者註冊錯誤](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步驟來註冊**Microsoft Automation**提供者。

## <a name="components-enabled-not-working"></a>案例：已啟用更新管理解決方案的元件，現在正在設定此虛擬機器

### <a name="issue"></a>問題

上架後 15 分鐘，您會繼續在虛擬機器上看見下列訊息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

下列原因會發生此錯誤：

- 正在封鎖與自動化帳戶的通訊。
- 所上架的 VM 可能來自未與安裝 Microsoft Monitoring Agent （MMA）執行過 sysprep 的複製電腦。

### <a name="resolution"></a>解決方案

1. 請前往[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)，瞭解必須允許哪些位址和埠，更新管理才能正常執行。
2. 如果您要使用複製的映射：
   1. 在您的 Log Analytics 工作區中，從已儲存的搜尋中移除 VM （如其所顯示的 `MicrosoftDefaultScopeConfig-Updates` 範圍設定）。 您可以在工作區中的 [一般] 底下找到儲存的搜尋。
   2. 執行 `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`。
   3. 執行 `Restart-Service HealthService` 以重新啟動 `HealthService`。 這會重新建立金鑰，並產生新的 UUID。
   4. 如果此方法無法運作，請先在映射上執行 sysprep，然後再安裝 MMA。

## <a name="multi-tenant"></a>案例：當您為另一個 Azure 租使用者中的電腦建立更新部署時，您會收到連結的訂用帳戶錯誤

### <a name="issue"></a>問題

當您嘗試在另一個 Azure 租使用者中建立電腦的更新部署時，您會遇到下列錯誤：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

當您在更新部署中所包含的另一個租使用者中建立具有 Azure Vm 的更新部署時，就會發生此錯誤。

### <a name="resolution"></a>解決方案

使用下列因應措施可讓這些專案排程。 您可以使用[new-azurermautomationschedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) Cmdlet 搭配 `-ForUpdate` 參數來建立排程。 然後，使用[new-azurermautomationsoftwareupdateconfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) Cmdlet，並將另一個租使用者中的電腦傳遞給 `-NonAzureComputer` 參數。 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>案例：無法解釋的重新開機

### <a name="issue"></a>問題

即使您已將 [**重新開機控制**] 選項設定為 [**永遠不重新開機**]，電腦仍會在安裝更新之後重新開機。

### <a name="cause"></a>原因

Windows Update 可以由數個登錄機碼修改，其中任何一個都可以修改重新開機行為。

### <a name="resolution"></a>解決方案

藉[由編輯](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)登錄和[用來管理重新開機](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的登錄機碼來檢查列于 [設定自動更新] 底下的登錄機碼，以確保您的電腦已正確設定。

## <a name="failed-to-start"></a>案例：電腦在更新部署中顯示「無法啟動」

### <a name="issue"></a>問題

機器顯示 [**無法啟動**] 狀態。 當您查看機器的特定詳細資料時，您會看到下列錯誤：

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

此錯誤可能會因為下列其中一個原因而發生：

* 機器不再存在。
* 機器已關閉且無法連線。
* 電腦發生網路連線問題，因此無法連線到電腦上的混合式背景工作角色。
* 變更了 SourceComputerId 的 MMA 已有更新。
* 如果您達到自動化帳戶中2000個並行作業的限制，則您的更新執行已節流。 系統會將每個部署視為一項作業，而更新部署中的每部電腦會計算為一項作業。 目前在您的自動化帳戶中執行的任何其他自動化作業或更新部署都會計入並行作業限制。

### <a name="resolution"></a>解決方案

適用時，請使用[動態群組](../automation-update-management-groups.md)進行更新部署。 此外：

* 確認電腦仍然存在且可連線。 
* 如果電腦不存在，請編輯您的部署，並移除電腦。
* 請參閱[網路規劃](../automation-update-management.md#ports)一節，以取得更新管理所需的埠和地址清單，然後確認您的電腦符合這些需求。
* 使用「混合式 Runbook 背景工作代理程式」疑難排解員驗證與混合式 Runbook 背景工作角色的連線。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。
* 在 Log Analytics 中執行下列查詢，以找出環境中 `SourceComputerId` 已變更的電腦。 尋找具有相同 `Computer` 值但不同 `SourceComputerId` 值的電腦。

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

* 在您找到受影響的機器之後，請編輯以這些機器為目標的更新部署，然後移除並重新加入它們，讓 `SourceComputerId` 反映正確的值。

## <a name="updates-nodeployment"></a>案例：已安裝不含部署的更新

### <a name="issue"></a>問題

當您在更新管理中註冊 Windows 機器時，您會看到已安裝但沒有部署的更新。

### <a name="cause"></a>原因

在 Windows 上，更新會在可用時立即安裝。 如果您未排程將更新部署到電腦上，這種行為可能會造成混淆。

### <a name="resolution"></a>解決方案

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` 登錄機碼預設為4：**自動下載並安裝**的設定。

針對更新管理用戶端，建議您將此機碼設為3：**自動下載，但不要自動安裝**。

如需詳細資訊，請參閱設定[自動更新](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="machine-already-registered"></a>案例：電腦已經註冊至不同的帳戶

### <a name="issue"></a>問題

您會收到下列錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

電腦已上架到另一個工作區以進行更新管理。

### <a name="resolution"></a>解決方案

1. 依照 [更新管理] 底下的 [[機器不會顯示在入口網站中](#nologs)] 底下的步驟，確認機器已回報至正確的工作區。
2. 藉由[刪除混合式 runbook 群組](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)來清除電腦上的舊成品，然後再試一次。

## <a name="machine-unable-to-communicate"></a>案例：電腦無法與服務通訊

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

## <a name="unable-to-create-selfsigned-cert"></a>案例：無法建立自我簽署憑證

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合式 Runbook 背景工作角色無法產生自我簽署憑證。

### <a name="resolution"></a>解決方案

請確認系統帳戶具有**C:\ProgramData\Microsoft\Crypto\RSA**資料夾的讀取權限，然後再試一次。

## <a name="mw-exceeded"></a>案例：排程更新失敗，發生 MaintenanceWindowExceeded 錯誤

### <a name="issue"></a>問題

更新的預設維護視窗是120分鐘。 您可以將維護期間增加到最多6小時或360分鐘。

### <a name="resolution"></a>解決方案

編輯任何失敗的排程更新部署，並增加維護時間範圍。

如需維護時段的詳細資訊，請參閱[安裝更新](../automation-tutorial-update-management.md#schedule-an-update-deployment)。

## <a name="hresult"></a>案例：電腦顯示為「未評估」，並顯示 HResult 例外狀況

### <a name="issue"></a>問題

* 您有在 [合規性] 底下顯示為 [未評估] 的機器，並在其下方看到例外狀況訊息。
* 您有顯示為 [未評估] 的電腦。
* 您會在入口網站中看到 HRESULT 錯誤碼。

### <a name="cause"></a>原因

更新代理程式（Windows 上的 Windows Update 代理程式、Linux 發行版本的套件管理員）未正確設定。 更新管理依賴電腦的更新代理程式來提供所需的更新、修補程式的狀態，以及已部署修補程式的結果。 若沒有此資訊，更新管理無法正確地報告所需或已安裝的修補程式。

### <a name="resolution"></a>解決方案

嘗試在本機電腦上執行更新。 如果失敗，通常表示更新代理程式發生設定錯誤。

此問題通常是因為網路設定和防火牆問題所造成。 嘗試下列方法：

* 針對 Linux，請查看適當的檔，確定您可以連線到套件存放庫的網路端點。
* 針對 Windows，請檢查[[更新不會從內部網路端點下載（WSUS/SCCM）](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)] 中所列的代理程式設定。
  * 如果電腦已設定為 Windows Update，請確定您可以連線[到與 HTTP/proxy 相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中所述的端點。
  * 如果電腦已設定為 Windows Server Update Services （WSUS），請確定您可以連線到 WUServer 登錄機[碼](/windows/deployment/update/waas-wu-settings)所設定的 WSUS 伺服器。

如果您看到 HRESULT，請按兩下以紅色顯示的例外狀況，以查看整個例外狀況訊息。 請參閱下表中的可能解決方案或建議的動作：

|例外狀況  |解決方式或動作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在[Windows update 錯誤碼清單](https://support.microsoft.com/help/938205/windows-update-error-code-list)中搜尋相關的錯誤碼，以尋找有關例外狀況原因的其他詳細資料。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 這表示網路連線問題。 請確定您的電腦具有更新管理的網路連線能力。 如需必要的埠和地址清單，請參閱[網路規劃](../automation-update-management.md#ports)一節。        |
|`0x8024001E`| 更新作業未完成，因為服務或系統已關閉。|
|`0x8024002E`| Windows Update 服務已停用。|
|`0x8024402C`     | 如果您使用 WSUS 伺服器，請確定 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 登錄機碼下的 `WUServer` 和 `WUStatusServer` 登錄值指定正確的 WSUS 伺服器。        |
|`0x80072EE2`|網路連線問題或與已設定的 WSUS 伺服器交談時發生問題。 檢查 WSUS 設定，並確定可從用戶端存取該服務。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 請確定 Windows Update 服務（wuauserv）正在執行且未停用。        |
|`0x80070005`| 拒絕存取的錯誤可能是由下列任何一項所造成：<br> 受感染的電腦<br> 未正確設定 Windows Update 設定<br> %WinDir%\SoftwareDistribution 資料夾的檔案許可權錯誤<br> 系統磁片磁碟機上的磁碟空間不足（C：）。
|任何其他一般例外狀況     | 在網際網路上執行搜尋以找出可能的解決方案，並與您當地的 IT 支援人員合作。         |

檢查%Windir%\Windowsupdate.log 檔案也可協助您判斷可能的原因。 如需如何讀取記錄檔的詳細資訊，請參閱[如何讀取 windowsupdate.log。](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)

您也可以下載並執行[Windows Update 疑難排解](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)員，以檢查電腦上 Windows Update 是否有任何問題。

> [!NOTE]
> [Windows Update 疑難排解](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)員檔會指出它是用於 windows 用戶端，但它也適用于 windows Server。

## <a name="scenario-update-run-returns-failed-status-linux"></a>案例：更新執行會傳回「失敗」狀態（Linux）

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

如果在作業失敗之前立即看到特定的修補程式、套件或更新，您可以嘗試從下一個更新部署中[排除](../automation-tutorial-update-management.md#schedule-an-update-deployment)它們。 若要從 Windows Update 收集記錄資訊，請參閱[Windows Update 記錄](/windows/deployment/update/windows-update-logs)檔。

如果您無法解決修補問題，請在下一個更新部署開始*之前*，建立下列記錄檔的複本，並加以保留以供疑難排解之用：

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>未安裝修補程式

### <a name="machines-dont-install-updates"></a>電腦不會安裝更新

* 請嘗試直接在機器上執行更新。 如果電腦無法套用更新，請參閱[疑難排解指南中的潛在錯誤清單](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)。
* 如果更新在本機執行，請遵循[從更新管理移除 VM 中](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)的指引，在電腦上移除並重新安裝代理程式。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道有可用的更新，但它們在我的機器上並未顯示為可用

* 這通常會發生在電腦設定為從 WSUS 或 Microsoft Endpoint Configuration Manager 取得更新時，但 WSUS 和 Configuration Manager 尚未核准更新。
* 您可以藉由在本文的「編輯登錄」[一節自動更新中，交叉參照 UseWUServer 登錄機碼](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)，檢查機器是否已針對 WSUS 和 SCCM 進行設定。
* 如果未在 WSUS 中核准更新，則不會進行安裝。 您可以藉由執行下列查詢，在 Log Analytics 中檢查未核准的更新：

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新顯示為已安裝，但我在我的電腦上找不到它們

* 更新常會被其他更新取代。 如需詳細資訊，請參閱 Windows Update 疑難排解指南中的[更新已被取代](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>依分類在 Linux 上安裝更新

* 依分類 (「重大與安全性更新」) 將更新部署至 Linux 時有重要事項須留意，尤其是針對 CentOS。 這些限制記載于[更新管理總覽頁面](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 一直遺失

* KB2267602 是 [Windows Defender 定義更新](https://www.microsoft.com/wdsi/definitions)。 它會每日更新。

## <a name="next-steps"></a>後續步驟

如果您看不到問題或無法解決問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)，這是官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源，來改善客戶體驗：解答、支援及專家。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
