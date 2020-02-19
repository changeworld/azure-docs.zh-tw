---
title: 針對 Azure 自動化 Desired State Configuration （DSC）進行疑難排解
description: 本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dcd0371d275c3a46fe9bf07c96516a2d0820abb7
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430528"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>針對 Azure 自動化 Desired State Configuration （DSC）的問題進行疑難排解

本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊。

## <a name="diagnosing-an-issue"></a>診斷問題

當您在 Azure 狀態設定中編譯或部署設定發生錯誤時，以下幾個步驟可協助您診斷問題。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 確定您的設定在本機電腦上成功編譯

Azure 狀態設定建置於 PowerShell DSC 上。 您可以在[POWERSHELL dsc](https://docs.microsoft.com/powershell/scripting/overview)檔中找到 DSC 語言和語法的檔。

藉由在本機電腦上編譯 DSC 設定，您可以探索並解決常見的錯誤，例如：

   - 遺失的模組
   - 語法錯誤
   - 邏輯錯誤

### <a name="2-view-dsc-logs-on-your-node"></a>2. 在您的節點上查看 DSC 記錄

如果您的設定成功編譯，但套用至節點時失敗，您可以在 DSC 記錄檔中找到詳細資訊。 如需有關如何尋找這些記錄檔的詳細資訊，請參閱[DSC 事件記錄檔的位置](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[XDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics)模組可協助您剖析來自 DSC 記錄檔的詳細資訊。 如果您聯繫支援人員，他們需要這些記錄來診斷您的問題。

您可以使用[安裝穩定版本模組](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中找到的指示，在本機電腦上安裝 xDscDiagnostics 模組。

若要在您的 Azure 電腦上安裝 xDscDiagnostics 模組，請使用[AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)。 您也可以[依照在具有執行命令的 WINDOWS VM 中執行 PowerShell 腳本](../../virtual-machines/windows/run-command.md)中的步驟，使用入口網站中的 [**執行命令**] 選項。

如需使用 xDscDiagnostics 的詳細資訊，請參閱[使用 xDscDiagnostics 分析 DSC 記錄](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)檔。 另請參閱[XDscDiagnostics Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 確定節點和自動化工作區具有必要的模組

DSC 取決於節點上安裝的模組。 使用 Azure 自動化狀態設定時，請使用匯[入模組](../shared-resources/modules.md#import-modules)中列出的步驟，將任何必要的模組匯入到您的自動化帳戶。 設定也可能相依于特定版本的模組。 如需詳細資訊，請參閱針對[模組進行疑難排解](shared-resources.md#modules)。

## <a name="common-errors-when-working-with-dsc"></a>使用 DSC 時的常見錯誤

### <a name="unsupported-characters"></a>案例：無法從入口網站刪除具有特殊字元的設定

#### <a name="issue"></a>問題

嘗試從入口網站刪除 DSC 設定時，您會看到下列錯誤：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此錯誤是計畫要解決的暫時性問題。

#### <a name="resolution"></a>解決方案

* 使用 Az Cmdlet "Remove-AzAutomationDscConfiguration" 來刪除設定。
* 此 Cmdlet 的檔尚未更新。  在那之前，請參閱 AzureRM 模組的檔。
  * [移除-Export-azurermautomationdscconfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>案例：無法註冊 Dsc 代理程式

#### <a name="issue"></a>問題

嘗試執行 `Set-DscLocalConfigurationManager` 或另一個 DSC Cmdlet 時，您會收到錯誤：

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

此錯誤通常是由防火牆、位於 proxy 伺服器後方的電腦，或其他網路錯誤所造成。

#### <a name="resolution"></a>解決方案

請確認您的電腦可存取適當的 Azure 自動化 DSC 端點，然後再試一次。 如需所需的埠和地址清單，請參閱[網路規劃](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>案例：狀態報表傳迴響應碼「未經授權」

#### <a name="issue"></a>問題

向狀態設定（DSC）註冊節點時，您會收到下列其中一則錯誤訊息：

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此問題是因為憑證錯誤或過期所造成。  如需詳細資訊，請參閱[憑證到期和重新註冊](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration)。

### <a name="resolution"></a>解決方案

請遵循下列步驟來重新註冊失敗的 DSC 節點。

首先，請使用下列步驟取消註冊節點。

1. 從 [Azure 入口網站] 的 [ **Home** -> **自動化帳戶**] 底下-> {您的自動化帳戶}->**狀態設定（DSC）**
2. 按一下 [節點]，然後按一下發生問題的節點。
3. 按一下 [取消註冊] 以取消註冊節點。

第二，從節點卸載 DSC 延伸模組。

1. 從 Azure 入口網站的**Home** -> **虛擬機器**-> {失敗的節點}->**擴充**功能
2. 按一下 [Microsoft Powershell]。
3. 按一下 [卸載] 以卸載 PowerShell DSC 延伸模組。

第三，從節點移除所有錯誤或過期的憑證。

在 [失敗] 節點上，從提升許可權的 Powershell 提示字元執行下列命令：

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

最後，使用下列步驟重新註冊失敗的節點。

1. 從 [Azure 入口網站] 的 [ **Home** -> **自動化帳戶**] 底下-> {您的自動化帳戶}->**狀態設定（DSC）**
2. 按一下 [節點]。
3. 按一下 [新增] 按鈕。
4. 選取失敗的節點。
5. 按一下 [連線]，然後選取您想要的選項。

### <a name="failed-not-found"></a>案例：節點處於失敗狀態，並發生「找不到」錯誤

#### <a name="issue"></a>問題

節點的報告具有「失敗」狀態且包含錯誤：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

此錯誤通常發生在將節點指派給設定名稱 (例如 ABC)，而不是指派給節點設定名稱 (例如 ABC.WebServer) 的情況下。

#### <a name="resolution"></a>解決方案

* 請確定您指派的節點具有「節點設定名稱」，而不是「設定名稱」。
* 您可以使用 Azure 入口網站或使用 PowerShell Cmdlet，將節點組態指派至節點。

  * 若要使用 Azure 入口網站將節點設定指派給節點，請開啟 [ **DSC 節點**] 頁面，然後選取一個節點，再按一下 [**指派節點**設定] 按鈕。
  * 若要使用 PowerShell Cmdlet 將節點設定指派給節點，請使用**unregister-azurermautomationdscnode** Cmdlet

### <a name="no-mof-files"></a>案例：編譯設定時，沒有產生任何節點設定 (MOF 檔案)

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

當 DSC 設定中緊接在 **Node** 關鍵字後面的運算式評估為 `$null` 時，便不會產生任何節點設定。

#### <a name="resolution"></a>解決方案

下列任何一個解決方案都可以修正此問題：

* 請確定設定定義中**Node**關鍵字旁的運算式未評估為 $null。
* 如果您在編譯組態時傳遞 ConfigurationData，請確定您傳遞的是組態向 [ConfigurationData](../automation-dsc-compile.md)要求的預期值。

### <a name="dsc-in-progress"></a>案例：DSC 節點報告變成停留在「進行中」狀態

#### <a name="issue"></a>問題

DSC 代理程式輸出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

您已將 WMF 版本升級，且有損毀的 WMI。

#### <a name="resolution"></a>解決方案

若要修正此問題，請依照[DSC 已知問題和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)一文中的指示進行。

### <a name="issue-using-credential"></a>案例：無法在 DSC 設定中使用認證

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

您已在設定中使用認證，但未提供適當的**ConfigurationData** ，將每個節點設定的**PSDscAllowPlainTextPassword**設為 true。

#### <a name="resolution"></a>解決方案

* 請務必傳入適當的**ConfigurationData** ，以針對設定中所述的每個節點設定，將**PSDscAllowPlainTextPassword**設為 true。 如需詳細資訊，請參閱[在 Azure 自動化狀態設定中編譯 DSC](../automation-dsc-compile.md)設定。

### <a name="failure-processing-extension"></a>案例：從 dsc 延伸模組上線，「失敗處理延伸模組」錯誤

#### <a name="issue"></a>問題

當使用 DSC 延伸模組上線時，會發生失敗，其中包含錯誤：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

當節點指派的節點設定名稱不存在於服務中時，通常會發生此錯誤。

#### <a name="resolution"></a>解決方案

* 請確定您所指派的節點的節點設定名稱與服務中的名稱完全相符。
* 您可以選擇不包含節點設定名稱，這會導致節點上架，但不會指派節點設定

### <a name="cross-subscription"></a>案例：使用 PowerShell 註冊節點會傳回錯誤「發生一或多個錯誤」

#### <a name="issue"></a>問題

使用 `Register-AzAutomationDSCNode` 或 `Register-AzureRMAutomationDSCNode`註冊節點時，您會收到下列錯誤。

```error
One or more errors occurred.
```

#### <a name="cause"></a>原因

當您嘗試註冊的節點與自動化帳戶位於不同的訂用帳戶時，就會發生此錯誤。

#### <a name="resolution"></a>解決方案

將跨訂用帳戶節點視為位於不同的雲端或內部部署。

請遵循下列步驟來註冊節點。

* Windows-[內部部署或 Azure/AWS 以外的雲端中的實體/虛擬 windows 機器](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)。
* Linux-[內部部署或 Azure 以外之雲端中的實體/虛擬 Linux 機器](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)。

### <a name="agent-has-a-problem"></a>案例：錯誤訊息-「布建失敗」

#### <a name="issue"></a>問題

註冊節點時，您會看到錯誤：

```error
Provisioning has failed
```

#### <a name="cause"></a>原因

當節點與 Azure 之間有連線問題時，就會出現此訊息。

#### <a name="resolution"></a>解決方案

判斷您的節點是否在私人虛擬網路中，或是否有其他連線到 Azure 的問題。

如需詳細資訊，請參閱[在上架解決方案時針對錯誤進行疑難排解](onboarding.md)。

### <a name="failure-linux-temp-noexec"></a>案例：在 Linux 中套用設定，發生一般錯誤時失敗

#### <a name="issue"></a>問題

在 Linux 中套用設定時，會發生失敗，其中包含錯誤：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

客戶已發現，如果 `/tmp` 位置設定為 `noexec`，DSC 的目前版本將無法套用設定。

#### <a name="resolution"></a>解決方案

* 從 `/tmp` 位置移除 [`noexec`] 選項。

### <a name="compilation-node-name-overlap"></a>案例：重迭的節點設定名稱可能會導致錯誤的版本

#### <a name="issue"></a>問題

如果使用單一設定腳本來產生多個節點設定，而某些節點設定的名稱是其他專案的子集，則編譯服務中的問題可能會導致指派錯誤的設定。  只有在使用單一腳本來產生具有每個節點之設定資料的設定，以及在字串開頭髮生名稱重迭時，才會發生這種情況。

例如，如果使用單一設定腳本，根據使用 Cmdlet 當做雜湊表傳遞的節點資料來產生設定，則節點資料會包含名為 "server" 和 "1server" 的伺服器。

#### <a name="cause"></a>原因

編譯服務的已知問題。

#### <a name="resolution"></a>解決方案

最佳的解決方法是在本機或在 CI/CD 管線中進行編譯，並將 MOF 檔案直接上傳至服務。  如果服務中的編譯是需求，則下一個最佳的解決方法是分割編譯工作，讓名稱中沒有任何重迭。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
