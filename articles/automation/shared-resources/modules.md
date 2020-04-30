---
title: 在 Azure 自動化中管理模組
description: 本文說明如何管理 Azure 自動化中的模組。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d036733c023417af3ef038bb9abc278ec91e665c
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508950"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自動化中管理模組

Azure 自動化可讓您匯入 PowerShell 模組，以在 DSC 設定中啟用 runbook 和 DSC 資源中的 Cmdlet。 Azure 自動化中使用的模組包括：

* [Azure PowerShell Az. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* 其他 PowerShell 模組
* 內部`Orchestrator.AssetManagement.Cmdlets`模組
* Python 2 模組
* 您建立的自訂模組 

當您建立自動化帳戶時，Azure 自動化預設會匯入一些模組。 請參閱[預設模組](#default-modules)。

當 Azure 自動化執行 runbook 和 DSC 編譯工作時，它會將模組載入到可執行 runbook 和編譯 DSC 設定的沙箱中。 自動化也會自動將所有 DSC 資源放在 DSC 提取伺服器上的模組中。 當機器套用 DSC 設定時，可以提取資源。

>[!NOTE]
>請務必只匯入您的 runbook 和 DSC 設定實際需要的模組。 我們不建議匯入根 Az 模組，因為它包含許多您可能不需要的其他模組，這可能會造成效能問題。 請改為匯入個別模組，例如 Az. Compute。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="default-modules"></a>預設模組

下表列出當您建立自動化帳戶時，預設會 Azure 自動化匯入的模組。 自動化可以匯入這些模組的較新版本。 不過，即使您刪除了較新的版本，也無法從您的自動化帳戶移除原始版本。 請注意，這些預設模組包含數個 AzureRM 模組。 

Azure 自動化不會自動將根 Az 模組匯入任何新的或現有的自動化帳戶。 如需使用這些模組的詳細資訊，請參閱[遷移至 Az 模組](#migrating-to-az-modules)。

> [!NOTE]
> 在 Azure 自動化中，我們不建議您在非[時間解決方案中包含啟動/停止 vm](../automation-solution-vm-management.md)的自動化帳戶中改變模組和 runbook。

|模組名稱|版本|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| AssetManagement Cmdlet | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-module-cmdlets"></a>Az module Cmdlet

針對 Az. Automation，大部分的 Cmdlet 與 AzureRM 模組具有相同的名稱，不同之處在于 AzureRm 前置詞已變更為 Az。 如需不遵循此命名慣例的 Az 模組清單，請參閱[例外狀況清單](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

## <a name="internal-cmdlets"></a>內部 Cmdlet

下表定義`Orchestrator.AssetManagement.Cmdlets`模組支援的內部 Cmdlet。 在您的 runbook 和 DSC 設定中使用這些 Cmdlet，以與自動化帳戶內的 Azure 資產進行互動。 Cmdlet 的設計是用來取代 Azure PowerShell Cmdlet，以從加密的變數、認證和加密的連線抓取秘密。 

>[!NOTE]
>只有當您在 Azure 沙箱環境或 Windows 混合式 Runbook 背景工作角色上執行 runbook 時，才可以使用內部 Cmdlet。 

|Name|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|開始-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|等候-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

請注意，內部 Cmdlet 的命名方式與 Az 和 AzureRM Cmdlet 不同。 內部 Cmdlet 名稱不會在名詞中包含 "Azure" 或 "Az" 之類的文字，但請使用 "Automation" 這個字。 我們建議在 Azure 沙箱或 Windows 混合式背景工作角色的 runbook 執行期間，使用 Az 或 AzureRM Cmdlet。 它們需要較少的參數，並在您已執行的作業內容中執行。

建議您使用 Az 或 AzureRM Cmdlet 來操作 runbook 內容以外的 Azure 自動化資源。 

## <a name="orchestratorassetmanagementcmdlets-module"></a>AssetManagement Cmdlet 模組

Azure 自動化支援適用于`Orchestrator.AssetManagement.Cmdlets` Windows 的 Log Analytics 代理程式的內部模組（預設為安裝）。 此`Get-AutomationPSCredential`模組中的 Cmdlet 常用於 runbook 來抓取`PSCredential`物件，這是大部分使用認證的 PowerShell Cmdlet 所預期的。 若要深入瞭解在 Azure 自動化中使用認證的詳細資訊，請參閱[Azure 自動化中的認證資產](credentials.md)。

## <a name="python-modules"></a>Python 模組

您可以在 Azure 自動化中建立 Python 2 runbook。 如需 Python 模組資訊，請參閱[管理 Azure 自動化中的 Python 2 套件](../python-packages.md)。

## <a name="migrating-to-az-modules"></a>遷移至 Az 模組

### <a name="migration-considerations"></a>移轉考量

本節包含在 Azure 自動化中遷移至 Az 模組時要考慮的事項。 另請參閱[將 Azure PowerShell 從 AzureRM 遷移至 Az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0)。 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>在相同的自動化帳戶中使用 AzureRM 模組和 Az 模組

 我們不建議您在相同的自動化帳戶中執行 AzureRM 模組和 Az 模組。 當您確定要從 AzureRM 遷移至 Az 時，最好完全認可完整的遷移。 最重要的原因是 Azure 自動化通常會重複使用自動化帳戶內的沙箱，以節省啟動時間。 如果您未進行完整模組遷移，您可能只會使用 AzureRM 模組來啟動作業，然後只使用 Az 模組來啟動另一個工作。 沙箱很快就會當機，而且您會收到嚴重錯誤，指出模組不相容。 這種情況會導致任何指定的 runbook 或設定隨機發生損毀。 

#### <a name="import-of-az-modules-into-the-powershell-session"></a>將 Az 模組匯入 PowerShell 會話

將 Az 模組匯入到您的自動化帳戶並不會自動將模組匯入 runbook 使用的 PowerShell 會話。 模組會在下列情況下匯入到 PowerShell 工作階段：

* 當 runbook 從模組叫用 Cmdlet 時
* 當 runbook 使用[import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) Cmdlet 明確匯入模組時
* 當 runbook 匯入另一個相依模組時

#### <a name="testing-for-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>在模組遷移之前測試您的 runbook 和 DSC 設定

在遷移至 Az 模組之前，請務必謹慎地在個別的自動化帳戶中測試所有 runbook 和 DSC 設定。 

#### <a name="updates-for-tutorial-runbooks"></a>教學課程 runbook 的更新 

當您建立新的自動化帳戶時，即使在遷移至 Az 模組之後，Azure 自動化預設會安裝 AzureRM 模組。 您仍然可以使用 AzureRM Cmdlet 來更新教學課程 runbook。 不過，您不應該執行這些 runbook。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止並取消排程使用 AzureRM 模組的所有 runbook

為確保您不會執行任何使用 AzureRM 模組的現有 runbook 或 DSC 設定，您必須停止並取消排程所有受影響的 runbook 和設定。 首先，請確定您個別檢查每個 runbook 或 DSC 設定及其排程，以確保您可以在未來視需要重新排定專案。 

當您準備好要移除排程時，可以使用 Azure 入口網站或[new-azurermautomationschedule 指令程式](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0)。 請參閱[移除排程](schedules.md#removing-a-schedule)。

### <a name="remove-the-azurerm-modules"></a>移除 AzureRM 模組

匯入 Az 模組之前，可以先移除 AzureRM 模組。 不過，刪除 AzureRM 模組可能會中斷原始檔控制同步處理，並導致任何仍排定失敗的腳本。 如果您決定移除模組，請參閱[卸載 AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm)。

### <a name="import-the-az-modules"></a>匯入 Az 模組

本節說明如何匯入 Azure 入口網站中的 Az 模組。 請記得只匯入所需的 Az 模組，而不是整個 Az. Automation 模組。 因為[az](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)是其他 Az 模組的相依性，請務必在任何其他模組之前匯入此模組。

1. 從您的自動化帳戶中，選取 [**共用資源**] 底下的 [**模組**]。 
2. 按一下 [瀏覽資源庫]**** 來開啟 [瀏覽資源庫] 頁面。  
3. 在搜尋列中，輸入模組名稱，例如`Az.Accounts`。 
4. 在 [PowerShell 模組] 頁面上，按一下 [匯**入**]，將模組匯入到您的自動化帳戶。

    ![將模組匯入至自動化帳戶](../media/modules/import-module.png)

藉由搜尋要匯入的模組，也可以透過[PowerShell 資源庫](https://www.powershellgallery.com)來完成此匯入程式。 找到模組之後，請選取它，選擇 [ **Azure 自動化**] 索引標籤，然後按一下 [**部署] 以 Azure 自動化**。

![直接從資源庫匯入模組](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>測試您的 Runbook

將 Az 模組匯入到自動化帳戶之後，您就可以開始編輯您的 runbook 和 DSC 設定，以使用新的模組。 若要測試 runbook 的修改以使用新的 Cmdlet，其中一種方式是`Enable-AzureRmAlias -Scope Process`在 runbook 的開頭使用。 藉由將此命令新增至您的 runbook，腳本就可以在沒有變更的情況下執行。 

## <a name="authoring-modules"></a>撰寫模組

當您撰寫 PowerShell 模組以用於 Azure 自動化時，建議您遵循本節中的考慮。

### <a name="version-folder"></a>版本資料夾

請不要在模組的 **.zip**套件中包含版本資料夾。  此問題較不在意 runbook，但會造成狀態設定（DSC）服務發生問題。 當模組散發至 DSC 所管理的節點時，Azure 自動化會自動建立版本資料夾。 如果版本資料夾存在，最後會有兩個實例。 以下是 DSC 模組的範例資料夾結構：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>說明資訊

在模組中包含每個 Cmdlet 的概要、描述和說明 URI。 在 PowerShell 中，您可以使用`Get-Help` Cmdlet 定義 Cmdlet 的說明資訊。 下列範例顯示如何在 **.psm1**模組檔案中定義概要和說明 URI。

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  提供這項資訊會透過 PowerShell 主控台`Get-Help`中的 Cmdlet 來顯示解說文字。 此文字也會顯示在 Azure 入口網站中。

  ![整合模組說明](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>連線類型

如果模組連接至外部服務，請定義[連線類型](#adding-a-connection-type-to-your-module)。 模組中的每個 Cmdlet 都應該接受連線物件（該連線類型的實例）做為參數。 使用者每次呼叫 Cmdlet 時，都會將連線資產的參數對應至 Cmdlet 的相對應參數。 下列 runbook 範例根據上一節中的範例，會使用名`ContosoConnection`為的 contoso 連線資產來存取 contoso 資源，並從外部服務傳回資料。 在此範例中，欄位會對應至`UserName` `Password` `PSCredential`物件的和屬性，然後傳遞至 Cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  方法是直接將連線物件傳遞給 Cmdlet，是更容易且更好的方式來處理這種行為：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  您可以啟用 Cmdlet 的類似行為，方法是允許它們直接接受連線物件做為參數，而不只是參數的連接欄位。 通常您會想讓每個 Cmdlet 都設定參數，讓未使用 Azure 自動化的使用者可以直接呼叫 Cmdlet，而不必建構雜湊表來作為連線物件。 參數集`UserAccount`是用來傳遞連接欄位屬性。 `ConnectionObject`可讓您直接透過傳遞連接。

### <a name="output-type"></a>輸出類型

定義模組中所有 Cmdlet 的輸出類型。 定義 Cmdlet 的輸出類型可讓設計階段 IntelliSense 在撰寫期間協助判斷 Cmdlet 的輸出屬性。 這種作法在圖形化 runbook 撰寫期間特別有用，其設計階段知識是讓您的模組輕鬆使用者體驗的關鍵。

加入`[OutputType([<MyOutputType>])]` ， `MyOutputType`其中是有效的類型。 若要深入瞭解`OutputType`，請參閱[關於函數 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下列程式碼是新增`OutputType`至 Cmdlet 的範例：

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![圖形化 Runbook 輸出類型](../media/modules/runbook-graphical-module-output-type.png)

  此行為類似於 Cmdlet 在 PowerShell ISE 中的輸出的「自動提示」功能，但不需要加以執行。

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet 狀態

將模組中的所有 Cmdlet 設為無狀態。 多個 runbook 作業可以同時在相同`AppDomain`和相同的進程和沙箱中執行。 如果這些層級上有任何共用的狀態，則作業可能會互相影響。 這種行為可能會導致間歇性且難以診斷的問題。 以下是不應執行之動作的範例：

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>模組相依性

請確定模組完全包含在可使用 xcopy 複製的封裝中。 當 runbook 執行時，Azure 自動化模組會散發至自動化沙箱。 模組必須獨立于執行它們的主機上運作。 

在匯入至另一個主機的 PowerShell 環境時，您應該能夠壓縮並移動模組套件，並使其正常運作。 若要進行這種情況，請確定模組不會相依于模組匯入 Azure 自動化時所壓縮模組資料夾以外的任何檔案。 

您的模組不應相依于主機上的任何唯一登錄設定。 範例是安裝產品時所進行的設定。 

### <a name="module-file-paths"></a>模組檔案路徑

請確定模組中的所有檔案都有少於140個字元的路徑。 長度超過140個字元的路徑會導致匯入 runbook 的問題。 Azure 自動化無法使用`Import-Module`將路徑大小超過140個字元的檔案匯入 PowerShell 會話。

## <a name="importing-modules"></a>匯入模組

本節定義數種方式，可讓您將模組匯入至自動化帳戶。 

### <a name="import-modules-in-azure-portal"></a>匯入 Azure 入口網站中的模組

若要匯入 Azure 入口網站中的模組：

1. 瀏覽至您的自動化帳戶。
2. 選取 [**共用資源**] 底下的 [**模組**]。
3. 按一下 [**新增模組**]。 
4. 選取包含模組的 **.zip**檔案。
5. 按一下 **[確定]** 開始匯入處理常式。

### <a name="import-modules-using-powershell"></a>使用 PowerShell 匯入模組

您可以使用[AzAutomationModule 指令程式](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0)，將模組匯入到您的自動化帳戶。 此 Cmdlet 會取得模組 .zip 套件的 URL。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您也可以使用相同的 Cmdlet 直接從 PowerShell 資源庫匯入模組。 請務必從`ModuleName` [PowerShell 資源庫](https://www.powershellgallery.com)抓取`ModuleVersion`和。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>從 PowerShell 資源庫匯入模組

您可以直接從資源庫或您的自動化帳戶匯入[PowerShell 資源庫](https://www.powershellgallery.com)模組。

若要直接從 PowerShell 資源庫匯入模組：

1. 移至https://www.powershellgallery.com並搜尋模組以進行匯入。
2. 按一下 [**安裝選項**] 底下 [ **Azure 自動化**] 索引標籤上的 [**部署] Azure 自動化**。 此動作會開啟 [Azure 入口網站]。 
3. 在 [匯入] 頁面上選取您的自動化帳戶，然後按一下 [確定]  。

![PowerShell 資源庫匯入模組](../media/modules/powershell-gallery.png)

若要直接從您的自動化帳戶匯入 PowerShell 資源庫模組：

1. 選取 [**共用資源**] 底下的 [**模組**]。 
2. 在 [模組] 頁面上，按一下 [**流覽資源庫]**，然後搜尋資源庫中的模組。 
3. 選取要匯入的模組，然後按一下 [匯**入**]。 
4. 在 [匯入] 頁面上，按一下 **[確定]** 以啟動匯入程式。

![PowerShell 資源庫從 Azure 入口網站匯入](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>刪除模組

如果您有模組的問題，或需要復原到舊版的模組，您可以從您的自動化帳戶中將它刪除。 當您建立自動化帳戶時，無法刪除已匯入之[預設模組](#default-modules)的原始版本。 如果要刪除的模組是其中一個[預設模組](#default-modules)的較新版本，則會回復為隨您的自動化帳戶一起安裝的版本。 否則，會移除您從自動化帳戶中刪除的任何模組。

### <a name="delete-modules-in-azure-portal"></a>刪除 Azure 入口網站中的模組

若要移除 Azure 入口網站中的模組：

1. 流覽至您的自動化帳戶，然後選取 [**共用資源**] 底下的 [**模組**]。 
2. 選取您要移除的模組。 
3. 在 [**模組**] 頁面上，選取 [**刪除**]。 如果此模組是其中一個[預設模組](#default-modules)，它會回復為建立自動化帳戶時所存在的版本。

### <a name="delete-modules-using-powershell"></a>使用 PowerShell 刪除模組

若要透過 PowerShell 移除模組，請執行下列命令：

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>將連線類型新增至您的模組

您可以在您的模組中新增選擇性的中繼資料檔案，以提供要在自動化帳戶中使用的自訂連線[類型](../automation-connections.md)。 此檔案會指定要在您的自動化帳戶中與模組的 Cmdlet 搭配使用的 Azure 自動化連線類型。 若要深入瞭解撰寫 PowerShell 模組，請參閱[如何撰寫 Powershell 腳本模組](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)。

![在 Azure 入口網站中使用自訂連接](../media/modules/connection-create-new.png)

指定連線類型屬性的檔案會命名** &lt;為&gt;ModuleName-Automation. json** ，並在您壓縮的 **.zip**檔案的模組資料夾中找到。 此檔案包含連接到模組所代表的系統或服務所需的連線欄位。 此設定允許在 Azure 自動化中建立連線類型。 您可以使用這個檔案來設定功能變數名稱、類型，以及欄位是否已加密，或是否為模組的連線類型選擇是否為選擇性。 下列範例是定義使用者名稱和密碼屬性之**json**檔案格式的範本：

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>後續步驟

* 如需使用 Azure PowerShell 模組的詳細資訊，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)。
* 若要深入瞭解如何建立 PowerShell 模組，請參閱[撰寫 Windows Powershell 模組](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)。
