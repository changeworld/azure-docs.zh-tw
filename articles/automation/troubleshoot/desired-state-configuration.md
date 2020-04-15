---
title: 排除 Azure 自動化狀態配置 (DSC) 的故障
description: 本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8e0b874e0ce56d7b4ed9fce15b2d88503d6c49b7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309135"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>解決 Azure 自動化狀態配置 (DSC) 的問題

本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊。

## <a name="diagnosing-an-issue"></a>診斷問題

在 Azure 狀態配置中編譯或部署配置時出錯時,下面是説明您診斷問題的幾個步驟。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 確保您的設定在本地電腦上成功編譯

Azure 狀態配置基於 PowerShell DSC 構建。 您可以在[PowerShell DSC 文件中](https://docs.microsoft.com/powershell/scripting/overview)找到 DSC 語言和語法的文檔。

透過在本地電腦上編譯 DSC 設定,可以發現並解決常見錯誤,例如:

   - 缺少模組
   - 語法錯誤
   - 邏輯錯誤

### <a name="2-view-dsc-logs-on-your-node"></a>2. 在節點上檢視 DSC 紀錄

如果您的配置編譯成功,但在應用於節點時失敗,則可以在 DSC 日誌中找到詳細資訊。 有關在哪裡可以找到這些日誌的資訊,請參閱[DSC 事件紀錄在哪裡](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[xDsc診斷](https://github.com/PowerShell/xDscDiagnostics)模組可以説明您分析來自 DSC 日誌的詳細資訊。 如果您聯繫支援部門,他們需要這些日誌來診斷您的問題。

您可以使用[安裝穩定版本模組](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的說明在本地電腦上安裝 xDsc診斷模組。

在 Azure 電腦上安裝 xDsc診斷模組,請使用[Invoke-AzVMRun 命令](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)。 您還可以在門戶中使用 **「執行」指令**選項,透過您的[Windows VM 中的執行 PowerShell 文稿中的步驟使用執行指令](../../virtual-machines/windows/run-command.md)。

有關使用 xDsc診斷的資訊,請參閱[使用 xDsc 診斷來分析 DSC 紀錄](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)。 另請參考[xDsc 診斷 Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 確保節點與自動化工作區具有所需的模組

DSC取決於節點上安裝的模組。 使用 Azure 自動化狀態配置時,請使用導入模組 中列出的步驟將任何必需[的模組](../shared-resources/modules.md#importing-modules)導入自動化帳戶。 配置還可以依賴於模組的特定版本。 有關詳細資訊,請參閱[故障排除模組](shared-resources.md#modules)。

## <a name="common-errors-when-working-with-dsc"></a>使用 DSC 時的錯誤

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>專案:無法從門戶中移除有特殊字元的設定

#### <a name="issue"></a>問題

試著從門戶中移除 DSC 設定時,您將看到以下錯誤:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此錯誤是計劃要解決的臨時問題。

#### <a name="resolution"></a>解決方案

* 使用 Az Cmdlet"刪除-Az自動化配置" 刪除設定。
* 此 cmdlet 的文檔尚未更新。  在此之前,請參閱 AzureRM 模組的文檔。
  * [移除 AzureRm 自動化DSC設定](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>方案:無法註冊 Dsc 代理

#### <a name="issue"></a>問題

試著`Set-DscLocalConfigurationManager`執行或 DSC cmdlet 時, 您會收到錯誤:

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

#### <a name="cause"></a>原因

此錯誤通常是由防火牆、位於代理伺服器後面的電腦或其他網路錯誤引起的。

#### <a name="resolution"></a>解決方案

驗證電腦有權訪問 Azure 自動化 DSC 的正確終結點,然後重試。 有關所需的連接埠和地址的清單,請參閱[網路規劃](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>方案:狀態報告返回回應代碼"未授權"

#### <a name="issue"></a>問題

使用狀態設定 (DSC) 註冊節點時,您會收到以下錯誤訊息之一:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此問題是由證書損壞或過期引起的。  有關詳細資訊,請參閱[憑證過期和重新註冊](../automation-dsc-onboarding.md#re-registering-a-node)。

此問題也可能由不允許訪問 ***.azure-automation.net**的代理配置引起。 有關詳細資訊,請參閱[專用網路的設定](../automation-dsc-overview.md#network-planning)。 

### <a name="resolution"></a>解決方案

按照下面列出的步驟重新註冊失敗的 DSC 節點。

首先,使用以下步驟取消註冊節點。

1. 從 Azure 門戶,**在家庭** -> **自動化帳戶**下 -> [您的自動化帳戶] ->**狀態配置 (DSC)**
2. 按下"節點",然後單擊遇到故障的節點。
3. 單擊"取消註冊"以取消註冊節點。

其次,從節點卸載 DSC 擴展。

1. 從 Azure 門戶,**在家庭** -> **虛擬機器**-> [失敗節點] ->**擴展**
2. 單擊「微軟.電源外殼.DSC」。。
3. 單擊"卸載",卸載 PowerShell DSC 擴展。

第三,從節點中刪除所有損壞或過期的證書。

在提升的 Powershell 提示器中的故障節點上,執行以下操作:

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

最後,使用以下步驟重新註冊故障節點。

1. 從 Azure 門戶,**在家庭** -> **自動化帳戶**下 -> [您的自動化帳戶] ->**狀態配置 (DSC)**
2. 單擊"節點"。
3. 按下「添加」按鈕。
4. 選擇故障節點。
5. 按下"連接",然後選擇所需的選項。

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>案例：節點處於失敗狀態，並發生「找不到」錯誤

#### <a name="issue"></a>問題

節點的報告具有「失敗」**** 狀態且包含錯誤：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

此錯誤通常發生在將節點指派給設定名稱 (例如 ABC)，而不是指派給節點設定名稱 (例如 ABC.WebServer) 的情況下。

#### <a name="resolution"></a>解決方案

* 請確保為節點分配的是「節點配置名稱」,而不是「設定名稱」。
* 您可以使用 Azure 入口網站或使用 PowerShell Cmdlet，將節點組態指派至節點。

  * 要使用 Azure 門戶將節點配置分配給節點,請打開**DSC 節點**頁面,然後選擇一個節點,然後按一下「**分配節點設定**」按鈕。
  * 要使用 PowerShell cmdlet 將節點設定配置給節點,請使用**Set-AzureRmAutomationDnode** cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>配置:編譯設定時未產生節點設定(MOF 檔案)

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

當 DSC 設定中緊接在 **Node** 關鍵字後面的運算式評估為 `$null` 時，便不會產生任何節點設定。

#### <a name="resolution"></a>解決方案

下列任何一個解決方案都可以修正此問題：

* 確保配置定義中**Node**關鍵字旁邊的運算式未計算為$null。
* 如果您在編譯組態時傳遞 ConfigurationData，請確定您傳遞的是組態向 [ConfigurationData](../automation-dsc-compile.md)要求的預期值。

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>案例：DSC 節點報告會停留在「進行中」狀態

#### <a name="issue"></a>問題

DSC 代理程式輸出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

您已將 WMF 版本升級，且有損毀的 WMI。

#### <a name="resolution"></a>解決方案

要解決此問題,請按照[DSC 已知問題和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)文章中的說明進行操作。

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>方案:無法在 DSC 設定中使用認證

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

您在配置中使用了認證,但沒有提供適當的**配置資料**來為每個節點設定設定**PSDscAllowPlainTextPassword**為 true。

#### <a name="resolution"></a>解決方案

* 請確保在適當的**配置資料**中傳遞,以便針對配置中提到的每個節點配置將**PSDscAllowPlainTextPassword**設置為 true。 有關詳細資訊,請參閱在[Azure 自動化狀態設定中編譯 DSC 設定](../automation-dsc-compile.md)。

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>方案:從 dsc 擴充載入,失敗處理延伸「錯誤

#### <a name="issue"></a>問題

使用 DSC 延伸載入時,將發生包含錯誤的失敗:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

當為節點分配服務中不存在的節點配置名稱時,通常會發生此錯誤。

#### <a name="resolution"></a>解決方案

* 請確保為節點分配的節點具有與服務中的名稱完全匹配的節點配置名稱。
* 您可以選擇不包括節點設定名稱,這會導致節點入入,但不會分配節點設定

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>方案:使用 PowerShell 註冊節點將返回錯誤"發生一個或多個錯誤"

#### <a name="issue"></a>問題

使用`Register-AzAutomationDSCNode``Register-AzureRMAutomationDSCNode`或 註冊節點時,您會收到以下錯誤。

```error
One or more errors occurred.
```

#### <a name="cause"></a>原因

當您嘗試註冊與自動化帳戶分開的訂閱中的節點時,將發生此錯誤。

#### <a name="resolution"></a>解決方案

將跨訂閱節點視為位於單獨的雲中或本地。

按照以下步驟註冊節點。

* Windows -[本地或 Azure/AWS 以外的雲端/虛擬 Windows 電腦](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)。
* Linux -[本地或 Azure 以外的雲端或虛擬 Linux 電腦](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)。

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>方案:錯誤消息 - "預配失敗"

#### <a name="issue"></a>問題

註冊節點時,您會看到錯誤:

```error
Provisioning has failed
```

#### <a name="cause"></a>原因

當節點和 Azure 之間存在連接問題時,將發生此消息。

#### <a name="resolution"></a>解決方案

確定節點是否位於專用虛擬網路中,還是連接到 Azure 時還有其他問題。

有關詳細資訊,請參閱[在加入解決方案時排除錯誤](onboarding.md)。

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>方案:在 Linux 中應用設定,失敗時會出現一般錯誤

#### <a name="issue"></a>問題

在 Linux 中應用設定時,將發生包含錯誤的失敗:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

客戶已確定,如果`/tmp`位置設置為`noexec`,則當前版本的 DSC 將無法應用配置。

#### <a name="resolution"></a>解決方案

* 從`/tmp``noexec`位置中刪除該選項。

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>配置:重疊的節點設定名稱可能會導致發佈錯誤

#### <a name="issue"></a>問題

如果單個配置腳本用於生成多個節點配置,並且某些節點配置的名稱是其他配置的子集,則編譯服務中的問題可能會導致分配錯誤的配置。  僅當使用單個腳本生成每個節點具有配置數據的配置時,並且僅當名稱重疊發生在字串的開頭時,才會發生這種情況。

例如,如果單個配置腳本用於根據使用 cmdlet 作為哈希表傳遞的節點數據生成配置,並且節點數據包括名為「伺服器」和「1server」的伺服器。

#### <a name="cause"></a>原因

編譯服務的已知問題。

#### <a name="resolution"></a>解決方案

最好的解決方法是在本地或 CI/CD 管道中編譯,並將 MOF 檔直接上傳到服務。  如果服務中的編譯是必需的,則下一個最佳解決方法是拆分編譯作業,以便名稱沒有重疊。

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>配置:DSC 設定上載上的閘道逾時錯誤

#### <a name="issue"></a>問題

上傳 DSC 設定`GatewayTimeout`時收到 錯誤。 

#### <a name="cause"></a>原因

編譯時間過長的 DSC 配置可能會導致此錯誤。

#### <a name="resolution"></a>解決方案

通過顯式包含任何`ModuleName``Import-DscResource`調用的參數,可以使 DSC 配置解析得更快。 有關詳細資訊,請參閱[使用導入 DSC 資源](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帳戶連接,通過將 Azure 社區連接到正確的資源(答案、支援和專家),從而改善客戶體驗。
* 如果需要更多協助，您可以提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
