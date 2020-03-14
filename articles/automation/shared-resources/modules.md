---
title: 管理 Azure 自動化中的模組
description: 本文說明如何在中管理模組 Azure 自動化
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278333"
---
# <a name="manage-modules-in-azure-automation"></a>管理 Azure 自動化中的模組

Azure 自動化提供將 PowerShell 模組匯入到您的自動化帳戶，以供 PowerShell 型 runbook 使用的功能。 這些模組可以是您從 PowerShell 資源庫建立的自訂模組，或是適用于 Azure 的 AzureRM 和 Az 模組。 當您建立自動化帳戶時，預設會匯入某些模組。

## <a name="import-modules"></a>匯入模組

有多種方式可讓您將模組匯入到您的自動化帳戶。 下列各節說明匯入模組的不同方式。

> [!NOTE]
> 模組中要用於 Azure 自動化的檔案路徑上限為140個字元。 任何超過140個字元的路徑都無法使用 `Import-Module`匯入至 PowerShell 會話。

### <a name="powershell"></a>PowerShell

您可以使用[new-azurermautomationmodule](/powershell/module/azurerm.automation/new-azurermautomationmodule)將模組匯入到您的自動化帳戶。 此 Cmdlet 會取得模組 zip 套件的 url。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您也可以使用相同的 Cmdlet 直接從 PowerShell 資源庫匯入模組。 請務必從[PowerShell 資源庫](https://www.powershellgallery.com)抓取**ModuleName**和**ModuleVersion** 。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，流覽至您的自動化帳戶，然後選取 **共用資源** 底下的 **模組**。 按一下 [ **+ 新增模組**]。 選取包含模組的 **.zip**檔案，然後按一下 **[確定]** 開始匯入處理常式。

### <a name="powershell-gallery"></a>PowerShell 資源庫

來自 PowerShell 資源庫的模組可以直接從[PowerShell 資源庫](https://www.powershellgallery.com)或從您的自動化帳戶匯入。

若要從 PowerShell 資源庫匯入模組，請移至 https://www.powershellgallery.com，然後搜尋您要匯入的模組。 按一下 [**安裝選項**] 底下 [ **Azure 自動化**] 索引標籤上的 [**部署] Azure 自動化**。 此動作會開啟 Azure 入口網站。 在 [匯**入**] 頁面上選取您的自動化帳戶，然後按一下 **[確定]** 。

![PowerShell 資源庫匯入模組](../media/modules/powershell-gallery.png)

您也可以直接從您的自動化帳戶匯入 PowerShell 資源庫中的模組。 在您的自動化帳戶中，選取 [**共用資源**] 底下的 [**模組**]。 在 [模組] 頁面上，按一下 [**流覽資源庫]** ，然後搜尋模組的 PowerShell 資源庫。 選取您要匯入的模組，然後按一下 [匯**入**]。 在 [匯**入**] 頁面上，按一下 **[確定]** 以啟動匯入程式。

![PowerShell 資源庫從 Azure 入口網站匯入](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>刪除模組

如果您有模組的問題，或需要復原到舊版的模組，您可以從您的自動化帳戶中將它刪除。 當您建立自動化帳戶時，無法刪除已匯入之[預設模組](#default-modules)的原始版本。 如果您想要刪除的模組是已安裝其中一個[預設模組](#default-modules)的較新版本，則會回復為隨您的自動化帳戶一起安裝的版本。 否則，將會移除您從自動化帳戶中刪除的任何模組。

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，流覽至您的自動化帳戶，然後選取 **共用資源** 底下的 **模組**。 選取您要移除的模組。 在 [**模組**] 頁面上，選取 [**刪除**]。 如果此模組是其中一個[預設模組](#default-modules)，則會回復為建立自動化帳戶時所存在的版本。

### <a name="powershell"></a>PowerShell

若要透過 PowerShell 移除模組，請執行下列命令：

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>內部 Cmdlet

以下是內部 `Orchestrator.AssetManagement.Cmdlets` 模組中匯入到每個自動化帳戶的 Cmdlet 清單。 這些 Cmdlet 可在您的 runbook 和 DSC 設定中存取，並可讓您與您的自動化帳戶內的資產進行互動。 此外，內部 Cmdlet 可讓您從加密的**變數**值、**認證**和加密的**連接**欄位抓取秘密。 Azure PowerShell Cmdlet 無法取得這些秘密。 使用這些 Cmdlet 時，您不需要以隱含方式連接到 Azure，例如使用執行身分帳戶向 Azure 進行驗證。

>[!NOTE]
>這些內部 Cmdlet 適用于 Windows 混合式 Runbook 背景工作角色，無法在 Linux 混合式 Runbook 背景工作角色上使用。 針對直接在電腦上執行的 runbook 或針對您的環境中的資源，使用對應的[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0)或[Az 模組](../az-modules.md)。 
>

|名稱|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|開始-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|等候-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>將連線類型新增至您的模組

您可以提供自訂連線[類型](../automation-connections.md)，以便在您的自動化帳戶中使用，方法是在您的模組中新增選擇性檔案。 此檔案是一個中繼資料檔案，指定要在您的自動化帳戶中與模組的 Cmdlet 搭配使用的 Azure 自動化連線類型。 若要達到此目的，您必須先知道如何撰寫 PowerShell 模組。 如需有關模組撰寫的詳細資訊，請參閱[如何撰寫 PowerShell 腳本模組](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)。

![在 Azure 入口網站中使用自訂連接](../media/modules/connection-create-new.png)

若要新增 Azure 自動化連線類型，您的模組必須包含名稱為 `<ModuleName>-Automation.json` 的檔案，以指定連線類型屬性。 Json 檔案會放在壓縮 .zip 檔案的模組資料夾內。 此檔案包含連接到模組所代表的系統或服務所需的連線欄位。 設定最後會在 Azure 自動化中建立連線類型。 使用此檔案，您可以針對模組的連線類型，設定功能變數名稱、類型，以及欄位是否應加密或選擇性。 下列範例是 json 檔案格式的範本，它會定義使用者名稱和密碼屬性：

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

## <a name="module-best-practices"></a>模組的最佳做法

PowerShell 模組都可以匯入到 Azure 自動化，以使其 Cmdlet 可供在 Runbook 內使用，以及使其 DSC 資源可供在 DSC 組態內使用。 Azure 自動化會在幕後儲存這些模組，並在執行 Runbook 作業和 DSC 編譯作業時將其載入 Azure 自動化沙箱，以在其中執行 Runbook 和編譯 DSC 組態。 模組中的任何 DSC 資源也會自動放置於 Automation DSC 提取伺服器。 它們可以在機器套用 DSC 組態時由機器提取。

當您撰寫 PowerShell 模組以用於 Azure 自動化時，建議您考慮下列各項：

* 請不要在 .zip 封裝內包含版本資料夾。  此問題較不在意 runbook，但會導致狀態設定服務發生問題。  當模組散發至 DSC 所管理的節點時，Azure 自動化將會自動建立版本資料夾，而如果有版本資料夾存在，則會有兩個實例。  DSC 模組的範例資料夾結構：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* 在模組中加入每個 Cmdlet 的概要、描述和說明 URI。 在 PowerShell 中，您可以為 Cmdlet 定義特定說明資訊，以讓使用者透過 **Get-Help** Cmdlet 獲得其使用說明。 下列範例顯示如何在 .psm1 模組檔案中定義的概要和說明 URI：

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

  提供此資訊會顯示在 PowerShell 主控台使用 **Get-Help** Cmdlet 的說明。 此描述也會顯示在 Azure 入口網站中。

  ![整合模組說明](../media/modules/module-activity-description.png)

* 如果模組連接至外部服務，則應該包含[連線類型](#add-a-connection-type-to-your-module)。 模組中的每個 Cmdlet 應該要能夠採用連線物件 (該連線類型的執行個體) 來做為參數。 使用者每次呼叫 Cmdlet 時，都會將連線資產的參數對應至 Cmdlet 的相對應參數。 根據上述的 runbook 範例，它會使用名為 ContosoConnection 的範例 Contoso 連線資產來存取 Contoso 資源，並從外部服務傳回資料。

  在下列範例中，欄位會對應至 `PSCredential` 物件的使用者名稱和密碼屬性，然後傳遞至 Cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  更容易且更好的方法來處理這種行為，就是直接將連線物件傳遞給 Cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  您可以透過讓 Cmdlet 接受直接將連線物件當做參數，而非只是參數的連線欄位，來讓 Cmdlet 具有像是上述範例的行為。 通常您會想讓每個 Cmdlet 都設定參數，讓未使用 Azure 自動化的使用者可以直接呼叫 Cmdlet，而不必建構雜湊表來作為連線物件。 參數集 `UserAccount`，可用來傳遞連接欄位屬性。 `ConnectionObject` 可讓您直接透過傳遞連接。

* 定義模組中所有 Cmdlet 的輸出類型。 為 Cmdlet 定義輸出類型，可讓設計階段 IntelliSense 協助您判斷 Cmdlet 的輸出屬性，以供在撰寫期間使用。 在圖形化撰寫自動化 Runbook 期間，它會特別有幫助，因為設計階段的知識是讓模組的使用者獲得容易使用體驗的關鍵。

新增 `[OutputType([<MyOutputType>])]`，其中 MyOutputType 是有效的類型。 若要深入瞭解 OutputType，請參閱[關於函數 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下列程式碼是將 `OutputType` 新增至 Cmdlet 的範例：

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

* 讓模組中的所有 Cmdlet 變成無狀態。 多個 runbook 作業可以同時在相同的 AppDomain 和相同的進程和沙箱中執行。 如果這些層級上有任何共用的狀態，則作業可能會互相影響。 這種行為可能會導致間歇性且難以診斷問題。  以下是不該做之事情的範例：

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

* 此模組應完全包含在可 xcopy 的封裝中。 當 runbook 需要執行時，Azure 自動化模組會散發至自動化沙箱。 模組需要與它們執行所在的主機獨立運作。 您應該能夠進行壓縮並且移動模組套件，在將它匯入至另一部主機的 PowerShell 環境時讓它正常運作。 為了讓它運作，模組不應該相依於模組資料夾外部的任何檔案。 此資料夾是在模組匯入至 Azure 自動化時，進行壓縮的資料夾。 此模組也不應該相依於主機上的任何唯一登錄設定，例如安裝產品時所設定的這些設定。 模組中的所有檔案都應具有少於140個字元的路徑。 任何超過140個字元的路徑都會導致匯入 runbook 時發生問題。 若未遵循此最佳做法，模組在 Azure 自動化中將無法使用。  

* 如果在您的模組中參考 [Azure Powershell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，請確定您未同時參考 `AzureRM`。 `Az` 模組無法與 `AzureRM` 模組搭配使用。 `Az` 在 Runbook 中受到支援，但是預設未匯入。 若要深入了解 `Az` 模組和要考量的事項，請參閱 [Azure 自動化中的 Az 模組支援](../az-modules.md)。

## <a name="default-modules"></a>預設模組

下表列出在建立自動化帳戶時，依預設匯入的模組。 下列模組可以匯入較新的版本，但即使您刪除了較新的版本，也無法從您的自動化帳戶移除原始版本。

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

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何建立 PowerShell 模組，請參閱 [撰寫 Windows PowerShell 模組](/powershell/scripting/developer/windows-powershell)
