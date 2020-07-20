---
title: 針對 Azure 自動化狀態設定的問題進行疑難排解
description: 本文說明如何診斷並解決 Azure 自動化狀態設定的問題。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8043369ebfef23ed84ccff8e7428fbd2048e10b0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187212"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>針對 Azure 自動化狀態設定的問題進行疑難排解

本文探討如何診斷並解決您在 Azure 自動化狀態設定中編譯或部署設定時發生的問題。 如需狀態設定功能的一般資訊，請參閱 [Azure 自動化狀態設定概觀](../automation-dsc-overview.md)。

## <a name="diagnose-an-issue"></a>診斷問題

發生設定方面的編譯或部署錯誤時，以下幾個步驟可協助您診斷問題。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1.確定您的設定在本機電腦上成功編譯

Azure 自動化狀態設定以 PowerShell Desired State Configuration (DSC) 為基礎。 您可以在 [PowerShell DSC](/powershell/scripting/overview) 中找到 DSC 語言和語法的文件。

您可以在本機電腦上編譯 DSC 設定，以探索並解決常見的錯誤，例如：

   - 遺失模組。
   - 語法錯誤。
   - 邏輯錯誤。

### <a name="2-view-dsc-logs-on-your-node"></a>2.在節點上檢視 DSC 記錄

如果您的設定成功編譯，但套用至節點後失敗，您可以在 DSC 記錄中找到詳細資訊。 如需哪裡可找到這些記錄的詳細資訊，請參閱 [DSC 事件記錄位於何處](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) 模組可協助您剖析來自 DSC 記錄的詳細資訊。 如果您連絡支援人員，他們會需要這些記錄來診斷您的問題。

您可以遵循[安裝穩定版模組](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的指示，在本機電腦上安裝 `xDscDiagnostics` 模組。

若要在 Azure 電腦上安裝 `xDscDiagnostics` 模組，請使用 [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0)。 您也可以遵循[使用執行命令在 Windows VM 中執行 PowerShell 指令碼](../../virtual-machines/windows/run-command.md)中的步驟，在 Azure 入口網站中使用 [執行命令] 選項。

如需使用 **xDscDiagnostics** 的詳細資訊，請參閱[使用 xDscDiagnostics 分析 DSC 記錄](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)。 另請參閱 [xDscDiagnostics Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3.確認節點和自動化工作區具有必要的模組

DSC 依賴節點上安裝的模組。 當您使用 Azure 自動化狀態設定時，請遵循[匯入模組](../shared-resources/modules.md#import-modules)中的步驟，將任何必要的模組匯入到您的自動化帳戶。 設定也可能相依於特定版本的模組。 如需詳細資訊，請參閱[針對模組進行疑難排解](shared-resources.md#modules)。

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>案例：無法從入口網站刪除含有特殊字元的設定

### <a name="issue"></a>問題

當您嘗試從入口網站刪除 DSC 設定時，您看到下列錯誤：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>原因

此錯誤是暫時性問題，已規劃要解決。

### <a name="resolution"></a>解決方案

使用[AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0) Cmdlet 來刪除設定。

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>案例：無法註冊 DSC 應用程式

### <a name="issue"></a>問題

執行 [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) 或另一個 DSC Cmdlet 時，您收到錯誤：

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>原因

此錯誤通常起因於防火牆、位於 Proxy 伺服器後方的電腦或其他網路錯誤。

### <a name="resolution"></a>解決方案

請確認電腦可存取 DSC 的適當端點，然後再試一次。 關於所需的連接埠和位址清單，請參閱[網路規劃](../automation-dsc-overview.md#network-planning)。

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>案例：狀態報告傳回「未經授權」回應碼

### <a name="issue"></a>問題

當您向「Azure 自動化狀態設定」註冊節點時，您收到下列其中一個錯誤訊息：

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此問題起因於錯誤或過期的憑證。 請參閱[重新註冊節點](../automation-dsc-onboarding.md#re-register-a-node)。

此問題也可能起因於 Proxy 設定不允許存取 * **.azure-automation.net**。 如需詳細資訊，請參閱[私人網路的設定](../automation-dsc-overview.md#network-planning)。 

### <a name="resolution"></a>解決方案

使用下列步驟來重新註冊失敗的 DSC 節點。

#### <a name="step-1-unregister-the-node"></a>步驟 1:取消註冊節點

1. 在 Azure 入口網站中，移至 [首頁] > [自動化帳戶] > (您的自動化帳戶) > [狀態設定 (DSC)]。
1. 選取 [節點]，然後選取有問題的節點。
1. 選取 [取消註冊] 以取消註冊節點。

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>步驟 2:從節點解除安裝 DSC 擴充功能

1. 在 Azure 入口網站中，移至 [首頁] > 虛擬機器 > (失敗節點) > [擴充功能]。
1. 選取 PowerShell DSC 擴充功能 [Microsoft.Powershell.DSC]。
1. 選取 [解除安裝] 以解除安裝擴充功能。

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>步驟 3：從節點移除所有錯誤或過期的憑證

在失敗節點上，從提高權限的 PowerShell 命令提示字元中執行下列命令：

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>步驟 4：重新註冊失敗節點

1. 在 Azure 入口網站中，移至 [首頁] > [自動化帳戶] > (您的自動化帳戶) > [狀態設定 (DSC)]。
1. 選取 [節點]。
1. 選取 [新增]。
1. 選取失敗節點。
1. 選取 [連線]，然後選取您想要的選項。

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>案例：節點處於失敗狀態，並發生「找不到」錯誤

### <a name="issue"></a>問題

節點在報告中出現「失敗」狀態，而且發生錯誤：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>原因

此錯誤通常起因於將節點指派給設定名稱 (例如 **ABC**)，而不是指派給節點設定 (MOF 檔案) 名稱 (例如 **ABC.WebServer**)。

### <a name="resolution"></a>解決方案

* 請確定您以「節點設定名稱」來指派節點，而不是使用「設定名稱」。
* 您可以使用 Azure 入口網站或 PowerShell Cmdlet，將節點設定指派給節點。

  * 在 Azure 入口網站中，移至 [首頁] > [自動化帳戶] > (您的自動化帳戶) > [狀態設定 (DSC)]。 然後選取節點，再選取 [指派節點設定]。
  * 使用 [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) Cmdlet。

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>案例：編譯設定時未產生任何節點設定 (MOF 檔案)

### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>原因

當 DSC 設定中在 `Node` 關鍵字後面的運算式評估為 `$null` 時，不會產生任何節點設定。

### <a name="resolution"></a>解決方案

使用下列其中一個解決方案來修正問題：

* 請確定設定定義中在 `Node` 關鍵字旁的運算式不是評估為 Null。
* 如果您在編譯設定時傳遞 [ConfigurationData](../automation-dsc-compile.md)，請確定您傳遞的值符合設定從設定資料中預期的值。

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>案例：DSC 節點報告停滯於「進行中」狀態

### <a name="issue"></a>問題

DSC 代理程式輸出：

```error
No instance found with given property values
```

### <a name="cause"></a>原因

您已升級 Windows Management Framework (WMF) 版本，但 Windows Management Instrumentation (WMI) 已損毀。

### <a name="resolution"></a>解決方案

請遵循 [DSC 已知問題和限制](/powershell/scripting/wmf/known-issues/known-issues-dsc)中的指示進行。

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>案例：無法在 DSC 設定中使用認證

### <a name="issue"></a>問題

DSC 編譯工作因為下列錯誤而暫止：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>原因

您已在設定中使用認證，但沒有為每個節點設定提供適當的 `ConfigurationData` 將 `PSDscAllowPlainTextPassword` 設為 true。

### <a name="resolution"></a>解決方案

針對設定中提到的每個節點設定，請確定傳入適當的 `ConfigurationData` 將 `PSDscAllowPlainTextPassword` 設為 true。 請參閱[在 Azure 自動化狀態設定中編譯 DSC 設定](../automation-dsc-compile.md)。

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>案例：從 DSC 擴充功能啟用電腦時發生「處理擴充功能失敗」錯誤

### <a name="issue"></a>問題

使用 DSC 擴充功能啟用電腦時失敗，傳回下列錯誤：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>原因

此錯誤通常起因於指派不存在於服務中的節點設定名稱給節點。

### <a name="resolution"></a>解決方案

* 請確定您指派給節點的名稱與服務中的名稱完全相符。
* 您可以選擇不包括節點設定名稱，這樣會啟用節點，但不會指派節點設定。

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>案例：使用 PowerShell 註冊節點時出現「發生一或多個錯誤」錯誤

### <a name="issue"></a>問題

當您使用 [Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) 或 [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0) 註冊節點時，您收到下列錯誤：

```error
One or more errors occurred.
```

### <a name="cause"></a>原因

此錯誤起因於您嘗試在自動化帳戶使用的訂用帳戶之外註冊節點。

### <a name="resolution"></a>解決方案

將跨訂用帳戶節點視為定義給個別雲端或內部部署。 使用下列其中一個選項來啟用機器，以註冊節點：

* Windows：[位於內部或 Azure/AWS 以外雲端的實體/虛擬 Windows 機器](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines)。
* Linux：[位於內部或 Azure 以外雲端的實體/虛擬 Linux 機器](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)。

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>案例：「佈建失敗」錯誤訊息

### <a name="issue"></a>問題

當您註冊節點時，您看到下列錯誤：

```error
Provisioning has failed
```

### <a name="cause"></a>原因

當節點與 Azure 之間的連線能力發生問題時，就會出現此訊息。

### <a name="resolution"></a>解決方案

判斷您的節點是否在虛擬私人網路 (VPN) 中，或是否有其他問題導致無法連線到 Azure。 請參閱[針對功能部署問題進行疑難排解](onboarding.md)。

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>案例：在 Linux 中套用設定時失敗，發生一般錯誤

### <a name="issue"></a>問題

在 Linux 中套用設定時失敗，傳回下列錯誤：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>原因

如果 **/tmp** 位置設為 `noexec`，則目前的 DSC 版本無法套用設定。

### <a name="resolution"></a>解決方案

從 **/tmp** 位置移除 `noexec` 選項。

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>案例：重疊的節點設定名稱可能導致錯誤的版本

### <a name="issue"></a>問題

當您使用單一設定指令碼來產生多個節點設定，但某些節點設定名稱是其他名稱的子集時，編譯服務最後會指派錯誤的設定。 只有當您使用單一指令碼來產生設定，且每一節點都有設定資料時，而且只有當字串開頭發生名稱重疊時，才會發生此問題。 例如，在 Cmdlet 中使用單一設定指令碼，根據 以雜湊表傳遞的節點資料來產生設定，而節點資料包含名為 **server** 和 **1server** 的伺服器。

### <a name="cause"></a>原因

這是編譯服務的已知問題。

### <a name="resolution"></a>解決方案

最佳解決方法是在本機或 CI/CD 管道中編譯，並將節點設定 MOF 檔案直接上傳至服務。 如果必須在服務中編譯，則次佳解決方法是分割編譯作業，以避免名稱重疊。

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>案例：DSC 設定上傳時發生閘道逾時錯誤

#### <a name="issue"></a>問題

當您上傳 DSC 設定時，您收到 `GatewayTimeout` 錯誤。 

### <a name="cause"></a>原因

需要較長時間來編譯的 DSC 設定可能會造成此錯誤。

### <a name="resolution"></a>解決方案

您可以在任何 [Import-DSCResource](/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) 呼叫中明確包含 `ModuleName` 參數，以加速剖析 DSC 設定。

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個管道以取得其他支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 聯繫 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援將 Azure 社群連結到解答、支援及專家。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
