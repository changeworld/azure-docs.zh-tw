---
title: 容毀毀 Azure 自動化混合 Runbook 工作
description: 本文提供有關對 Azure 自動化混合 Runbook 工作項進行故障排除的資訊。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679324"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>混合式 Runbook 背景工作的疑難排解

本文提供針對混合式 Runbook 背景工作角色問題進行疑難排解的資訊。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="general"></a>一般

混合式 Runbook 背景工作角色取決於與自動化帳戶通訊的代理程式，來註冊背景工作角色、接收 Runbook 作業及報告狀態。 對於 Windows,此代理是 Windows 的日誌分析代理。 對於 Linux,它是 Linux 的日誌分析代理。

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>案例：Runbook 執行失敗

#### <a name="issue"></a>問題

Runbook 執行失敗,您會收到以下錯誤。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

您的 Runbook 在嘗試執行三次後不久即掛起。 在某些情況下,可能會中斷 Runbook 的完成。 相關的錯誤消息可能不包含任何其他資訊。

#### <a name="cause"></a>原因

可能的原因如下：

* Runbook 無法使用本地資源進行身份驗證。
* 混合式背景工作角色位於 Proxy 或防火牆後方。
* 配置為運行混合 Runbook 輔助角色的計算機不符合最低硬體要求。

#### <a name="resolution"></a>解決方案

驗證電腦在埠 443 上具有對 ***.azure-automation.net**的出站訪問許可權。

運行混合 Runbook 輔助角色的計算機應在將輔助角色配置為承載此功能之前滿足最低硬體要求。 Runbook 及其使用的後台進程可能會導致系統過度使用,並導致 Runbook 作業延遲或超時。

確認運行混合 Runbook 輔助功能的計算機滿足最低硬體要求。 如果滿足最低硬體需求，請監視 CPU 和記憶體使用率，判斷混合式 Runbook 背景工作角色處理序之效能和 Windows 之間是否有任何相互關聯。 任何記憶體或 CPU 壓力都可能表示需要升級資源。 您還可以選擇支援最低要求和擴展的不同計算資源,當工作負載需求指示需要增加時。

檢查**Microsoft-SMA**事件日誌中`Win32 Process Exited with code [4294967295]`有關說明 的相應事件。 此錯誤的原因是,您尚未在 Runbook 中配置身份驗證,也沒有為混合 Runbook 工作組指定「運行為」憑據。 查看[混合 Runbook 輔助角色執行簿中的 Runbook](../automation-hrw-run-runbooks.md)許可權,以確認已正確配置運行簿的身份驗證。

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>方案:混合 Runbook 輔助角色中的事件 15011

#### <a name="issue"></a>問題

混合 Runbook 工作線程接收事件 15011,指示查詢結果無效。 當工作人員嘗試打開與[SignalR 伺服器](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)的連接時,將顯示以下錯誤。

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>原因

尚未為自動部署解決方案正確配置混合 Runbook 輔助角色。 此解決方案包含將 VM 連接到日誌分析工作區的部件。 PowerShell 文本使用提供的名稱查找訂閱中的工作區。 在這種情況下,日誌分析工作區位於不同的訂閱中。 腳本找不到工作區並嘗試創建工作區,但名稱已被獲取。 因此,部署失敗。

#### <a name="resolution"></a>解決方案

有兩個選項可以解決此問題:

* 修改 PowerShell 文稿以查找另一個訂閱中的日誌分析工作區。 如果您計劃將來部署許多混合 Runbook 輔助電腦,則這是一個很好的解決方案。

* 手動將輔助計算機配置為在協調器沙箱中運行。 然後運行在輔助角色上的 Azure 自動化帳戶中創建的 Runbook 以測試功能。

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>方案:Windows Azure VM 自動從混合輔助角色群組刪除

#### <a name="issue"></a>問題

當工作機已關閉很長時間時,您看不到混合 Runbook 工作項或 VM。

#### <a name="cause"></a>原因

混合 Runbook 工作機已 30 天以上未 ping Azure 自動化。 因此,自動化已清除混合 Runbook 工作群體或系統輔助角色組。 

#### <a name="resolution"></a>解決方案

啟動輔助電腦,然後將其重新註冊到 Azure 自動化。 請參閱有關在[部署 Windows 混合 Runbook 輔助角色](../automation-windows-hrw-install.md)中安裝 Runbook 環境和連接到 Azure 自動化的說明。

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>方案:在混合 Runbook 輔助功能的憑證儲存中找不到憑證

#### <a name="issue"></a>問題

在混合 Runbook 工作線程上運行的 Runbook 失敗,出現以下錯誤消息。

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>原因

當您嘗試在執行簿中使用[執行為帳戶](../manage-runas-account.md)時,將發生此錯誤,該執行簿運行在混合 Runbook 工作線程上,其中不存在"運行為帳戶"證書。 默認情況下,混合 Runbook 工作團隊在本地沒有證書資產。 "運行為帳戶"要求此資產正常運行。

#### <a name="resolution"></a>解決方案

如果混合 Runbook 工作線程是 Azure VM,則可以對[Azure 資源使用託管識別](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。 此方案允許使用 Azure VM 的託管標識而不是「運行為」帳戶對 Azure 資源進行身份驗證,從而簡化身份驗證。 當混合 Runbook 工作線程是本地電腦時,您需要在電腦上安裝「執行為帳戶」證書。 要瞭解如何安裝憑證,請參閱在[混合 Runbook 工作線程執行 Runbook](../automation-hrw-run-runbooks.md)執行簿中執行 PowerShell Runbook 執行簿**匯出執行憑證到混合工作線程的步驟**。

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>方案:混合 Runbook 工作線程註冊期間的錯誤 403

#### <a name="issue"></a>問題

工作人員的初始註冊階段失敗,您會收到以下錯誤 (403)。

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

可能的原因如下：

* 代理的設定中鍵入的工作區 ID 或工作區金鑰(主鍵)類型錯誤。 

* 混合 Runbook 輔助角色無法下載配置,從而導致帳戶連結錯誤。 當 Azure 啟用解決方案時,它僅支援某些區域來連結日誌分析工作區和自動化帳戶。 計算機上也可能設置了不正確的日期和/或時間。 如果時間與當前時間的時間為 +/-15 分鐘,則載入失敗。

#### <a name="resolution"></a>解決方案

##### <a name="mistyped-workspace-idkey"></a>鍵入錯誤的工作區 ID/鍵
要驗證代理的工作區 ID 或工作區密鑰是否鍵入錯誤,請參閱[添加或刪除工作區 —](../../azure-monitor/platform/agent-manage.md#windows-agent) Windows 代理的 Windows 代理或[添加或刪除工作區 -](../../azure-monitor/platform/agent-manage.md#linux-agent) Linux 代理的 Linux 代理。  請確保從 Azure 門戶中選擇完整字串,並仔細複製並粘貼它。

##### <a name="configuration-not-downloaded"></a>未下載設定

日誌分析工作區和自動化帳戶必須位於連結區域。 有關受支援區域的清單,請參閱[Azure 自動化和紀錄分析工作區映射](../how-to/region-mappings.md)。

您可能還需要更新計算機的日期和/或時區。 如果選擇自定義時間範圍,請確保該範圍為 UTC,這可能與您的本地時區不同。

## <a name="linux"></a>Linux

Linux 混合 Runbook 工作線程依賴於[Linux 的日誌分析代理](../../azure-monitor/platform/log-analytics-agent.md)與您的自動化帳戶通信,以註冊輔助角色、接收 Runbook 作業和報告狀態。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因：

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>專案:Linux 的紀錄分析代理未執行

#### <a name="issue"></a>問題

Linux 的紀錄分析代理未執行

#### <a name="cause"></a>原因

如果代理未運行,則阻止 Linux 混合 Runbook 工作線程與 Azure 自動化通訊。 由於各種原因,代理可能未運行。

#### <a name="resolution"></a>解決方案

 通過輸入命令`ps -ef | grep python`驗證代理正在運行。 您應該會看到類似於以下內容的輸出,即使用**nx 自動化**使用者帳戶的 Python 進程。 如果未啟用更新管理或 Azure 自動化解決方案,則以下進程均未運行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

下列清單顯示已針對 Linux 混合式 Runbook 背景工作角色啟動的處理序。 它們都位於 **/var/opt/microsoft/omsagent/狀態/自動化工作者/** 目錄中。

* **oms.conf** - 工作人員管理員流程。 它會直接從 DSC 啟動。

* **worker.conf** - 自動註冊的混合工作進程。 它是由背景工作角色管理員啟動。 更新管理會使用此處理序，且使用者不會注意到此處理序。 如果機器上未啟用更新管理解決方案，就不會出現此處理序。

* **diy/worker.conf** - DIY 混合工人流程。 DIY 混合式背景工作處理序可用來在混合式 Runbook 背景工作角色上執行使用者 Runbook。 它僅在使用不同配置的關鍵詳細資訊中與自動註冊混合工作進程不同。 如果 Azure 自動化解決方案已禁用且未註冊 DIY Linux 混合輔助角色,則此過程不存在。

若代理未執行,則執行以下指令以啟動服務: `sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>配置:指定的類別不存在

如果您在`The specified class does not exist..`**/var/opt/microsoft/omsconfig/omsconfig.log**中看到錯誤,則需要更新 Linux 的日誌分析代理。 執行以下指令以重新安裝代理程式:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合 Runbook 工作線程依賴於[Windows 的日誌分析代理](../../azure-monitor/platform/log-analytics-agent.md)與您的自動化帳戶通信以註冊輔助角色、接收 Runbook 作業和報告狀態。 如果工作人員的註冊失敗,本節包括一些可能的原因。

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>方案:Windows 的紀錄分析代理未執行

#### <a name="issue"></a>問題

`healthservice`未在混合 Runbook 工作電腦上運行。

#### <a name="cause"></a>原因

如果 Windows 服務的日誌分析未運行,則混合 Runbook 輔助角色無法與 Azure 自動化通訊。

#### <a name="resolution"></a>解決方案

通過在 PowerShell 中輸入以下`Get-Service healthservice`命令來 驗證代理是否正在運行。 如果服務已停止，在 PowerShell 中輸入下列命令可啟動服務：`Start-Service healthservice`。

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>配置:操作管理員紀錄中的事件 4502

#### <a name="issue"></a>問題

在**應用程式與服務紀錄_操作管理員**事件紀錄中,您將看到`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`事件 4502 與事件訊息,其中包含以下說明:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>原因

此問題是因 Proxy 或網路防火牆封鎖 Microsoft Azure 的通訊所引起。 驗證電腦在埠 443 上具有對 ***.azure-automation.net**的出站訪問許可權。 

#### <a name="resolution"></a>解決方案

日誌存儲在**C:_程序數據\微軟系統中心\協調器\7.2_SMA_沙箱**的每個混合工作線程上本地存儲。 您可以驗證**應用程式和服務日誌中是否有任何警告或服務日誌、Microsoft-SMA_操作****以及應用程式和服務日誌\操作管理員**事件日誌中是否存在任何警告或錯誤事件。 這些日誌指示影響角色加入 Azure 自動化的連接或其他類型的問題,或在正常操作下遇到的問題。 有關解決日誌分析代理問題的其他説明,請參閱[日誌分析 Windows 代理的疑難排解問題](../../azure-monitor/platform/agent-windows-troubleshoot.md)。

混合工作人員向 Azure 自動化發送[Runbook 輸出和消息](../automation-runbook-output-and-messages.md)的方式與在雲中運行的 Runbook 作業發送輸出和消息的方式相同。 您可以像啟用 Runbook 一樣啟用"詳細"和"進度"

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>方案:協調器.Sandbox.exe 無法透過代理連接到Office 365

#### <a name="issue"></a>問題

在 Windows 混合 Runbook 工作線程上運行的腳本無法按預期方式連接到協調器沙箱上的 Office 365。 該腳本使用[連接-Msol 服務](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)進行連接。 

如果調整**協調器.Sandbox.exe.config**來設置代理和旁路清單,沙箱仍然無法正確連接。 具有相同代理和繞過清單設置**的 Powershell_ise.exe.config**檔似乎按預期工作。 服務管理自動化 (SMA) 日誌和 PowerShell 日誌不提供有關代理的任何資訊。

#### <a name="cause"></a>原因

與伺服器上的活動目錄聯合服務 (ADFS) 的連接無法繞過代理。 請記住,PowerShell 沙箱以記錄使用者身份運行。 但是,協調器沙箱是高度自定義的,可能會忽略**協調器.Sandbox.exe.config**文件設置。 它有用於處理計算機或 MMA 代理設置的特殊代碼,但不適用於處理其他自定義代理設置。 

#### <a name="resolution"></a>解決方案

您可以通過移腳本來使用 Azure AD 模組而不是 PowerShell cmdlet 的 MSOnline 模組來解決協調器沙箱的問題。 請參閱[從協調器遷移到 Azure 自動化 (Beta)。](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration)

如果要繼續使用 MSOnline 模組 cmdlet,請變更文稿以使用[呼叫命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。 指定`ComputerName``Credential`和參數的值。 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

此代碼更改在指定憑據的上下文中啟動一個全新的 PowerShell 作業階段。 它應使流量能夠流經正在驗證活動使用者的代理伺服器。

>[!NOTE]
>此解決方案使得無需操作沙箱配置檔。 即使您成功地使配置檔與腳本一起工作,每次更新混合 Runbook 工作代理時,該檔也會被擦除。

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>方案:混合 Runbook 工作人員不報告

#### <a name="issue"></a>問題

您的混合式 Runbook 背景工作角色電腦正在執行中，但您看不到工作區中機器的活動訊號資料。

下列範例查詢會顯示工作區中的機器及其上次活動訊號：

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

這個問題可能是因為混合式 Runbook 背景工作角色上的快取損毀所造成的。

#### <a name="resolution"></a>解決方案

若要解決此問題，請登入混合式 Runbook 背景工作角色，並執行下列程式碼。 此腳本停止 Windows 的日誌分析代理,刪除其緩存,並重新啟動服務。 此動作會強制混合式 Runbook 背景工作角色重新從「Azure 自動化」下載其設定。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>專案:無法新增混合 Runbook 輔助角色

#### <a name="issue"></a>問題

您在嘗試使用 `Add-HybridRunbookWorker` Cmdlet 來新增「混合式 Runbook 背景工作角色」時收到下列訊息。

```error
Machine is already registered
```

#### <a name="cause"></a>原因

如果電腦已註冊到其他自動化帳戶,或者您嘗試在從計算機中刪除混合 Runbook 輔助角色後讀取該角色,則可能導致此問題。

#### <a name="resolution"></a>解決方案

要解決此問題,請刪除以下註冊表項,重新啟動`HealthService`, 然後`Add-HybridRunbookWorker`重試 cmdlet。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>後續步驟

如果您在上面看不到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。