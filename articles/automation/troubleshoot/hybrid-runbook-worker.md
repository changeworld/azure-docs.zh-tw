---
title: 疑難排解 - Azure 自動化混合式 Runbook 背景工作角色
description: 本文提供針對 Azure 自動化混合式 Runbook 背景工作角色進行疑難排解的資訊
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 137623e4c52d24061aec8ec11fca0fc02ca54c7f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372506"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>混合式 Runbook 背景工作的疑難排解

本文提供針對混合式 Runbook 背景工作角色問題進行疑難排解的資訊。

## <a name="general"></a>一般

混合式 Runbook 背景工作角色取決於與自動化帳戶通訊的代理程式，來註冊背景工作角色、接收 Runbook 作業及報告狀態。 對於 Windows，此代理程式是適用于 Windows 的 Log Analytics 代理程式，也稱為 Microsoft Monitoring Agent （MMA）。 針對 Linux，這是適用于 Linux 的 Log Analytics 代理程式。

### <a name="runbook-execution-fails"></a>案例：Runbook 執行失敗

#### <a name="issue"></a>問題

Runbook 執行失敗，並出現下列錯誤。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

您的 runbook 在嘗試執行三次後很快就會暫停。 有一些條件可以中斷 runbook 完成。 相關的錯誤訊息可能不會包含任何其他資訊。

#### <a name="cause"></a>原因

可能的原因如下：

* Runbook 無法使用本機資源進行驗證。

* 混合式背景工作角色位於 Proxy 或防火牆後方。

* 設定為執行「混合式 Runbook 背景工作角色」功能的電腦不符合最低硬體需求。

#### <a name="resolution"></a>解決方案

確認電腦對埠443上的 *. azure-automation.net 具有輸出存取權。

執行混合式 Runbook 背景工作角色的電腦應符合最低硬體需求，才能將背景工作角色設定為裝載此功能。 Runbook 及其使用的背景進程可能會導致系統過度使用，並導致 runbook 作業延遲或超時。

確認執行「混合式 Runbook 背景工作角色」功能的電腦符合最低硬體需求。 如果滿足最低硬體需求，請監視 CPU 和記憶體使用率，判斷混合式 Runbook 背景工作角色處理序之效能和 Windows 之間是否有任何相互關聯。 任何記憶體或 CPU 壓力都可能表示需要升級資源。 您也可以選取不同的計算資源來支援最低需求，並在工作負載需求指出需要增加時進行調整。

查閱 **Microsoft-SMA** 事件記錄檔，找出描述為「Win32 處理序結束，代碼為 [4294967295]」的對應事件。 此錯誤的原因是您尚未在 runbook 中設定驗證，或指定了混合式 Runbook 背景工作角色群組的執行身分認證。 請參閱在[混合式 runbook 背景工作角色上執行](../automation-hrw-run-runbooks.md)runbook 中的 runbook 許可權，以確認您已正確設定 runbook 的驗證。

### <a name="no-cert-found"></a>案例：在混合式 Runbook 背景工作角色的憑證存放區中找不到憑證

#### <a name="issue"></a>問題

在混合式 Runbook 背景工作角色上執行的 runbook 因下列錯誤訊息而失敗。

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>原因

當您嘗試在執行身分帳戶憑證不存在的混合式 Runbook 背景工作角色上執行的 runbook 中使用[執行身分帳戶](../manage-runas-account.md)時，就會發生此錯誤。 混合式 Runbook 背景工作角色預設不會有本機的憑證資產，因此執行身分帳戶必須要有此認證才能正常運作。

#### <a name="resolution"></a>解決方案

如果您的混合式 Runbook 背景工作角色是 Azure VM，您可以改[為使用 azure 資源的受控](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)識別。 此案例可讓您使用 Azure VM 的受控識別（而非執行身分帳戶）來向 Azure 資源進行驗證，藉此簡化驗證。 當混合式 Runbook 背景工作角色是內部部署電腦時，您必須在電腦上安裝執行身分帳戶憑證。 若要瞭解如何安裝憑證，請參閱在[混合式 runbook 背景工作角色上執行 runbook](../automation-hrw-run-runbooks.md)中執行 PowerShell runbook 匯出-export-runascertificatetohybridworker 的步驟。

### <a name="error-403-on-registration"></a>案例：在混合式 Runbook 背景工作角色註冊期間發生錯誤403

#### <a name="issue"></a>問題

背景工作的初始註冊階段失敗，而您收到下列錯誤（403）。

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

可能的原因如下：
* 代理程式的設定中有輸入錯誤的工作區識別碼或工作區金鑰（主要）。 
* 混合式 Runbook 背景工作角色無法下載設定，導致帳戶連結錯誤。 當 Azure 啟用解決方案時，它只支援特定區域來連結 Log Analytics 工作區和自動化帳戶。 此外，電腦上也可能設定了不正確的日期和/或時間。 如果時間是從目前時間開始的 +/-15 分鐘，上架會失敗。

#### <a name="resolution"></a>解決方案

##### <a name="mistyped-workspace-idkey"></a>輸入錯誤的工作區識別碼/金鑰
若要確認代理程式的工作區識別碼或工作區金鑰是否輸入錯誤，請參閱[新增或移除工作區–](../../azure-monitor/platform/agent-manage.md#windows-agent) windows 代理程式的 windows 代理程式，或[新增或移除](../../azure-monitor/platform/agent-manage.md#linux-agent)Linux 代理程式的 linux 代理程式。  請務必從 Azure 入口網站中選取完整的字串，並小心複製並貼上它。

##### <a name="configuration-not-downloaded"></a>設定未下載

您的 Log Analytics 工作區和自動化帳戶必須位於已連結的區域。 如需支援的區域清單，請參閱[Azure 自動化和 Log Analytics 工作區](../how-to/region-mappings.md)對應。

您可能也需要更新電腦的日期和時間區域。 如果您選取自訂的時間範圍，請確定範圍是以 UTC 為准，這可能會與您的當地時區不同。

## <a name="linux"></a>Linux

Linux 混合式 Runbook 背景工作角色取決於[適用于 linux 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)，來與您的自動化帳戶進行通訊，以註冊背景工作角色、接收 Runbook 作業及報告狀態。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因：

### <a name="oms-agent-not-running"></a>案例：適用于 Linux 的 Log Analytics 代理程式未執行

#### <a name="issue"></a>問題

適用于 Linux 的 Log Analytics 代理程式未執行

#### <a name="cause"></a>原因

如果代理程式未執行，它會讓 Linux 混合式 Runbook 背景工作角色無法與 Azure 自動化進行通訊。 代理程式可能因各種原因而無法執行。

#### <a name="resolution"></a>解決方案

 輸入下列命令，確認代理程式正在執行：`ps -ef | grep python`。 您所看到的輸出應該會類似下列具有 **nxautomation** 使用者帳戶的 Python 處理序。 如果未啟用更新管理或 Azure 自動化解決方案，下列處理序都不會執行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

下列清單顯示已針對 Linux 混合式 Runbook 背景工作角色啟動的處理序。 這些程序全都位於 `/var/opt/microsoft/omsagent/state/automationworker/` 目錄。

* **oms** -背景工作角色管理員進程。 它會直接從 DSC 啟動。

* **worker. 會議**-自動註冊的混合式背景工作進程。 它是由背景工作角色管理員啟動。 更新管理會使用此處理序，且使用者不會注意到此處理序。 如果機器上未啟用更新管理解決方案，就不會出現此處理序。

* **diy/worker. 會議**-diy 混合式背景工作進程。 DIY 混合式背景工作處理序可用來在混合式 Runbook 背景工作角色上執行使用者 Runbook。 它與自動註冊的混合式背景工作進程不同，主要細節是使用不同的設定。 如果已停用 Azure 自動化解決方案，而且未註冊 DIY Linux 混合式背景工作角色，則不會出現此進程。

若代理程式未執行，請執行下列命令來啟動服務： `sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="class-does-not-exist"></a>案例：指定的類別不存在

如果您看到錯誤 **，指定的類別不存在。。** 在 `/var/opt/microsoft/omsconfig/omsconfig.log`中，需要更新適用于 Linux 的 Log Analytics 代理程式。 執行下列命令來重新安裝代理程式：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows 混合式 Runbook 背景工作角色取決於[適用于 Windows 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)，來與您的自動化帳戶進行通訊，以註冊背景工作角色、接收 Runbook 作業及報告狀態。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因：

### <a name="mma-not-running"></a>案例： Microsoft Monitoring Agent 不在執行中

#### <a name="issue"></a>問題

`healthservice` 服務未在混合式 Runbook 背景工作角色機器上執行。

#### <a name="cause"></a>原因

如果 Microsoft Monitoring Agent 的 Microsoft 服務並未執行，此狀態會使混合式 Runbook 背景工作角色無法與 Azure 自動化通訊。

#### <a name="resolution"></a>解決方案

在 PowerShell 中輸入下列命令，確認代理程式正在執行：`Get-Service healthservice`。 如果服務已停止，在 PowerShell 中輸入下列命令可啟動服務：`Start-Service healthservice`。

### <a name="event-4502"></a>案例： Operations Manager 記錄檔中的事件4502

#### <a name="issue"></a>問題

在 [**應用程式和服務 Logs\Operations 管理員**] 事件記錄檔中，您會看到包含**microsoft.enterprisemanagement**的事件4502和 EventMessage，其描述如下：*服務所呈現的憑證 \<AzureAutomation\>。 Microsoft.enterprisemanagement.healthservice.azureautomation.hybridagent 事件不是由用於 Microsoft 服務的憑證授權單位單位所發行。請洽詢您的網路系統管理員，確認他們是否正在執行可攔截 TLS/SSL 通訊的 proxy。*

#### <a name="cause"></a>原因

此問題是因 Proxy 或網路防火牆封鎖 Microsoft Azure 的通訊所引起。 確認電腦可透過連接埠 443 輸出存取 *.azure-automation.net。 

#### <a name="resolution"></a>解決方案

記錄儲存每一個混合式背景工作角色本機的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 您可以確認 [**應用程式和服務 Logs\Microsoft-SMA\Operations** ] 和 [**應用程式及服務 Logs\Operations 管理員**] 事件記錄檔中是否有任何警告或錯誤事件，指出會影響角色上線的連線能力或其他問題，以在正常作業下進行 Azure 自動化或問題。 如需 Log Analytics 代理程式問題的其他協助疑難排解，請參閱針對[Log Analytics Windows 代理程式的問題進行疑難排解](../../azure-monitor/platform/agent-windows-troubleshoot.md)。

[Runbook 輸出和訊息](../automation-runbook-output-and-messages.md) 會從混合式背景工作角色傳送到 Azure 自動化，就像雲端中執行的 Runbook 工作一樣。 您也可以啟用詳細資訊和進度資料流，就像您在其他 Runbook 中的作法一樣。

### <a name="corrupt-cache"></a>案例：混合式 Runbook 背景工作角色未報告

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

若要解決此問題，請登入混合式 Runbook 背景工作角色，並執行下列程式碼。 此指令碼會停止 Microsoft Monitoring Agent、移除其快取，並重新啟動服務。 此動作會強制混合式 Runbook 背景工作角色從 Azure 自動化重新下載其設定。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>案例：您無法新增混合式 Runbook 背景工作角色

#### <a name="issue"></a>問題

您在嘗試使用 `Add-HybridRunbookWorker` Cmdlet 來新增「混合式 Runbook 背景工作角色」時收到下列訊息。

```error
Machine is already registered
```

#### <a name="cause"></a>原因

如果電腦已向不同的自動化帳戶註冊，或如果您在將混合式 Runbook 背景工作角色從電腦移除之後嘗試進行重新新增，可能會導致此問題。

#### <a name="resolution"></a>解決方案

若要解決此問題，請移除下列登錄機碼，然後重新啟動 `HealthService` 並重新嘗試 `Add-HybridRunbookWorker` Cmdlet：

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。