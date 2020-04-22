---
title: 容毀毀 Azure 自動化狀態設定 (DSC)
description: 本文提供有關對 Azure 自動化狀態配置 (DSC) 進行故障排除的資訊。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679314"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>解決 Azure 自動化狀態設定 (DSC) 問題

本文提供有關在 Azure 自動化狀態配置 (DSC) 中編譯或部署配置時出現的故障排除問題的資訊。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="diagnosing-an-issue"></a>診斷問題

當您收到設定的編譯或部署錯誤時,下面是説明您診斷問題的幾個步驟。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 確保您的設定在本地電腦上成功編譯

Azure 自動化狀態配置 (DSC) 基於 PowerShell 所需狀態配置 (DSC) 構建。 您可以在[PowerShell DSC 文件中](https://docs.microsoft.com/powershell/scripting/overview)找到 DSC 語言和語法的文檔。

透過在本地電腦上編譯 DSC 設定,可以發現並解決常見錯誤,例如:

   - 缺少模組
   - 語法錯誤
   - 邏輯錯誤

### <a name="2-view-dsc-logs-on-your-node"></a>2. 在節點上檢視 DSC 紀錄

如果您的配置編譯成功,但在應用於節點時失敗,則可以在 DSC 日誌中找到詳細資訊。 有關在哪裡可以找到這些日誌的資訊,請參閱[DSC 事件紀錄在哪裡](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[xDsc診斷](https://github.com/PowerShell/xDscDiagnostics)模組可以説明您分析來自 DSC 日誌的詳細資訊。 如果您聯繫支援部門,他們需要這些日誌來診斷您的問題。

您可以使用`xDscDiagnostics`[安裝穩定版本模組](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的說明將模組安裝在本地電腦上。

在`xDscDiagnostics`Azure 電腦上安裝該模組,請使用[呼叫-AzVMRun命令](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0)。 還可以按照 Windows [VM 中的運行 PowerShell 腳本中](../../virtual-machines/windows/run-command.md)的步驟使用 Azure 門戶中的 **「運行」命令**選項。

有關使用**xDsc診斷**的資訊,請參閱[使用 xDsc 診斷來分析 DSC 紀錄](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)。 另請參考[xDsc 診斷 Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 確保節點與自動化工作區具有所需的模組

DSC取決於節點上安裝的模組。 使用 Azure 自動化狀態配置時,請使用導入模組 中的步驟將任何必需[的模組](../shared-resources/modules.md#importing-modules)導入自動化帳戶。 配置還可以依賴於模組的特定版本。 有關詳細資訊,請參閱[故障排除模組](shared-resources.md#modules)。

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>專案:無法從門戶中移除有特殊字元的設定

### <a name="issue"></a>問題

試著從門戶中移除 DSC 設定時,您將看到以下錯誤:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>原因

此錯誤是計劃要解決的臨時問題。

### <a name="resolution"></a>解決方案

使用 [刪除-AzAutomationDsc配置](cmdlet)https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0刪除配置。

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>方案:無法註冊 Dsc 代理

### <a name="issue"></a>問題

當[Set-Dsc 本地設定管理器](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1)或其他 DSC cmdlet 時,您會收到錯誤:

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

此錯誤通常是由防火牆、位於代理伺服器後面的電腦或其他網路錯誤引起的。

### <a name="resolution"></a>解決方案

驗證電腦是否有權訪問 DSC 的正確終結點,然後重試。 有關所需的連接埠和地址的清單,請參閱[網路規劃](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>配置:狀態回報傳回回應碼未授權

### <a name="issue"></a>問題

使用 Azure 自動化狀態設定註冊節點時,您會收到以下錯誤訊息之一:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此問題是由證書損壞或過期引起的。 請參考[憑證過期與重新註冊](../automation-dsc-onboarding.md#re-registering-a-node)。

此問題也可能由不允許訪問 ***.azure-automation.net**的代理配置引起。 有關詳細資訊,請參閱[專用網路的設定](../automation-dsc-overview.md#network-planning)。 

### <a name="resolution"></a>解決方案

使用以下步驟重新註冊失敗的 DSC 節點。

步驟 1 - 取消註冊節點。

1. 在 Azure 門戶中,導航到**家庭** -> **自動化帳戶**->(您的自動化帳戶) ->**狀態配置 (DSC)。**
2. 選擇**節點**,然後單擊遇到故障的節點。
3. 按下 **「取消註冊**」以取消註冊節點。

步驟 2 - 從節點卸載 DSC 擴展。

1. 在 Azure 門戶中,導航到**家庭** -> **虛擬機器**->(失敗節點) ->**延伸**。
2. 選擇**微軟.電源shell.DSC,** 電源外殼DSC擴展。
3. 單擊 **「卸載**」以卸載擴展。

步驟 3 - 從節點中刪除所有損壞或過期的證書。

在來自提升 PowerShell 提示符的故障節點上,執行以下命令:

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

步驟 4 - 重新註冊故障節點。

1. 在 Azure 門戶中,導航到**家庭** -> **自動化帳戶**->(您的自動化帳戶) ->**狀態配置 (DSC)**
2. 選擇**節點**。
3. 按一下 [新增]  。
4. 選擇故障節點。
5. 按下連線**並**選擇所需的選項。

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>案例：節點處於失敗狀態，並發生「找不到」錯誤

### <a name="issue"></a>問題

節點的報告具有「失敗」狀態且包含錯誤：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>原因

當節點配置指定設定名稱(例如**ABC,** 而不是節點設定 (MOF 檔) 名稱(例如 ABC)時,通常會發生此錯誤 **。網路伺服器**.

### <a name="resolution"></a>解決方案

* 請確保為節點分配節點配置名稱,而不是配置名稱。
* 您可以使用 Azure 入口網站或使用 PowerShell Cmdlet，將節點組態指派至節點。

  * 在 Azure 門戶中,導航到**家庭** -> **自動化帳戶**->(您的自動化帳戶) ->**狀態配置 (DSC),** 然後選擇一個節點並按一下「**分配節點配置**」。
  * 使用[集-阿茲自動化Dnode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) cmdlet。

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>配置:編譯設定時未產生節點設定(MOF 檔案)

### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>原因

當 DSC`Node`配置中關鍵字之後的運算為`$null`時,不會生成節點配置。

### <a name="resolution"></a>解決方案

使用以下解決方案之一來解決此問題:

* 確保配置定義關鍵`Node`字旁邊的運算式未計算為 Null。
* 如果在編譯設定時傳遞[配置數據](../automation-dsc-compile.md),請確保從配置數據傳遞配置期望的值。

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>方案:DSC 節點報告陷入「正在進行」狀態

### <a name="issue"></a>問題

DSC 代理程式輸出：

```error
No instance found with given property values
```

### <a name="cause"></a>原因

已升級 Windows 管理框架 (WMF) 版本並損壞了 Windows 管理工具 (WMI)。

### <a name="resolution"></a>解決方案

按照[DSC 中已知的問題和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)中的說明進行操作。

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>方案:無法在 DSC 設定中使用認證

### <a name="issue"></a>問題

您的 DSC 編譯作業因錯誤而掛起:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>原因

您在配置中使用了認證,但沒有為每個節點配置提供正確的`ConfigurationData`設置為`PSDscAllowPlainTextPassword`true。

### <a name="resolution"></a>解決方案

確保將設定中提到的每個節點配置`ConfigurationData`的`PSDscAllowPlainTextPassword`正確 狀態傳遞給 true。 請參考[Azure 自動化狀態設定中編譯 DSC 設定](../automation-dsc-compile.md)。

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>專案:「從 DSC 延伸載入時」失敗處理延伸「錯誤

### <a name="issue"></a>問題

使用 DSC 延伸載入時,將發生包含錯誤的失敗:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>原因

當為節點分配服務中不存在的節點配置名稱時,通常會發生此錯誤。

### <a name="resolution"></a>解決方案

* 請確保為節點分配的名稱與服務中的名稱完全匹配。
* 您可以選擇不包括節點配置名稱,這將導致節點入入,但不分配節點配置。

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>專案:「使用 PowerShell 註冊節點時發生一個或多個錯誤」錯誤

### <a name="issue"></a>問題

使用[寄存器-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0)或[寄存器-Azure-Azure 自動化DSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)註冊節點時,您會收到以下錯誤:

```error
One or more errors occurred.
```

### <a name="cause"></a>原因

當您嘗試將節點註冊到與自動化帳戶使用的單獨訂閱中的節點時,將發生此錯誤。

### <a name="resolution"></a>解決方案

將跨訂閱節點視為為單獨的雲或本地定義的節點。 使用以下載入選項之一註冊節點:

* Windows -[本地或 Azure/AWS 以外的雲端/虛擬 Windows 電腦](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines)。
* Linux -[本地或 Azure 以外的雲端或虛擬 Linux 電腦](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines)。

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>方案:錯誤消息 - "預配失敗"

### <a name="issue"></a>問題

註冊節點時,您會看到錯誤:

```error
Provisioning has failed
```

### <a name="cause"></a>原因

當節點和 Azure 之間的連接出現問題時,將發生此消息。

### <a name="resolution"></a>解決方案

確定節點是否位於虛擬專用網路 (VPN) 中,還是連接到 Azure 時還有其他問題。 請參考[在加入解決方案時排除故障錯誤](onboarding.md)。

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>方案:在 Linux 中應用設定時失敗,出現一般錯誤

### <a name="issue"></a>問題

在 Linux 中應用設定時,將發生包含錯誤的失敗:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>原因

如果 **/tmp**位置`noexec`設置為 ,則當前版本的 DSC 無法應用配置。

### <a name="resolution"></a>解決方案

從`noexec`**/tmp**位置刪除該選項。

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>配置:重疊的節點設定名稱可能會導致發佈錯誤

### <a name="issue"></a>問題

當您使用單個配置腳本生成多個節點配置,並且某些節點配置名稱是其他名稱的子集時,編譯服務最終可能會分配錯誤的配置。 僅當使用單個腳本生成每個節點具有配置數據的配置時,並且僅當名稱重疊發生在字串的開頭時,才會出現此問題。 例如,單個設定文稿用於根據使用 cmdlet 為哈希表傳遞的節點資料產生設定,節點資料包括名為**伺服器**的伺服器和 1 伺服器的**伺服器**。

### <a name="cause"></a>原因

這是編譯服務的已知問題。

### <a name="resolution"></a>解決方案

最佳解決方法是在本地或 CI/CD 管道中編譯,並將節點配置 MOF 檔直接上傳到服務。 如果服務中的編譯是必需的,則下一個最佳解決方法是拆分編譯作業,以便在名稱中沒有重疊。

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>配置:DSC 設定上載上的閘道逾時錯誤

#### <a name="issue"></a>問題

上傳 DSC 設定`GatewayTimeout`時收到 錯誤。 

### <a name="cause"></a>原因

編譯時間過長的 DSC 配置可能會導致此錯誤。

### <a name="resolution"></a>解決方案

通過顯式包括任何導入`ModuleName`[DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)調用的參數,可以使 DSC 配置解析得更快。

## <a name="next-steps"></a>後續步驟

如果您在上面看不到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。