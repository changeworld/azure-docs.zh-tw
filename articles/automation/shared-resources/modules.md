---
title: 在 Azure 自動化中管理模組
description: 本文介紹如何在 Azure 自動化中管理模組
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278333"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自動化中管理模組

Azure 自動化提供了將 PowerShell 模組導入自動化帳戶以由基於 PowerShell 的 Runbook 使用的功能。 這些模組可以是您從 PowerShell 庫創建的自訂模組，也可以是 Azure 的 AzureRM 和 Az 模組。 創建自動化帳戶時，預設情況下將導入某些模組。

## <a name="import-modules"></a>匯入模組

您可以通過多種方式將模組導入自動化帳戶。 以下各節顯示了導入模組的不同方法。

> [!NOTE]
> 要在 Azure 自動化中使用的模組中檔的最大路徑為 140 個字元。 任何超過 140 個字元的路徑將無法導入到 具有 的 PowerShell`Import-Module`會話中。

### <a name="powershell"></a>PowerShell

您可以使用[新 AzureRm 自動化模組](/powershell/module/azurerm.automation/new-azurermautomationmodule)將模組導入自動化帳戶。 Cmdlet 將 URL 帶到模組 zip 包。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您還可以使用相同的 Cmdlet 直接從 PowerShell 庫導入模組。 請確保從[PowerShell 庫中](https://www.powershellgallery.com)獲取**模組名稱**和**模組版本**。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 門戶中，導航到自動化帳戶，並在 **"共用資源**"下選擇**模組**。 按一下 **+ 添加模組**。 選擇包含模組的 **.zip**檔，然後按一下 **"確定**"開始導入過程。

### <a name="powershell-gallery"></a>PowerShell 資源庫

PowerShell 庫中的模組可以直接從[PowerShell 庫](https://www.powershellgallery.com)導入，也可以從您的自動化帳戶導入。

要從 PowerShell 庫導入模組，請訪問https://www.powershellgallery.com並搜索要導入的模組。 按一下 **"在****"安裝選項**"下的**Azure 自動化**選項卡上部署到 Azure 自動化。 此動作會開啟 Azure 入口網站。 在 **"導入**"頁上，選擇您的自動化帳戶，然後按一下 **"確定**"。

![PowerShell 庫導入模組](../media/modules/powershell-gallery.png)

您還可以直接從您的自動化帳戶導入 PowerShell 庫中的模組。 在自動化帳戶中，選擇**共用資源**下的**模組**。 在模組頁面上按一下 **"流覽庫**"，然後搜索 PowerShell 庫以查找模組。 選擇要導入的模組，然後按一下"**導入**"。 在 **"導入**"頁上，按一下"**確定"** 以啟動導入過程。

![從 Azure 門戶導入電源外殼庫](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>刪除模組

如果模組出現問題或需要回滾到模組的早期版本，則可以將其從自動化帳戶中刪除。 不能刪除創建自動化帳戶時導入的[預設模組](#default-modules)的原始版本。 如果要刪除的模組是安裝的[預設模組](#default-modules)之一的較新版本，它將回滾到與自動化帳戶一起安裝的版本。 否則，您從自動化帳戶中刪除的任何模組都將被刪除。

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 門戶中，導航到自動化帳戶，並在 **"共用資源**"下選擇**模組**。 選擇要刪除的模組。 在 **"模組"** 頁上，選擇 **"刪除**"。 如果此模組是[預設模組](#default-modules)之一，它將回滾到創建自動化帳戶時存在的版本。

### <a name="powershell"></a>PowerShell

要通過 PowerShell 刪除模組，請運行以下命令：

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>內部釐米

以下是導入到每個自動化帳戶的內部`Orchestrator.AssetManagement.Cmdlets`模組中的 Cmdlet 的清單。 這些 Cmdlet 可在 Runbook 和 DSC 配置中訪問，並允許您與自動化帳戶中的資產進行交互。 此外，內部 Cmdlet 允許您從加密**的變數**值、**憑據**和加密**連接**欄位檢索機密。 Azure PowerShell Cmdlet 無法檢索這些機密。 這些 Cmdlet 不要求您在使用它們時隱式連接到 Azure，例如使用"運行作為帳戶"對 Azure 進行身份驗證。

>[!NOTE]
>這些內部 Cmdlet 在 Windows 混合 Runbook 輔助角色上可用，它們在 Linux 混合 Runbook 協助工具程式上不可用。 對直接在電腦上運行或針對環境中的資源運行的 Runbook 使用相應的[AzureRM.自動化](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0)或[Az 模組](../az-modules.md)。 
>

|名稱|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|啟動-自動化運行簿|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|等待自動化作業|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>向模組添加連線類型

您可以通過向模組添加可選檔，提供自訂[連線類型](../automation-connections.md)，供您在自動化帳戶中使用。 此檔是一個元資料檔案，指定要與自動化帳戶中的模組 Cmdlet 一起使用的 Azure 自動化連線類型。 為此，您必須首先瞭解如何編寫 PowerShell 模組。 有關模組創作的詳細資訊，請參閱[如何編寫 PowerShell 腳本模組](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)。

![在 Azure 門戶中使用自訂連接](../media/modules/connection-create-new.png)

要添加 Azure 自動化連線類型，模組必須包含具有指定連線類型屬性`<ModuleName>-Automation.json`的名稱的檔。 json 檔放置在壓縮的 .ZIP 檔案的模組資料夾中。 此檔案包含連線的欄位，連線至模組代表的系統或服務時需要這個項目。 配置最終在 Azure 自動化中創建連線類型。 使用此檔，您可以為模組的連線類型設置欄位名稱、類型以及欄位是應加密還是可選。 下面的示例是 json 檔案格式中定義使用者名和密碼屬性的範本：

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

## <a name="module-best-practices"></a>模組最佳實踐

PowerShell 模組都可以匯入到 Azure 自動化，以使其 Cmdlet 可供在 Runbook 內使用，以及使其 DSC 資源可供在 DSC 組態內使用。 Azure 自動化會在幕後儲存這些模組，並在執行 Runbook 作業和 DSC 編譯作業時將其載入 Azure 自動化沙箱，以在其中執行 Runbook 和編譯 DSC 組態。 模組中的任何 DSC 資源也會自動放置於 Automation DSC 提取伺服器。 它們可以在機器套用 DSC 組態時由機器提取。

我們建議您在創作用於 Azure 自動化的 PowerShell 模組時考慮以下事項：

* 請不要在 .zip 包中包含版本資料夾。  此問題不太適合 Runbook，但會導致狀態佈建服務出現問題。  當模組分發到 DSC 管理的節點時，Azure 自動化將自動創建版本資料夾，如果存在版本資料夾，則最終將創建兩個實例。  DSC 模組的示例資料夾結構：

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* 在模組中加入每個 Cmdlet 的概要、描述和說明 URI。 在 PowerShell 中，您可以為 Cmdlet 定義特定說明資訊，以讓使用者透過 **Get-Help** Cmdlet 獲得其使用說明。 下面的示例演示如何在 .psm1 模組檔中定義概要和説明 URI：

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

  提供此資訊會顯示在 PowerShell 主控台使用 **Get-Help** Cmdlet 的說明。 此說明也顯示在 Azure 門戶中。

  ![整合模組說明](../media/modules/module-activity-description.png)

* 如果模組連接到外部服務，則應包含[連線類型](#add-a-connection-type-to-your-module)。 模組中的每個 Cmdlet 應該要能夠採用連線物件 (該連線類型的執行個體) 來做為參數。 使用者每次調用 Cmdlet 時都會將連接資產的參數映射到 Cmdlet 的相應參數。 基於上面的 Runbook 示例，它使用名為 ContosoConnect 的示例 Contoso 連接資產來訪問 Contoso 資源並從外部服務返回資料。

  在下面的示例中，欄位映射到`PSCredential`物件的 UserName 和密碼屬性，然後傳遞到 Cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  處理此行為的一種更簡單、更好的方法是將連線物件直接傳遞到 Cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  您可以透過讓 Cmdlet 接受直接將連線物件當做參數，而非只是參數的連線欄位，來讓 Cmdlet 具有像是上述範例的行為。 通常您會想讓每個 Cmdlet 都設定參數，讓未使用 Azure 自動化的使用者可以直接呼叫 Cmdlet，而不必建構雜湊表來作為連線物件。 參數集`UserAccount`， 用於傳遞連接欄位屬性。 `ConnectionObject`允許您直接傳遞連接。

* 定義模組中所有 Cmdlet 的輸出類型。 為 Cmdlet 定義輸出類型，可讓設計階段 IntelliSense 協助您判斷 Cmdlet 的輸出屬性，以供在撰寫期間使用。 在圖形化撰寫自動化 Runbook 期間，它會特別有幫助，因為設計階段的知識是讓模組的使用者獲得容易使用體驗的關鍵。

添加`[OutputType([<MyOutputType>])]`"我的輸出類型"的有效類型。 要瞭解有關輸出類型的更多，請參閱[有關函數輸出類型屬性](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 以下代碼是添加到`OutputType`Cmdlet 的示例：

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

* 讓模組中的所有 Cmdlet 變成無狀態。 多個 Runbook 作業可以在同一 AppDomain 和相同的進程和沙箱中同時運行。 如果這些級別上共用任何狀態，則作業可能會相互影響。 此行為可能導致間歇性且難以診斷問題。  以下是不該做之事情的範例：

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

* 該模組應完全包含在可 xcopy 的封裝中。 當 Runbook 需要執行運行時，Azure 自動化模組將分發到自動化沙箱。 模組需要與它們執行所在的主機獨立運作。 您應該能夠進行壓縮並且移動模組套件，在將它匯入至另一部主機的 PowerShell 環境時讓它正常運作。 為了讓它運作，模組不應該相依於模組資料夾外部的任何檔案。 此資料夾是在模組匯入至 Azure 自動化時，進行壓縮的資料夾。 此模組也不應該相依於主機上的任何唯一登錄設定，例如安裝產品時所設定的這些設定。 模組中的所有檔路徑應小於 140 個字元。 任何超過 140 個字元的路徑都會導致導入 Runbook 時出現問題。 若未遵循此最佳做法，模組在 Azure 自動化中將無法使用。  

* 如果在您的模組中參考 [Azure Powershell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，請確定您未同時參考 `AzureRM`。 `Az` 模組無法與 `AzureRM` 模組搭配使用。 `Az` 在 Runbook 中受到支援，但是預設未匯入。 若要深入了解 `Az` 模組和要考量的事項，請參閱 [Azure 自動化中的 Az 模組支援](../az-modules.md)。

## <a name="default-modules"></a>預設模組

下表列出了預設情況下在創建自動化帳戶時導入的模組。 下面列出的模組可以導入較新版本，但即使刪除了新版本的模組，也無法從自動化帳戶中刪除原始版本。

|模組名稱|版本|
|---|---|
| 審計政策 | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GP註冊政策分析器 | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| 協調器.資產管理.Cmdlet | 1 |
| 私營部門服務資源 | 2.9.0.0 |
| 安全政策 | 2.1.0.0 |
| 國家配置綜合資源 | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShell執行政策 | 1.1.0.0 |
| x遠端桌面管理員 | 1.1.0.0 |

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何建立 PowerShell 模組，請參閱 [撰寫 Windows PowerShell 模組](/powershell/scripting/developer/windows-powershell)
