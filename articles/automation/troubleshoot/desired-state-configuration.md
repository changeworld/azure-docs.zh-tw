---
title: 疑難排解 Azure 自動化狀態設定
description: 本文提供 Azure 自動化狀態設定疑難排解的相關資訊。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c9e7b6d93fb4bbc3e3b05d9346ec84197665a55
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995295"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>針對 Azure 自動化狀態設定問題進行疑難排解

本文提供有關在 Azure 自動化狀態設定中編譯或部署設定時，所發生之問題的疑難排解資訊。

## <a name="diagnose-an-issue"></a>診斷問題

當您收到設定的編譯或部署錯誤時，以下是可協助您診斷問題的幾個步驟。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 確定您的設定在本機電腦上成功編譯

Azure 自動化狀態設定是以 PowerShell Desired State Configuration （DSC）為基礎。 您可以在[POWERSHELL dsc](https://docs.microsoft.com/powershell/scripting/overview)檔中找到 DSC 語言和語法的檔。

藉由在本機電腦上編譯 DSC 設定，您可以探索並解決常見的錯誤，例如：

   - 遺失的模組。
   - 語法錯誤。
   - 邏輯錯誤。

### <a name="2-view-dsc-logs-on-your-node"></a>2. 在您的節點上查看 DSC 記錄

如果您的設定成功編譯，但套用至節點時失敗，您可以在 DSC 記錄檔中找到詳細資訊。 如需有關如何尋找這些記錄檔的詳細資訊，請參閱[DSC 事件記錄檔的位置](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[XDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics)模組可協助您剖析來自 DSC 記錄檔的詳細資訊。 如果您聯繫支援人員，他們需要這些記錄來診斷您的問題。

您可以遵循`xDscDiagnostics` [安裝穩定版本模組](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的指示，在本機電腦上安裝模組。

若要在`xDscDiagnostics`您的 Azure 電腦上安裝模組，請使用[AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0)。 您也可以遵循在[WINDOWS VM 中使用執行命令執行 PowerShell 腳本](../../virtual-machines/windows/run-command.md)中的步驟，使用 Azure 入口網站中的 [**執行命令**] 選項。

如需使用**xDscDiagnostics**的詳細資訊，請參閱[使用 xDscDiagnostics 分析 DSC 記錄](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)檔。 另請參閱[XDscDiagnostics Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 確定節點和自動化工作區具有必要的模組

DSC 取決於節點上安裝的模組。 當您使用 Azure 自動化狀態設定時，請遵循匯[入模組](../shared-resources/modules.md#import-modules)中的步驟，將任何必要的模組匯入到您的自動化帳戶。 設定也可能相依于特定版本的模組。 如需詳細資訊，請參閱針對[模組進行疑難排解](shared-resources.md#modules)。

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>案例：無法從入口網站刪除具有特殊字元的設定

### <a name="issue"></a>問題

當您嘗試從入口網站刪除 DSC 設定時，您會看到下列錯誤：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>原因

此錯誤是計畫要解決的暫時性問題。

### <a name="resolution"></a>解決方案

使用 [AzAutomationDscConfiguration] （https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 Cmdlet 來刪除設定。

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>案例：無法註冊 DSC 代理程式

### <a name="issue"></a>問題

當[DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1)或另一個 DSC Cmdlet 時，您會收到錯誤：

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

此錯誤通常是由防火牆、位於 proxy 伺服器後方的電腦，或其他網路錯誤所造成。

### <a name="resolution"></a>解決方案

請確認您的電腦具有適當的 DSC 端點存取權，然後再試一次。 如需所需的埠和地址清單，請參閱[網路規劃](../automation-dsc-overview.md#network-planning)。

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>案例：狀態報表傳回未經授權的回應碼

### <a name="issue"></a>問題

當您使用 Azure 自動化狀態設定註冊節點時，會收到下列其中一個錯誤訊息：

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此問題是因為憑證錯誤或過期所造成。 請參閱[重新註冊節點](../automation-dsc-onboarding.md#re-register-a-node)。

此問題也可能是因為 proxy 設定不允許存取 ***. azure-automation.net**所造成。 如需詳細資訊，請參閱[私人網路的](../automation-dsc-overview.md#network-planning)設定。 

### <a name="resolution"></a>解決方案

使用下列步驟來重新註冊失敗的 DSC 節點。

#### <a name="step-1-unregister-the-node"></a>步驟1：取消註冊節點

1. 在 Azure 入口網站中，移至 [**首頁** > **自動化帳戶**] > （您的自動化帳戶） >**狀態設定（DSC）**]。
1. 選取 [**節點**]，然後選取發生問題的節點。
1. 選取 [**取消註冊**] 以取消註冊節點。

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>步驟2：從節點卸載 DSC 擴充功能

1. 在 Azure 入口網站中，移至 [**首頁** > ] [**虛擬機器**] [> （失敗節點）] > [**擴充**功能]。
1. 選取 [ **Microsoft**Dsc，powershell dsc 延伸模組]。
1. 選取 [**卸載**] 以卸載擴充功能。

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>步驟3：從節點移除所有錯誤或過期的憑證

在 [失敗] 節點上，從提升許可權的 PowerShell 提示字元執行下列命令：

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

#### <a name="step-4-reregister-the-failing-node"></a>步驟4：重新註冊失敗的節點

1. 在 Azure 入口網站中，移至 [**首頁** > **自動化帳戶**] > （您的自動化帳戶） >**狀態設定（DSC）**]。
1. 選取 [**節點**]。
1. 選取 [新增]  。
1. 選取失敗的節點。
1. 選取 **[連線]**，然後選取您想要的選項。

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>案例：節點處於失敗狀態，並發生「找不到」錯誤

### <a name="issue"></a>問題

節點具有 [失敗] 狀態的報表，並包含錯誤：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>原因

當節點指派給設定名稱（例如**abc**），而不是節點設定（MOF 檔案）名稱（例如 abc）時，通常會發生此錯誤 **。Web**伺服器。

### <a name="resolution"></a>解決方案

* 請確定您是使用節點設定名稱來指派節點，而不是設定名稱。
* 您可以使用 Azure 入口網站或使用 PowerShell Cmdlet，將節點設定指派給節點。

  * 在 Azure 入口網站中，移至 [**首頁** > **自動化帳戶**] > （您的自動化帳戶） >**狀態設定（DSC）**]。 然後選取節點，並選取 [**指派節點**設定]。
  * 使用[AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) Cmdlet。

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>案例：編譯設定時，沒有產生任何節點設定（MOF 檔案）

### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>原因

當 DSC 設定中的`Node`關鍵字後面的運算式評估為`$null`時，不會產生任何節點設定。

### <a name="resolution"></a>解決方案

使用下列其中一個解決方案來修正問題：

* 請確定設定定義中`Node`關鍵字旁邊的運算式未評估為 Null。
* 如果您在編譯設定時傳遞[ConfigurationData](../automation-dsc-compile.md) ，請確定您要傳遞設定資料所預期的值。

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>案例： DSC 節點報表會停滯在進行中狀態

### <a name="issue"></a>問題

DSC 代理程式輸出：

```error
No instance found with given property values
```

### <a name="cause"></a>原因

您已升級您的 Windows Management Framework （WMF）版本，但 Windows Management Instrumentation （WMI）已損毀。

### <a name="resolution"></a>解決方案

遵循[DSC 已知問題和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)中的指示。

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>案例：無法在 DSC 設定中使用認證

### <a name="issue"></a>問題

您的 DSC 編譯工作因下列錯誤而暫停：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>原因

您已在設定中使用認證，但未提供適當`ConfigurationData`的， `PSDscAllowPlainTextPassword`以將每個節點設定設為 true。

### <a name="resolution"></a>解決方案

請務必針對設定中所述`ConfigurationData`的每`PSDscAllowPlainTextPassword`個節點設定，傳遞適當的以將設為 true。 請參閱[在 Azure 自動化狀態設定中編譯 DSC](../automation-dsc-compile.md)設定。

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>案例：從 DSC 延伸模組啟用電腦時發生「失敗處理延伸模組」錯誤

### <a name="issue"></a>問題

當您使用 DSC 擴充功能啟用電腦時，會發生失敗，其中包含錯誤：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>原因

當節點指派的節點設定名稱不存在於服務中時，通常會發生此錯誤。

### <a name="resolution"></a>解決方案

* 請確定您所指派的節點名稱與服務中的名稱完全相符。
* 您可以選擇不包含節點設定名稱，這會導致節點無法指派節點設定。

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>案例：使用 PowerShell 註冊節點時發生「發生一或多個錯誤」錯誤

### <a name="issue"></a>問題

當您使用[AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0)或[unregister-azurermautomationdscnode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)註冊節點時，您會收到下列錯誤：

```error
One or more errors occurred.
```

### <a name="cause"></a>原因

當您嘗試在不同的訂用帳戶中註冊節點，而該節點是由自動化帳戶所使用時，就會發生此錯誤。

### <a name="resolution"></a>解決方案

將跨訂用帳戶節點視為針對個別雲端或內部部署所定義。 使用下列其中一個選項來註冊節點，以啟用機器：

* Windows：[內部部署的實體/虛擬 Windows 機器，或 Azure/AWS 以外的雲端](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines)。
* Linux：[內部部署或 Azure 以外之雲端中的實體/虛擬 Linux 機器](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)。

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>案例：「布建失敗」錯誤訊息

### <a name="issue"></a>問題

當您註冊節點時，您會看到錯誤：

```error
Provisioning has failed
```

### <a name="cause"></a>原因

當節點與 Azure 之間的連線發生問題時，就會出現此訊息。

### <a name="resolution"></a>解決方案

判斷您的節點是否在虛擬私人網路（VPN）中，或是否有其他連線到 Azure 的問題。 請參閱針對[在上架解決方案時的錯誤進行疑難排解](onboarding.md)。

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>案例：在 Linux 中套用設定時，發生一般錯誤

### <a name="issue"></a>問題

當您在 Linux 中套用設定時，會發生失敗，其中包含錯誤：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>原因

如果 **/tmp**位置設為`noexec`，則目前的 DSC 版本將無法套用設定。

### <a name="resolution"></a>解決方案

從/tmp `noexec`位置移除選項 **/tmp** 。

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>案例：重迭的節點設定名稱可能會導致錯誤的版本

### <a name="issue"></a>問題

當您使用單一設定腳本來產生多個節點設定，而某些節點設定名稱是其他名稱的子集時，編譯服務最後會指派錯誤的設定。 只有當您使用單一腳本來產生具有每個節點之設定資料的設定，而且只有在字串開頭髮生名稱重迭時，才會發生此問題。 範例是單一設定腳本，可用來根據使用 Cmdlet 當做雜湊表傳遞的節點資料來產生設定，而節點資料則包含名為**server**和**1server**的伺服器。

### <a name="cause"></a>原因

這是編譯服務的已知問題。

### <a name="resolution"></a>解決方案

最佳的解決方法是在本機或在 CI/CD 管線中編譯，並將節點設定 MOF 檔案直接上傳至服務。 如果服務中的編譯是需求，則下一個最佳的解決方法是分割編譯工作，讓名稱中沒有任何重迭。

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>案例： DSC 設定上傳時發生閘道逾時錯誤

#### <a name="issue"></a>問題

當您上`GatewayTimeout`傳 DSC 設定時，會收到錯誤。 

### <a name="cause"></a>原因

需要較長時間進行編譯的 DSC 設定可能會造成此錯誤。

### <a name="resolution"></a>解決方案

您可以藉由明確包含任何`ModuleName` [DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)呼叫的參數，讓 DSC 設定的剖析速度更快。

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶聯繫，以改善客戶體驗。 Azure 支援將 Azure 社區連接到解答、支援及專家。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。
