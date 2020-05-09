---
title: Azure 自動化混合式 Runbook 背景工作角色進行疑難排解
description: 本文提供 Azure 自動化混合式 Runbook 背景工作角色進行疑難排解的資訊。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d734c910cc966cfd83f1e1c7f9cbd728643fbc4
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836507"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>混合式 Runbook 背景工作的疑難排解

本文提供有關 Azure 自動化混合式 Runbook 背景工作角色的問題疑難排解的資訊。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的 Azure 自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="general"></a>一般

混合式 Runbook 背景工作角色取決於代理程式，以與您的 Azure 自動化帳戶進行通訊，以註冊背景工作角色、接收 Runbook 作業及報告狀態。 對於 Windows，此代理程式是適用于 Windows 的 Log Analytics 代理程式。 針對 Linux，這是適用于 Linux 的 Log Analytics 代理程式。

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>案例：Runbook 執行失敗

#### <a name="issue"></a>問題

Runbook 執行失敗，您收到下列錯誤訊息：

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

您的 runbook 在嘗試執行三次後很快就會暫停。 有一些條件可以中斷 runbook 完成。 相關的錯誤訊息可能不會包含任何其他資訊。

#### <a name="cause"></a>原因

可能的原因如下：

* Runbook 無法使用本機資源進行驗證。
* 混合式背景工作角色位於 Proxy 或防火牆後方。
* 設定為執行混合式 Runbook 背景工作角色的電腦不符合最低硬體需求。

#### <a name="resolution"></a>解決方案

確認電腦對埠443上的 ***. azure-automation.net**具有輸出存取權。

執行混合式 Runbook 背景工作角色的電腦應符合最低硬體需求，才能將背景工作角色設定為裝載此功能。 Runbook 及其使用的背景進程可能會導致系統過度使用，並導致 runbook 作業延遲或超時。

確認執行「混合式 Runbook 背景工作角色」功能的電腦符合最低硬體需求。 如果滿足最低硬體需求，請監視 CPU 和記憶體使用率，判斷混合式 Runbook 背景工作角色處理序之效能和 Windows 之間是否有任何相互關聯。 任何記憶體或 CPU 壓力都可能表示需要升級資源。 您也可以選取不同的計算資源來支援最低需求，並在工作負載需求指出需要增加時進行調整。

查看**MICROSOFT SMA**事件記錄檔，以取得描述`Win32 Process Exited with code [4294967295]`的對應事件。 此錯誤的原因是您尚未在 runbook 中設定驗證，或指定了混合式 Runbook 背景工作角色群組的執行身分認證。 請參閱在[混合式 runbook 背景工作角色上執行](../automation-hrw-run-runbooks.md)runbook 中的 runbook 許可權，以確認您已正確設定 runbook 的驗證。

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>案例：混合式 Runbook 背景工作角色中的事件15011

#### <a name="issue"></a>問題

混合式 Runbook 背景工作角色收到事件15011，表示查詢結果無效。 當工作者嘗試開啟與[SignalR 伺服器](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)的連線時，會出現下列錯誤。

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>原因

尚未針對自動化部署解決方案正確設定混合式 Runbook 背景工作角色。 此解決方案包含將 VM 連接到 Log Analytics 工作區的元件。 PowerShell 腳本會在訂用帳戶中尋找具有所提供名稱的工作區。 在此情況下，Log Analytics 工作區會在不同的訂用帳戶中。 腳本找不到工作區，並嘗試建立一個，但該名稱已被採用。 因此，部署將會失敗。

#### <a name="resolution"></a>解決方案

您有兩個選項可解決此問題：

* 修改 PowerShell 腳本，以尋找另一個訂用帳戶中的 Log Analytics 工作區。 如果您打算在未來部署許多混合式 Runbook 背景工作電腦，這是很好的解決方案。

* 手動設定背景工作電腦在 Orchestrator 沙箱中執行。 然後，在背景工作角色上執行 Azure 自動化帳戶中建立的 runbook，以測試該功能。

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>案例：自動從混合式背景工作角色群組卸載的 Windows Azure Vm

#### <a name="issue"></a>問題

當背景工作電腦已關閉一段很長的時間時，您看不到混合式 Runbook 背景工作角色或 Vm。

#### <a name="cause"></a>原因

混合式 Runbook 背景工作電腦未在30天內 ping Azure 自動化。 因此，自動化已清除混合式 Runbook 背景工作角色群組或系統背景工作群組。 

#### <a name="resolution"></a>解決方案

啟動背景工作電腦，然後使用 Azure 自動化 rereregister 它。 如需有關如何安裝 runbook 環境並聯機至 Azure 自動化的指示，請參閱[部署 Windows 混合式 runbook 背景工作角色](../automation-windows-hrw-install.md)。

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>案例：在混合式 Runbook 背景工作角色的憑證存放區中找不到憑證

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

當您嘗試在執行身分帳戶憑證不存在的混合式 Runbook 背景工作角色上執行的 runbook 中使用[執行身分帳戶](../manage-runas-account.md)時，就會發生此錯誤。 混合式 Runbook 背景工作角色預設不會有本機的憑證資產。 執行身分帳戶需要此資產才能正常運作。

#### <a name="resolution"></a>解決方案

如果您的混合式 Runbook 背景工作角色是 Azure VM，您可以改[為使用 Runbook 驗證搭配受控](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities)識別。 此案例可讓您使用 Azure VM 的受控識別（而非執行身分帳戶）來向 Azure 資源進行驗證，藉此簡化驗證。 當混合式 Runbook 背景工作角色是內部部署電腦時，您必須在電腦上安裝執行身分帳戶憑證。 若要瞭解如何安裝憑證，請參閱在[混合式 runbook 背景工作角色上執行 runbook](../automation-hrw-run-runbooks.md)中的執行 PowerShell runbook**匯出-export-runascertificatetohybridworker**的步驟。

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>案例：在混合式 Runbook 背景工作角色註冊期間發生錯誤403

#### <a name="issue"></a>問題

背景工作的初始註冊階段失敗，您會收到下列錯誤（403）：

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

下列問題是可能的原因：

* 代理程式的設定中有輸入錯誤的工作區識別碼或工作區金鑰（主要）。 
* 混合式 Runbook 背景工作角色無法下載設定，這會導致帳戶連結錯誤。 當 Azure 啟用解決方案時，它只支援特定區域來連結 Log Analytics 工作區和自動化帳戶。 電腦上也可能設定了不正確的日期或時間。 如果時間是從目前時間加上或減去15分鐘，上架會失敗。

#### <a name="resolution"></a>解決方案

##### <a name="mistyped-workspace-id-or-key"></a>輸入錯誤的工作區識別碼或金鑰
若要確認代理程式的工作區識別碼或工作區金鑰是否輸入錯誤，請參閱[新增或移除工作區-](../../azure-monitor/platform/agent-manage.md#windows-agent) windows 代理程式適用的 windows 代理程式，或[新增或移除](../../azure-monitor/platform/agent-manage.md#linux-agent)Linux 代理程式的 linux 代理程式。 請務必從 Azure 入口網站中選取完整的字串，並小心複製並貼上它。

##### <a name="configuration-not-downloaded"></a>設定未下載

您的 Log Analytics 工作區和自動化帳戶必須位於已連結的區域。 如需支援的區域清單，請參閱[Azure 自動化和 Log Analytics 工作區](../how-to/region-mappings.md)對應。

您可能也需要更新電腦的日期或時區。 如果您選取自訂的時間範圍，請確定範圍是以 UTC 為准，這可能會與您的當地時區不同。

## <a name="linux"></a>Linux

Linux 混合式 Runbook 背景工作角色取決於[適用于 linux 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)，來與您的自動化帳戶進行通訊，以註冊背景工作角色、接收 Runbook 作業及報告狀態。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因。

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>案例： Linux 混合式 Runbook 背景工作角色會在簽署 Runbook 時收到密碼提示

#### <a name="issue"></a>問題

針對 Linux `sudo`混合式 Runbook 背景工作角色執行命令，會抓取未預期的密碼提示。

#### <a name="cause"></a>原因

**Sudoers**檔案中未正確設定適用于 Linux 的 Log Analytics 代理程式的**nxautomationuser**帳戶。 混合式 Runbook 背景工作角色需要適當的帳戶許可權和其他資料設定，才能在 Linux Runbook Worker 上簽署 runbook。

#### <a name="resolution"></a>解決方案

* 請確定混合式 Runbook 背景工作角色在機器上具有 GnuPG （GPG）可執行檔。

* 確認**sudoers**檔案中的**nxautomationuser**帳戶設定。 請參閱[在混合式 Runbook 背景工作角色上執行 runbook](../automation-hrw-run-runbooks.md)。

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>案例：適用于 Linux 的 Log Analytics 代理程式未執行

#### <a name="issue"></a>問題

適用于 Linux 的 Log Analytics 代理程式未執行。

#### <a name="cause"></a>原因

如果代理程式未執行，它會讓 Linux 混合式 Runbook 背景工作角色無法與 Azure 自動化進行通訊。 代理程式可能因各種原因而無法執行。

#### <a name="resolution"></a>解決方案

 輸入命令`ps -ef | grep python`以確認代理程式正在執行。 您應該會看到如下所示的輸出。 使用**nxautomation**使用者帳戶的 Python 處理常式。 如果未啟用更新管理或 Azure 自動化方案，則下列進程都不會執行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

下列清單顯示已針對 Linux 混合式 Runbook 背景工作角色啟動的處理序。 它們全都位於/var/opt/microsoft/omsagent/state/automationworker/目錄中。

* **oms**：背景工作角色管理員進程。 它會直接從 DSC 啟動。
* **worker. 會議**：自動註冊的混合式背景工作進程。 它是由背景工作角色管理員啟動。 更新管理會使用此處理序，且使用者不會注意到此處理序。 如果機器上未啟用更新管理解決方案，就不會出現此處理序。
* **diy/worker. 會議**： diy 混合式背景工作進程。 DIY 混合式背景工作處理序可用來在混合式 Runbook 背景工作角色上執行使用者 Runbook。 它與自動註冊的混合式背景工作進程不同，因為它使用不同的設定。 如果已停用 Azure 自動化解決方案，而且未註冊 DIY Linux 混合式背景工作角色，則不會出現此進程。

如果代理程式未執行，請執行下列命令來啟動服務： `sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>案例：指定的類別不存在

如果您在 **/var/opt/microsoft/omsconfig/omsconfig.log**中看到`The specified class does not exist..`錯誤訊息，則需要更新適用于 Linux 的 log Analytics 代理程式。 執行下列命令來重新安裝代理程式。

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合式 Runbook 背景工作角色取決於[適用于 Windows 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)，來與您的自動化帳戶進行通訊，以註冊背景工作角色、接收 Runbook 作業及報告狀態。 如果註冊背景工作角色失敗，本節會包含一些可能的原因。

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>案例：適用于 Windows 的 Log Analytics 代理程式未執行

#### <a name="issue"></a>問題

`healthservice`不是在混合式 Runbook 背景工作角色機器上執行。

#### <a name="cause"></a>原因

如果 Windows 服務的 Log Analytics 並未執行，混合式 Runbook 背景工作角色就無法與 Azure 自動化通訊。

#### <a name="resolution"></a>解決方案

在 PowerShell 中輸入下列命令，確認代理程式正在執行中： `Get-Service healthservice`。 如果服務已停止，在 PowerShell 中輸入下列命令可啟動服務：`Start-Service healthservice`。

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>案例： Operations Manager 記錄檔中的事件4502

#### <a name="issue"></a>問題

在 [**應用程式和服務 Logs\Operations 管理員**] 事件記錄檔中，您會看到事件4502， `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`以及包含下列描述的事件訊息：<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>原因

此問題是因 Proxy 或網路防火牆封鎖 Microsoft Azure 的通訊所引起。 確認電腦對埠443上的 ***. azure-automation.net**具有輸出存取權。

#### <a name="resolution"></a>解決方案

記錄儲存每一個混合式背景工作角色本機的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 您可以確認 [**應用程式和服務 Logs\Microsoft-SMA\Operations** ] 和 [**應用程式和服務 Logs\Operations 管理員**] 事件記錄檔中是否有任何警告或錯誤事件。 這些記錄表示連線能力或其他類型的問題，其會影響要 Azure 自動化的角色上線，或在正常作業下發生的問題。 如需 Log Analytics 代理程式問題的其他協助疑難排解，請參閱針對[Log Analytics Windows 代理程式的問題進行疑難排解](../../azure-monitor/platform/agent-windows-troubleshoot.md)。

混合式背景工作角色會將[runbook 輸出和訊息](../automation-runbook-output-and-messages.md)傳送至 Azure 自動化，方法與在雲端中執行的 runbook 作業一樣，會傳送輸出和訊息。 您可以啟用 [詳細資訊] 和 [進度] 資料流程，就像 runbook 一樣。

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>案例： Orchestrator。 msbuild.exe 無法透過 proxy 連接到 Office 365

#### <a name="issue"></a>問題

在 Windows 混合式 Runbook 背景工作角色上執行的腳本無法如預期般連接到 Orchestrator 沙箱上的 Office 365。 此腳本會使用連接[connect-msolservice](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)來進行連線。 

如果您調整**Orchestrator. config.xml**來設定 proxy 和略過清單，沙箱仍然無法正確連接。 具有相同 proxy 和略過清單設定的**Powershell_ise .exe .config**檔案似乎會如您預期般運作。 Service Management Automation （SMA）記錄檔和 PowerShell 記錄檔不會提供關於 proxy 的任何資訊。

#### <a name="cause"></a>原因

伺服器上 Active Directory 同盟服務（AD FS）的連接無法略過 proxy。 請記住，PowerShell 沙箱會以已記錄的使用者身分執行。 不過，Orchestrator 沙箱的自訂程度很高，而且可能會忽略**orchestrator**檔案的設定。 它有特殊程式碼可處理機器或 Log Analytics 代理程式 proxy 設定，而不是處理其他自訂 proxy 設定。 

#### <a name="resolution"></a>解決方案

您可以藉由將您的腳本遷移到使用 Azure Active Directory 模組，而不是 PowerShell Cmdlet 的 MSOnline 模組，來解決 Orchestrator 沙箱的問題。 如需詳細資訊，請參閱[從 Orchestrator 遷移至 Azure 自動化（搶鮮版（Beta））](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration)。

如果您想要繼續使用 MSOnline 模組 Cmdlet，請將您的腳本變更為使用[Invoke 命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。 指定和`Credential`參數的`ComputerName`值。 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

這段程式碼變更會在指定認證的內容底下啟動全新的 PowerShell 會話。 它應該會讓流量流經驗證作用中使用者的 proxy 伺服器。

>[!NOTE]
>此解決方案不需要操作沙箱設定檔。 即使您成功使設定檔與腳本搭配運作，每次更新混合式 Runbook 背景工作角色代理程式時，都會抹除該檔案。

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>案例：混合式 Runbook 背景工作角色未報告

#### <a name="issue"></a>問題

您的混合式 Runbook 背景工作角色機器正在執行，但您在工作區中看不到機器的心跳資料。

下列範例查詢會顯示工作區中的機器及其上次活動訊號：

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

這個問題可能是因為混合式 Runbook 背景工作角色上的快取損毀所造成的。

#### <a name="resolution"></a>解決方案

若要解決此問題，請登入混合式 Runbook 背景工作角色，並執行下列程式碼。 此腳本會停止適用于 Windows 的 Log Analytics 代理程式、移除其快取，然後重新開機服務。 此動作會強制混合式 Runbook 背景工作角色重新從「Azure 自動化」下載其設定。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>案例：您無法新增混合式 Runbook 背景工作角色

#### <a name="issue"></a>問題

當您嘗試使用`Add-HybridRunbookWorker` Cmdlet 新增混合式 Runbook 背景工作角色時，您會收到下列訊息：

```error
Machine is already registered
```

#### <a name="cause"></a>原因

如果電腦已向不同的自動化帳戶註冊，或您嘗試在將混合式 Runbook 背景工作角色從電腦移除之後重新加入，則可能會造成此問題。

#### <a name="resolution"></a>解決方案

若要解決此問題，請移除下列登錄機碼`HealthService`、重新開機， `Add-HybridRunbookWorker`然後再試一次 Cmdlet。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶聯繫，以改善客戶體驗。 Azure 支援將 Azure 社區連接到解答、支援及專家。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。
