---
title: 在 Azure 自動化中管理模組
description: Azure 自動化可讓您匯入 PowerShell 模組，以在 DSC 設定中啟用 runbook 和 DSC 資源中的 Cmdlet。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84fdb5a9cf3c22048473cd00ee6f8e7ac36c9097
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864295"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自動化中管理模組

Azure 自動化可讓您匯入 PowerShell 模組，以在 DSC 設定中啟用 runbook 和 DSC 資源中的 Cmdlet。 Azure 自動化中使用的模組包括：

* [Azure PowerShell Az. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。
* [Azure PowerShell AzureRM](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)。
* 其他 PowerShell 模組。
* 內部`Orchestrator.AssetManagement.Cmdlets`模組。
* Python 2 模組。
* 您所建立的自訂模組。

當您建立自動化帳戶時，Azure 自動化預設會匯入一些模組。 請參閱[預設模組](#default-modules)。

當自動化執行 runbook 和 DSC 編譯工作時，它會將模組載入沙箱中，runbook 可以在其中執行，而 DSC 設定可以編譯。 自動化也會自動將所有 DSC 資源放在 DSC 提取伺服器上的模組中。 當機器套用 DSC 設定時，可以提取資源。

>[!NOTE]
>請務必只匯入您的 runbook 和 DSC 設定實際需要的模組。 我們不建議您匯入根 Az 模組。 其中包含許多您可能不需要的其他模組，這可能會造成效能問題。 請改為匯入個別模組，例如 Az. Compute。

>[!NOTE]
>本文使用 Azure PowerShell Az 模組。 您仍然可以使用 AzureRM 模組。 若要深入瞭解 Az 模組和 AzureRM 相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="default-modules"></a>預設模組

下表列出當您建立自動化帳戶時，預設會 Azure 自動化匯入的模組。 自動化可以匯入這些模組的較新版本。 不過，即使您刪除了較新的版本，也無法從您的自動化帳戶移除原始版本。 請注意，這些預設模組包含數個 AzureRM 模組。 

Automation 不會自動將根 Az 模組匯入任何新的或現有的自動化帳戶。 如需使用這些模組的詳細資訊，請參閱[遷移至 Az 模組](#migrating-to-az-modules)。

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

## <a name="az-modules"></a>Az 模組

針對 Az. Automation，大部分的 Cmdlet 與用於 AzureRM 模組的指令程式具有相同的名稱，不同之處在于*AzureRM*前置詞已變更為*Az*。 如需不遵循此命名慣例的 Az 模組清單，請參閱[例外狀況清單](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

## <a name="internal-cmdlets"></a>內部 Cmdlet

Azure 自動化支援適用于`Orchestrator.AssetManagement.Cmdlets` Windows 的 Log Analytics 代理程式的內部模組（預設為安裝）。 下表定義了內部 Cmdlet。 這些 Cmdlet 是設計用來取代 Azure PowerShell Cmdlet，以與共用資源互動。 他們可以從加密的變數、認證和加密的連線抓取秘密。

>[!NOTE]
>只有當您要在 Azure 沙箱環境或 Windows 混合式 Runbook 背景工作角色上執行 runbook 時，才可以使用內部 Cmdlet。 

|名稱|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|開始-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|等候-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

請注意，內部 Cmdlet 的命名方式與 Az 和 AzureRM Cmdlet 不同。 內部 Cmdlet 名稱不會在名詞中包含 "Azure" 或 "Az" 之類的文字，但請使用「*自動化*」一詞。 我們建議在 Azure 沙箱或 Windows 混合式 Runbook 背景工作角色中執行 runbook 期間，使用 Az 或 AzureRM Cmdlet。 它們需要較少的參數，並在已執行的作業內容中執行。

使用 Az 或 AzureRM Cmdlet 來操作 runbook 內容以外的自動化資源。 

## <a name="python-modules"></a>Python 模組

您可以在 Azure 自動化中建立 Python 2 runbook。 如需 Python 模組資訊，請參閱[管理 Azure 自動化中的 Python 2 套件](../python-packages.md)。

## <a name="custom-modules"></a>自訂模組

Azure 自動化支援您建立來搭配 runbook 和 DSC 設定使用的自訂 PowerShell 模組。 其中一種自訂模組是一種整合模組，可選擇性地包含中繼資料的檔案，以定義模組 Cmdlet 的自訂功能。 [[新增連線類型](../automation-connections.md#add-a-connection-type)] 中提供整合模組使用的範例。

Azure 自動化可以匯入自訂模組，使其 Cmdlet 可供使用。 在幕後，它會儲存模組，並在 Azure 沙箱中使用它，就像它是其他模組一樣。

## <a name="migrating-to-az-modules"></a>遷移至 Az 模組

### <a name="migration-considerations"></a>移轉考量

本節包含當您要遷移至自動化中的 Az 模組時要考慮的事項。 如需詳細資訊，請參閱[將 Azure PowerShell 從 AzureRM 遷移至 Az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0)。 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>在相同的自動化帳戶中使用 AzureRM 模組和 Az 模組

 我們不建議您在相同的自動化帳戶中執行 AzureRM 模組和 Az 模組。 當您確定要從 AzureRM 遷移至 Az 時，最好完全認可完整的遷移。 自動化通常會重複使用自動化帳戶內的沙箱，以節省啟動時間。 如果您未進行完整模組遷移，您可能會啟動僅使用 AzureRM 模組的作業，然後啟動另一個只使用 Az 模組的作業。 沙箱很快就會當機，而且您會收到錯誤，指出模組不相容。 這種情況會導致任何特定 runbook 或設定隨機發生損毀。 

#### <a name="importing-az-modules-into-the-powershell-session"></a>將 Az 模組匯入 PowerShell 會話

將 Az 模組匯入到您的自動化帳戶並不會自動將模組匯入 runbook 使用的 PowerShell 會話。 模組會在下列情況下匯入到 PowerShell 工作階段：

* 當 runbook 從模組叫用 Cmdlet 時。
* 當 runbook 使用[import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) Cmdlet 明確匯入模組時。
* 當 runbook 匯入另一個相依模組時。

#### <a name="testing-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>在模組遷移之前測試 runbook 和 DSC 設定

在遷移至 Az 模組之前，請務必謹慎地在個別的自動化帳戶中測試所有 runbook 和 DSC 設定。 

#### <a name="updates-for-tutorial-runbooks"></a>教學課程 runbook 的更新 

當您建立新的自動化帳戶時，即使在遷移至 Az 模組之後，自動化還是會預設安裝 AzureRM 模組。 您仍然可以使用 AzureRM Cmdlet 來更新教學課程 runbook。 不過，您不應該執行這些 runbook。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止並取消排程使用 AzureRM 模組的所有 runbook

為確保您不會執行任何使用 AzureRM 模組的現有 runbook 或 DSC 設定，您必須停止並取消排程所有受影響的 runbook 和設定。 首先，請確定您分別檢查每個 runbook 或 DSC 設定及其排程，以確保您可以在未來視需要重新排定專案。 

當您準備好要移除排程時，可以使用 Azure 入口網站或[new-azurermautomationschedule 指令程式](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0)。 請參閱[移除排程](schedules.md#remove-a-schedule)。

### <a name="remove-the-azurerm-modules"></a>移除 AzureRM 模組

您可以先移除 AzureRM 模組，再匯入 Az 模組。 不過，如果您這樣做，您可以中斷原始檔控制同步處理，並導致任何仍排定失敗的腳本。 如果您決定移除模組，請參閱[卸載 AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm)。

### <a name="import-the-az-modules"></a>匯入 Az 模組

您可以在 Azure 入口網站中匯入 Az 模組。 請記得只匯入所需的 Az 模組，而不是整個 Az. Automation 模組。 因為[az. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)是其他 Az 模組的相依性，請務必在任何其他模組之前匯入此模組。

1. 從您的自動化帳戶的 [**共用資源**] 底下，選取 [**模組**]。 
2. 選取 **[流覽資源庫]**。  
3. 在搜尋列中，輸入模組名稱（例如， `Az.Accounts`）。 
4. 在 [ **PowerShell 模組**] 頁面上，選取 [匯**入**]，將模組匯入到您的自動化帳戶。

    ![將模組匯入至自動化帳戶的螢幕擷取畫面](../media/modules/import-module.png)

您也可以透過[PowerShell 資源庫](https://www.powershellgallery.com)來執行這項匯入，方法是搜尋要匯入的模組。 當您找到模組時，請加以選取，然後選擇 [ **Azure 自動化**] 索引標籤。選取 [**部署] 以 Azure 自動化**。

![直接從 PowerShell 資源庫匯入模組的螢幕擷取畫面](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>測試您的 Runbook

將 Az 模組匯入到自動化帳戶之後，您就可以開始編輯您的 runbook 和 DSC 設定，以使用新的模組。 測試 runbook 修改以使用新 Cmdlet 的其中一種方式，是使用 runbook 開頭的`Enable-AzureRmAlias -Scope Process`命令。 藉由將此命令新增至您的 runbook，腳本就可以在沒有變更的情況下執行。 

## <a name="author-modules"></a>撰寫模組

當您撰寫自訂 PowerShell 模組以用於 Azure 自動化時，建議您遵循本節中的考慮。 若要準備模組以進行匯入，您必須至少建立一個具有與模組資料夾相同名稱的 .psd1、.psm1 或 PowerShell 模組 **.dll**檔案。 然後壓縮模組資料夾，讓 Azure 自動化可以將它匯入為單一檔案。 **.Zip**套件的名稱應該與包含的模組資料夾相同。 

若要深入瞭解撰寫 PowerShell 模組，請參閱[如何撰寫 Powershell 腳本模組](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)。

### <a name="version-folder"></a>版本資料夾

請不要在模組的 **.zip**套件中包含版本資料夾。 此問題較不在意 runbook，但會造成狀態設定（DSC）服務發生問題。 當模組散發至由狀態設定管理的節點時，Azure 自動化會自動建立版本資料夾。 如果版本資料夾存在，最後會有兩個實例。 以下是 DSC 模組的範例資料夾結構：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>說明資訊

在模組中包含每個 Cmdlet 的概要、描述和說明 URI。 在 PowerShell 中，您可以使用`Get-Help` Cmdlet 來定義 Cmdlet 的說明資訊。 下列範例顯示如何在 **.psm1**模組檔案中定義概要和說明 URI。

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

  ![整合模組說明的螢幕擷取畫面](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>連線類型

如果模組連接至外部服務，請使用[自訂整合模組](#custom-modules)來定義連線類型。 模組中的每個 Cmdlet 都應該接受該連線類型（連線物件）的實例作為參數。 使用者每次呼叫 Cmdlet 時，都會將連線資產的參數對應至 Cmdlet 的相對應參數。 

![在 Azure 入口網站中使用自訂連接](../media/modules/connection-create-new.png)

下列 runbook 範例會使用名`ContosoConnection`為的 contoso 連接資產來存取 contoso 資源，並從外部服務傳回資料。 在此範例中，欄位會對應至`UserName` `Password` `PSCredential`物件的和屬性，然後傳遞至 Cmdlet。

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

您可以啟用 Cmdlet 的類似行為，方法是允許它們直接接受連線物件做為參數，而不只是參數的連接欄位。 您通常會想要為每個設定參數，如此一來，未使用 Automation 的使用者就可以呼叫您的 Cmdlet，而不需要將雜湊表視為連線物件。 參數集`UserAccount`是用來傳遞連接欄位屬性。 `ConnectionObject`可讓您直接透過傳遞連接。

### <a name="output-type"></a>輸出類型

定義模組中所有 Cmdlet 的輸出類型。 定義 Cmdlet 的輸出類型可讓設計階段 IntelliSense 在撰寫期間協助判斷 Cmdlet 的輸出屬性。 這種作法在圖形化 runbook 撰寫期間特別有用，其設計階段知識是讓您的模組輕鬆使用者體驗的關鍵。

Add `[OutputType([<MyOutputType>])]`，其中`MyOutputType`是有效的型別。 若要深入瞭解`OutputType`，請參閱[關於函數 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下列程式碼是新增`OutputType`至 Cmdlet 的範例：

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

  ![圖形化 runbook 輸出類型的螢幕擷取畫面](../media/modules/runbook-graphical-module-output-type.png)

  此行為類似于 PowerShell 整合服務環境中 Cmdlet 輸出的「事先輸入」功能，而不需要執行它。

  ![POSH IntelliSense 的螢幕擷取畫面](../media/modules/automation-posh-ise-intellisense.png)

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

請確定模組完全包含在可使用 xcopy 複製的封裝中。 自動化模組會在 runbook 執行時散發至自動化沙箱。 模組必須獨立于執行它們的主機上運作。 

您應該能夠壓縮並移動模組套件，並讓它在匯入至另一個主機的 PowerShell 環境時正常運作。 若要這樣做，請確定模組不會相依于模組匯入自動化時所壓縮模組資料夾以外的任何檔案。 

您的模組不應相依于主機上的任何唯一登錄設定。 範例是安裝產品時所進行的設定。 

### <a name="module-file-paths"></a>模組檔案路徑

請確定模組中的所有檔案都有少於140個字元的路徑。 長度超過140個字元的路徑會導致匯入 runbook 的問題。 自動化無法使用`Import-Module`將路徑大小超過140個字元的檔案匯入 PowerShell 會話。

## <a name="import-modules"></a>匯入模組

本節定義數種方式，可讓您將模組匯入至自動化帳戶。 

### <a name="import-modules-in-the-azure-portal"></a>匯入 Azure 入口網站中的模組

若要匯入 Azure 入口網站中的模組：

1. 移至自動化帳戶。
2. 在 [**共用資源**] 底下，選取 [**模組**]。
3. 選取 [**新增模組**]。 
4. 選取包含模組的 **.zip**檔案。
5. 選取 **[確定]** 以開始匯入處理常式。

### <a name="import-modules-by-using-powershell"></a>使用 PowerShell 匯入模組

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

### <a name="import-modules-from-the-powershell-gallery"></a>從 PowerShell 資源庫匯入模組

您可以直接從資源庫或您的自動化帳戶匯入[PowerShell 資源庫](https://www.powershellgallery.com)模組。

若要直接從 PowerShell 資源庫匯入模組：

1. 移至https://www.powershellgallery.com並搜尋模組以進行匯入。
2. 在 [**安裝選項**] 下的 [ **Azure 自動化**] 索引標籤上，選取 [**部署至 Azure 自動化**]。 此動作會開啟 [Azure 入口網站]。 
3. 在 [匯**入**] 頁面上選取您的自動化帳戶，然後選取 **[確定]**。

![PowerShell 資源庫匯入模組的螢幕擷取畫面](../media/modules/powershell-gallery.png)

若要直接從您的自動化帳戶匯入 PowerShell 資源庫模組：

1. 在 [**共用資源**] 底下，選取 [**模組**]。 
2. 選取 **[流覽資源庫]**，然後在資源庫中搜尋模組。 
3. 選取要匯入的模組，然後選取 [匯**入**]。 
4. 選取 **[確定]** 以啟動匯入程式。

![從 Azure 入口網站匯入 PowerShell 資源庫模組的螢幕擷取畫面](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>刪除模組

如果您有模組的問題，或需要復原到舊版的模組，您可以從您的自動化帳戶中將它刪除。 當您建立自動化帳戶時，無法刪除已匯入之[預設模組](#default-modules)的原始版本。 如果要刪除的模組是其中一個[預設模組](#default-modules)的較新版本，則會回復為隨您的自動化帳戶一起安裝的版本。 否則，會移除您從自動化帳戶中刪除的任何模組。

### <a name="delete-modules-in-the-azure-portal"></a>刪除 Azure 入口網站中的模組

若要移除 Azure 入口網站中的模組：

1. 移至自動化帳戶。 在 [**共用資源**] 底下，選取 [**模組**]。 
2. 選取您要移除的模組。 
3. 在 [**模組**] 頁面上，選取 [**刪除**]。 如果此模組是其中一個[預設模組](#default-modules)，它會回復為建立自動化帳戶時所存在的版本。

### <a name="delete-modules-by-using-powershell"></a>使用 PowerShell 刪除模組

若要透過 PowerShell 移除模組，請執行下列命令：

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>後續步驟

* 如需使用 Azure PowerShell 模組的詳細資訊，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)。
* 若要深入瞭解如何建立 PowerShell 模組，請參閱[撰寫 Windows powershell 模組](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)。
