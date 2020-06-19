---
title: 針對 Azure 自動化混合式 Runbook 背景工作角色問題進行疑難排解
description: 本文說明如何針對 Azure 自動化混合式 Runbook 背景工作角色進行疑難排解並解決發生的問題。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 28b6b09c679e37ca4ecd901371e65bffb27ecba4
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680998"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>針對混合式 Runbook 背景工作角色問題進行疑難排解

本文提供針對 Azure 自動化混合式 Runbook 背景工作角色進行疑難排解並解決問題的相關資訊。 如需一般資訊，請參閱[混合式 Runbook 背景工作角色概觀](../automation-hybrid-runbook-worker.md)。

## <a name="general"></a>一般

混合式 Runbook 背景工作角色取決於與 Azure 自動化帳戶通訊的代理程式，以註冊背景工作角色、接收 Runbook 作業及報告狀態。 針對 Windows，此代理程式為適用於 Windows 的 Log Analytics 代理程式。 針對 Linux，此代理程式為適用於 Linux 的 Log Analytics 代理程式。

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>案例：Runbook 執行失敗

#### <a name="issue"></a>問題

Runbook 執行失敗且收到下列錯誤訊息：

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook 在嘗試執行三次後會立即暫停。 在某些情況下，Runbook 會因為受到干擾而無法完成。 相關的錯誤訊息可能不會包含任何其他資訊。

#### <a name="cause"></a>原因

可能原因如下：

* Runbook 無法使用本機資源驗證。
* 混合式背景工作角色位於 Proxy 或防火牆後方。
* 設定用於執行混合式 Runbook 背景工作角色的電腦未滿足最低硬體需求。

#### <a name="resolution"></a>解決方案

確認電腦可透過連接埠 443 輸出存取 * **.azure-automation.net**。

執行混合式 Runbook 背景工作角色的電腦應滿足最低硬體需求，您才能設定背景工作角色來裝載這項功能。 Runbook 及其所使用的背景處理序可能會導致系統超用，造成 Runbook 作業延遲或逾時。

確認執行混合式 Runbook 背景工作角色功能的電腦滿足最低硬體需求。 如果滿足最低硬體需求，請監視 CPU 和記憶體使用率，判斷混合式 Runbook 背景工作角色處理序之效能和 Windows 之間是否有任何相互關聯。 任何記憶體或 CPU 壓力皆可能表示資源需要升級。 您也可選擇其他支援最低需求，且能在工作負載需求指出需要增加資源時擴充的計算資源。

查看 **Microsoft-SMA** 事件記錄檔，找出描述為 `Win32 Process Exited with code [4294967295]` 的對應事件。 此錯誤的原因是您尚未在 Runbook 中設定驗證，或尚未指定混合式 Runbook 背景工作角色群組的執行身分認證。 請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](../automation-hrw-run-runbooks.md)中的 Runbook 權限，確認已為 Runbook 設定正確的驗證。

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>案例：混合式 Runbook 背景工作角色中的事件 15011

#### <a name="issue"></a>問題

混合式 Runbook 背景工作角色收到事件 15011，表示查詢結果無效。 當背景工作角色嘗試開啟與 [SignalR 伺服器](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)的連線時，會出現下列錯誤。

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>原因

尚未針對自動化功能部署正確設定混合式 Runbook 背景工作角色，例如更新管理。 此部署包含將 VM 連線到 Log Analytics 工作區的步驟。 PowerShell 指令碼會在訂用帳戶中尋找包含提供名稱的工作區。 在此情況下，Log Analytics 工作區則位於不同的訂用帳戶中。 指令碼找不到工作區並嘗試建立一個，但該名稱已被採用。 最終導致部署失敗。

#### <a name="resolution"></a>解決方案

您有兩種方式可解決此問題︰

* 修改 PowerShell 指令碼，以尋找另一個訂用帳戶中的 Log Analytics 工作區。 如果打算日後部署許多混合式 Runbook 背景工作角色電腦，則這會是很好的解決方案。

* 手動設定背景工作角色電腦，使其在 Orchestrator 沙箱中執行。 然後，在該背景工作角色上執行在 Azure 自動化帳戶中建立的 Runbook，以測試其功能。

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>案例：Windows Azure VM 自動從混合式背景工作角色群組卸除

#### <a name="issue"></a>問題

當背景工作角色電腦已關閉一段很長的時間時，您將無法看到混合式 Runbook 背景工作角色或 VM。

#### <a name="cause"></a>原因

混合式 Runbook 背景工作角色電腦超過 30 天未 Ping Azure 自動化。 因此，自動化已清除混合式 Runbook 背景工作角色群組或系統背景工作角色群組。 

#### <a name="resolution"></a>解決方案

啟動背景工作角色電腦，然後向 Azure 自動化重新註冊。 如需有關安裝 Runbook 環境並連線至 Azure 自動化的指示，請參閱[部署 Windows 混合式 Runbook 背景工作角色](../automation-windows-hrw-install.md)。

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>案例：在混合式 Runbook 背景工作角色的憑證存放區中找不到任何憑證

#### <a name="issue"></a>問題

混合式 Runbook 背景工作角色上執行的 Runbook 失敗並出現下列錯誤訊息：

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>原因

當在混合式 Runbook 背景工作角色上執行的 Runbook 中嘗試使用[執行身分帳戶](../manage-runas-account.md)時，若沒有執行身分帳戶憑證，就會發生此錯誤。 混合式 Runbook 背景工作角色預設不會有本機的憑證資產。 執行身分帳戶需要此資產才能正常運作。

#### <a name="resolution"></a>解決方案

如果混合式 Runbook 背景工作角色是 Azure VM，則可改用[使用 Runbook 驗證搭配受控識別](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities)。 此案例可供使用 Azure VM 的受控識別來向 Azure 資源進行驗證，而非使用執行身分帳戶，藉此簡化驗證。 當混合式 Runbook 背景工作角色是內部部署電腦時，您必須在該電腦上安裝執行身分帳戶憑證。 若要了解如何安裝憑證，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](../automation-hrw-run-runbooks.md)中的步驟，以執行 PowerShell Runbook **Export-RunAsCertificateToHybridWorker**。

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>案例：在混合式 Runbook 背景工作角色註冊期間發生錯誤 403

#### <a name="issue"></a>問題

背景工作的初始註冊階段失敗，且您收到下列錯誤 (403)：

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

下列問題是可能的原因：

* 代理程式設定中有輸入錯誤的工作區識別碼或工作區金鑰 (主要)。 
* 混合式 Runbook 背景工作角色無法下載設定，導致帳戶連結錯誤。 當 Azure 在電腦上啟用功能時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 電腦上也可能設定了不正確的日期或時間。 如果時間為目前時間的 +/-15 分鐘，則會導致功能部署失敗。

#### <a name="resolution"></a>解決方案

##### <a name="mistyped-workspace-id-or-key"></a>輸入錯誤的工作區識別碼或金鑰
若要確認代理程式的工作區識別碼或工作區金鑰是否輸入錯誤，針對 Windows 代理程式，請參閱[新增或移除工作區 – Windows 代理程式](../../azure-monitor/platform/agent-manage.md#windows-agent)針對 Linux 代理程式，請參閱[新增或移除工作區 – Linux 代理程式](../../azure-monitor/platform/agent-manage.md#linux-agent)。 請務必從 Azure 入口網站中選取完整的字串，將其小心地複製並貼上。

##### <a name="configuration-not-downloaded"></a>設定未下載

Log Analytics 工作區和自動化帳戶必須位於已連結的區域。 如需支援的區域清單，請參閱 [Azure 自動化和 Log Analytics 工作區對應](../how-to/region-mappings.md)。

您可能也需要更新電腦的日期或時區。 如果選取自訂的時間範圍，請確定該範圍是以 UTC 為准，這可能會與當地時區不同。

## <a name="linux"></a>Linux

Linux 混合式 Runbook 背景工作角色仰賴[適用於 Linux 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)來與自動化帳戶通訊，以便註冊背景工作角色、接收 Runbook 作業和報告狀態。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因。

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>案例：Linux 混合式 Runbook 背景工作角色會在簽署 Runbook 時收到輸入密碼的提示

#### <a name="issue"></a>問題

執行 Linux 混合式 Runbook 背景工作角色的 `sudo` 命令時，會收到非預期的輸入密碼提示。

#### <a name="cause"></a>原因

適用於 Linux 的 Log Analytics 代理程式 **nxautomationuser** 帳戶未在 **sudoers** 檔案中正確設定。 混合式 Runbook 背景工作角色需要適當的帳戶權限及其他資料設定，才能在 Linux Runbook 背景工作角色上簽署 Runbook。

#### <a name="resolution"></a>解決方案

* 確定混合式 Runbook 背景工作角色在電腦上具有 GnuPG (GPG) 可執行檔。

* 驗證 **sudoers** 檔案中 **nxautomationuser** 帳戶的設定。 參閱[在混合式 Runbook 背景工作角色上執行 Runbook](../automation-hrw-run-runbooks.md)。

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>案例：適用於 Linux 的 Log Analytics 代理程式未執行

#### <a name="issue"></a>問題

適用於 Linux 的 Log Analytics 代理程式未執行。

#### <a name="cause"></a>原因

如果代理程式未執行，就會讓 Linux 混合式 Runbook 背景工作角色無法與 Azure 自動化進行通訊。 代理程式無法執行的原因有很多種。

#### <a name="resolution"></a>解決方案

 輸入 `ps -ef | grep python` 命令，確認代理程式正在執行。 您應該會看到如下所示的輸出。 具有 **nxautomation** 使用者帳戶的 Python 處理序。 如果未啟用 Azure 自動化功能，則不會執行下列任何處理序。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

下列清單顯示已針對 Linux 混合式 Runbook 背景工作角色啟動的處理序。 這些處理序全都位於 /var/opt/microsoft/omsagent/state/automationworker/ 目錄中。

* **oms.conf**：這是背景工作管理員處理序。 它會直接從 DSC 啟動。
* **worker.conf**：這是自動註冊的混合式背景工作角色處理序。 它是由背景工作角色管理員啟動。 更新管理會使用此處理序，且使用者不會注意到此處理序。 如果電腦上未啟用更新管理，就不會出現此處理序。
* **diy/worker.conf**：DIY 混合式背景工作處理序。 DIY 混合式背景工作處理序可用來在混合式 Runbook 背景工作角色上執行使用者 Runbook。 此處理序與自動註冊的混合式背景工作角色處理序其唯一差別在於會使用不同設定。 如果已停用 Azure 自動化且未註冊 DIY Linux 混合式背景工作角色，就不會出現此處理序。

如果代理程式未執行，請執行下列命令來啟動服務：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>案例：指定的類別不存在

如果在 **/var/opt/microsoft/omsconfig/omsconfig.log** 中看到錯誤訊息 `The specified class does not exist..`，則需要更新適用於 Linux 的 Log Analytics 代理程式。 請執行下列命令來重新安裝代理程式。

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合式 Runbook 背景工作角色仰賴[適用於 Windows 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)來與自動化帳戶通訊以註冊背景工作角色、接收 Runbook 作業和報告狀態。 如果註冊背景工作角色失敗，此區段會包含部分可能的原因。

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>案例：適用於 Windows 的 Log Analytics 代理程式未執行

#### <a name="issue"></a>問題

`healthservice` 未在混合式 Runbook 背景工作角色電腦上執行。

#### <a name="cause"></a>原因

如果適用於 Windows 服務的 Log Analytics 代理程式未執行，則混合式 Runbook 背景工作角色即無法與 Azure 自動化進行通訊。

#### <a name="resolution"></a>解決方案

在 PowerShell 中輸入下列命令，確認代理程式正在執行：`Get-Service healthservice`。 如果服務已停止，在 PowerShell 中輸入下列命令可啟動服務：`Start-Service healthservice`。

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>案例：Operations Manager 記錄中的事件 4502

#### <a name="issue"></a>問題

在**應用程式和服務記錄\Operations Manager** 事件記錄檔中，您會看到事件 4502，以及包含下列描述的 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` 事件訊息：<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>原因

此問題是因 Proxy 或網路防火牆封鎖 Microsoft Azure 的通訊所引起。 確認電腦可透過連接埠 443 輸出存取 * **.azure-automation.net**。

#### <a name="resolution"></a>解決方案

記錄儲存每一個混合式背景工作角色本機的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 您可在**應用程式和服務記錄\Microsoft-SMA\Operations** 與**應用程式和服務記錄\Operations Manager** 事件記錄檔確認是否有任何警告或錯誤事件。 這些記錄檔表示連線能力或其他類型的問題會影響為 Azure 自動化啟用角色，或在正常作業下發生的問題。 如需針對 Log Analytics 代理程式問題進行疑難排解的其他協助，請參閱[針對 Log Analytics Windows 代理程式的問題進行疑難排解](../../azure-monitor/platform/agent-windows-troubleshoot.md)。

混合式背景工作角色會將 [Runbook 輸出和訊息](../automation-runbook-output-and-messages.md)傳送至 Azure 自動化，其方式與在雲端中執行 Runbook 作業傳送輸出和訊息的方式相同。 您可透過如同在 Runbook 中所執行的步驟來啟用 [詳細資訊] 和 [進度] 資料流。

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>案例：Orchestrator.Sandbox.exe 無法透過 Proxy 連線到 Office 365

#### <a name="issue"></a>問題

在 Windows 混合式 Runbook 背景工作角色上執行的指令碼無法如預期般連線到 Orchestrator 沙箱上的 Office 365。 此指令碼會使用 [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) 來進行連線。 

如果調整 **Orchestrator.Sandbox.exe.config** 來設定 Proxy 和略過清單，則仍然無法正確連線到沙箱。 具有相同 Proxy 和略過清單設定的 **Powershell_ise.exe.config** 檔案或許能提供協助。 Service Management Automation (SMA) 記錄檔和 PowerShell 記錄檔不會提供關於 Proxy 的任何資訊。

#### <a name="cause"></a>原因

伺服器上 Active Directory 同盟服務 (AD FS) 的連線無法略過 Proxy。 請記住，PowerShell 沙箱會以已記錄的使用者身分執行。 不過，Orchestrator 沙箱的自訂程度很高，且可能忽略 **Orchestrator.Sandbox.exe.config** 檔案設定。 其具有特殊程式碼可處理電腦或 Log Analytics 代理程式 Proxy 設定，但不適用於處理其他自訂 Proxy 設定。 

#### <a name="resolution"></a>解決方案

您可藉由移轉指令碼，以使用 Azure Active Directory 模組，而非 PowerShell Cmdlet 的 MSOnline 模組來解決 Orchestrator 沙箱問題。 如需詳細資訊，請參閱[從 Orchestrator 移轉至 Azure 自動化 (搶鮮版 (Beta))](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration)。

如果想要繼續使用 MSOnline 模組 Cmdlet，請將指令碼變更為使用 [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。 指定 `ComputerName` 和 `Credential` 參數的值。 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

這段程式碼變更會在指定認證的基礎上開啟全新 PowerShell 工作階段， 可讓流量經過對作用中使用者進行身分驗證的 Proxy 伺服器。

>[!NOTE]
>這種解決方式即無需要操作沙箱設定檔。 即使成功使該設定檔與指令碼搭配運作，但每次更新混合式 Runbook 背景工作角色代理程式時，仍會抹除該檔案。

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>案例：混合式 Runbook 背景工作角色未回報

#### <a name="issue"></a>問題

混合式 Runbook 背景工作角色電腦正在執行中，但您看不到工作區中電腦的活動訊號資料。

下列範例查詢會顯示工作區中的機器及其上次活動訊號：

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

這個問題可能是因為混合式 Runbook 背景工作角色上的快取損毀所造成的。

#### <a name="resolution"></a>解決方案

若要解決此問題，請登入混合式 Runbook 背景工作角色，並執行下列程式碼。 此指令碼會停止適用於 Windows 的 Log Analytics 代理程式、移除其快取，並重新啟動服務。 此動作會強制混合式 Runbook 背景工作角色重新從「Azure 自動化」下載其設定。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>案例：您無法新增混合式 Runbook 背景工作角色

#### <a name="issue"></a>問題

您在嘗試使用 `Add-HybridRunbookWorker` Cmdlet 來新增混合式 Runbook 背景工作角色時收到下列訊息：

```error
Machine is already registered
```

#### <a name="cause"></a>原因

此問題可能是因為電腦已經向不同的自動化帳戶註冊，或在將混合式 Runbook 背景工作角色從電腦中移除之後，嘗試重新新增此背景工作角色。

#### <a name="resolution"></a>解決方案

若要解決此問題，請移除下列登錄機碼、重新啟動 `HealthService`，然後重新嘗試 `Add-HybridRunbookWorker` Cmdlet。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>後續步驟

如果在這裡沒有看到問題或無法解決問題，請嘗試下列其中一個管道以取得其他支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 連線至 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援會將 Azure 社群連線到解答、支援及專家。
* 提出 Azure 支援事件。 前往 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
