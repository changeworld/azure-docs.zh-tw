---
title: 在 Azure 自動化中管理模組
description: 本文介紹如何在 Azure 自動化中管理模組。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770952"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自動化中管理模組

Azure 自動化允許您導入 PowerShell 模組,以便在 DSC 配置中啟用 Runbook 和 DSC 資源中的 cmdlet。 Azure 自動化中使用的模組包括:

* [Azure 電源外殼 Az.自動化](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure 電源外殼 AzureRM.自動化](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* 適用於`Orchestrator.AssetManagement.Cmdlets`Windows 的紀錄分析代理的內部模組
* [Azure 自動化創作工具組](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* 其他 PowerShell 模組
* 您建立的自訂模組 

創建自動化帳戶時,Azure 自動化默認導入一些模組。 請參考[預設模組](#default-modules)。

當 Azure 自動化執行 Runbook 和 DSC 編譯作業時,它會將模組載入到可以運行 Runbook 和 DSC 配置的沙箱中。 自動化還會自動將任何 DSC 資源放在 DSC 拉取伺服器上的模組中。 計算機在應用 DSC 配置時可以提取資源。

>[!NOTE]
>請務必僅導入 Runbook 和 DSC 設定實際需要的模組。 無論如何,不要導入匯總模組,例如,Az.自動化。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="default-modules"></a>預設模組

下表列出了 Azure 自動化在創建自動化帳戶時預設導入的模組。 自動化可以導入這些模組的較新版本。 但是,即使您刪除了較新版本,也不能從自動化帳戶中刪除原始版本。 請注意,這些預設模組包括多個 AzureRM 模組。 

> [!NOTE]
> 我們不建議在包含任何解決方案的自動化帳戶中更改模組和 Runbook。 

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
| xPowerShell 執行政策 | 1.1.0.0 |
| x遠端桌面管理員 | 1.1.0.0 |


在 Runbook 和 DSC 配置中首選 Az.自動化模組。 但是,Azure 自動化不會自動將匯總 Az 模組導入任何新的或現有的自動化帳戶。 有關使用這些模組的更多,請參閱[移移到 Az 模組](#migrating-to-az-modules)。

## <a name="internal-cmdlets"></a>內部公分

下表定義了`Orchestrator.AssetManagement.Cmdlets`模組支援的內部 cmdlet。 在 Runbook 和 DSC 配置中使用這些配置與自動化帳戶中的自動化資產進行互動。 cmdlet 旨在從加密的變數、憑據和加密連接中檢索機密。 

> [!NOTE]
> Azure PowerShell cmdlet 無法獲取內部 cmdlet 可以檢索的資產機密。 

|名稱|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|啟動-自動化執行簿|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|等待自動化作業|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

這些內部 cmdlet 在 Windows 混合 Runbook 輔助角色上可用,但在 Linux 混合 Runbook 輔助功能程式上不可用。 它們從協調器沙箱中執行,該沙箱由混合工作人員使用。  它們的使用不需要與 Azure 的隱式連接,就像使用「運行方式」帳戶進行身份驗證時一樣。

使用 Az 或 AzureRM cmdlet 用於直接在電腦上運行的 Runbook 和配置,或者針對環境中的資源,而不是使用內部 cmdlet。 在這些情況下,在使用 cmdlet 時必須隱式連接到 Azure,就像使用 Run As 帳戶對 Azure 進行身份驗證時一樣。 

## <a name="modules-supporting-get-automationpscredential"></a>支援取得自動化PS認證的模組

對於使用 Azure 自動化創作工具套件的本地開發,cmdlet`Get-AutomationPSCredential`是程式集 Azure[自動化創作工具套件的](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)一部分。 對於使用自動化上下文的 Azure,cmdlet`Orchestrator.AssetManagement.Cmdlets`位於 中。 要瞭解有關在 Azure 自動化中使用認證的資訊,請參閱[Azure 自動化 中的認證資產](credentials.md)。

## <a name="migrating-to-az-modules"></a>遷移至 Az 模組

在 Azure 自動化中使用 Az 模組時,需要考慮以下幾點:

* 自動化帳戶中的更高級別解決方案可以使用 Runbook 和模組。 因此,編輯 Runbook 或升級模組可能會導致解決方案出現問題。 在導入 Az 模組之前,應在單獨的自動化帳戶中仔細測試所有 Runbook 和解決方案。 

* 對模組的任何修改都會對[非工作時間解決方案的啟動/停止 VM](../automation-solution-vm-management.md)產生負面影響。 

* 將 Az 模組導入自動化帳戶不會自動在執行手冊使用的 PowerShell 作業階段中導入該模組。 模組會在下列情況下匯入到 PowerShell 工作階段：

    * 當 Runbook 從模組呼叫 cmdlet 時
    * 當 Runbook`Import-Module`使用 cmdlet 顯式匯入模組時
    * 當 Runbook 匯入另一個從屬模組時

完成模組遷移後,不要嘗試使用自動化帳戶上的 AzureRM 模組開始執行簿。 還建議不要導入或更新帳戶上的 AzureRM 模組。 請考慮遷移到 Az.自動化的帳戶,並且僅使用 Az 模組操作。 

>[!IMPORTANT]
>創建新的自動化帳戶時,Azure 自動化預設安裝 AzureRM 模組。 您仍可以使用 AzureRM cmdlet 更新教程執行簿。 但是,不應運行這些 Runbook。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止並取消計畫使用 AzureRM 模組的所有 Runbook

為了確保不運行任何使用 AzureRM 模組的現有 Runbook,停止並取消計畫所有受影響的 Runbook。 以執行類似於此範例的代碼,您可以看到存在哪些計畫以及要刪除的計劃:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

請務必分別查看每個計劃,以確保如有必要,可以在未來為 Runbook 重新計劃。

### <a name="import-the-az-modules"></a>匯入 Az 模組

本節介紹如何在 Azure 門戶中導入 Az 模組。 請記住僅導入所需的 Az 模組,而不是導入整個 Az.自動化模組。 由於[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)是其他 Az 模組的依賴項,因此請確保在任何其他模組之前導入此模組。

1. 從您的自動化帳戶中,選擇**分享資源**下的**模組**。 
2. 按一下 [瀏覽資源庫]**** 來開啟 [瀏覽資源庫] 頁面。  
3. 在搜尋列中,輸入模組名稱,例如`Az.Accounts`。 
4. 在 PowerShell 模組頁面上,按一下「**導入**」以將模組導入自動化帳戶。

    ![將模組匯入自動化帳戶](../media/modules/import-module.png)

此導入過程也可以通過[PowerShell 庫](https://www.powershellgallery.com)透過搜索要導入的模組來完成。 找到模組後,選擇它,選擇 Azure**自動化**選項卡,然後單擊「**部署到 Azure 自動化**」。

![直接從資源庫匯入模組](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>測試您的 Runbook

將 Az 模組導入自動化帳戶後,可以開始編輯 Runbook 以使用新模組。 大多數 cmdlet 的名稱與 AzureRM 模組的名稱相同,但 AzureRM(或 AzureRm) 前綴已更改為 Az。 有關不遵循此命名約定的模組清單,請參閱[異常清單](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

測試 Runbook 的變更以使用新 cmdlet 的方法是在 Runbook`Enable-AzureRmAlias -Scope Process`的開頭使用 。 通過將此命令添加到 runbook,文本可以運行而不進行更改。

## <a name="authoring-modules"></a>製作模組

我們建議您在編寫 PowerShell 模組以在 Azure 自動化中使用時遵循本節中的注意事項。

### <a name="references-to-azurerm-and-az"></a>對 AzureRM 與 Az 的參考

如果引用模組中的 Az 模組,請確保不引用 AzureRM 模組。 不能同時使用這兩組模組。 

### <a name="version-folder"></a>版本資料夾

請勿在模組的 **.zip**包中包含版本資料夾。  此問題不太適合 Runbook,但確實會導致狀態配置 (DSC) 服務出現問題。 當模組分發到由 DSC 管理的節點時,Azure 自動化會自動創建版本資料夾。 如果存在版本資料夾,則最終有兩個實例。 下面是 DSC 模組的範例資料夾結構:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>說明資訊

包括模組中每個 cmdlet 的概要、說明和説明 URI。 在 PowerShell 中,您`Get-Help`可以使用 cmdlet 為 cmdlet 定義説明資訊。 下面的範例展示如何在 **.psm1**模組檔中定義概要並説明 URI。

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

  提供此資訊透過 PowerShell`Get-Help`主控台中的 cmdlet 顯示説明文字。 此文本也顯示在 Azure 門戶中。

  ![整合模組說明](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>連線類型

若是模組連線到外部服務,請定義[連線類型](#adding-a-connection-type-to-your-module)。 模組中的每個 cmdlet 都應接受連接物件(該連接類型的實例)作為參數。 使用者每次調用 cmdlet 時都會將連接資產的參數映射到 cmdlet 的相應參數。 以下 Runbook 範例基於上一節中的範例,使用`ContosoConnection`調用的 Contoso 連接資產來存取 Contoso 資源並從外部服務返回數據。 在此範例中,欄位映射到`UserName``Password``PSCredential`物件的和屬性,然後傳遞到 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  處理此行為的一種更簡單、更好的方法是將連接物件直接傳遞到 cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  您可以通過允許 cmdlet 直接接受連接物件作為參數,而不是僅為參數接受連接欄位,從而為 cmdlet 啟用類似的行為。 通常您會想讓每個 Cmdlet 都設定參數，讓未使用 Azure 自動化的使用者可以直接呼叫 Cmdlet，而不必建構雜湊表來作為連線物件。 參數集`UserAccount`用於傳遞連接欄位屬性。 `ConnectionObject`允許您直接傳遞連接。

### <a name="output-type"></a>輸出類型

定義模組中所有 cmdlet 的輸出類型。 定義 cmdlet 的輸出類型允許設計時 IntelliSense 幫助確定 cmdlet 在創作過程中的輸出屬性。 在圖形 Runbook 創作過程中,這種做法尤其有用,為此,設計時間知識是輕鬆體驗模組的關鍵。

添加`[OutputType([<MyOutputType>])]``MyOutputType`位置 是有效的類型。 要瞭解有關詳細資訊`OutputType`,請參閱[有關函數輸出類型屬性](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 以下代碼是新增到`OutputType`cmdlet 的範例:

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

使模組中的所有 cmdlet 都無狀態。 多個 Runbook 作業可以在`AppDomain`同一 進程和沙箱中同時運行。 如果這些級別上共用任何狀態,則作業可能會相互影響。 此行為可能導致間歇性和難以診斷的問題。 下面是不該做什麼的範例:

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

### <a name="module-dependency"></a>模組相依項

確保模組完全包含在可以使用 xcopy 複製的套件中。 執行 Runbook 時,Azure 自動化模組將分發到自動化沙箱。 模組必須獨立於運行它們的主機工作。 

您應該能夠壓縮和移動模組包,並在導入到另一個主機的 PowerShell 環境中時使其正常工作。 為此,請確保模組不依賴於模組資料夾外的任何文件,這些檔在將模組導入 Azure 自動化時壓縮。 

您的模組不應依賴於主機上的任何唯一註冊表設置。 示例是安裝產品時所做的設置。 

### <a name="module-file-paths"></a>模組檔案路徑

確保模組中的所有檔具有少於 140 個字元的路徑。 長度超過 140 個字元的任何路徑都會導致導入 Runbook 出現問題。 Azure 自動化無法將路徑大小超過 140 個字元的文件導入`Import-Module`到 具有的 PowerShell 會話中。

## <a name="importing-modules"></a>匯入模組

本節定義了將模組導入自動化帳戶的幾種方法。 

### <a name="import-modules-in-azure-portal"></a>Azure 門戶中匯入模組

要在 Azure 門戶中導入模組,請參閱:

1. 瀏覽至您的自動化帳戶。
2. 在**分享資源**下選擇**模組**。
3. 按下 **「添加模組**」。 
4. 選擇包含模組的 **.zip**檔。
5. 按下 **「確定**」開始導入過程。

### <a name="import-modules-using-powershell"></a>使用 PowerShell 匯入模組

您可以使用[新阿茲自動化模組](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0)cmdlet 將模組導入自動化帳戶。 cmdlet 取得模組 .zip 包的 URL。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您還可以使用相同的 cmdlet 直接從 PowerShell 庫導入模組。 一定要抓住`ModuleName`並`ModuleVersion`從[PowerShell畫廊](https://www.powershellgallery.com)。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>從 PowerShell 函式庫匯入模組

您可以直接從庫或從自動化帳戶導入[PowerShell 庫](https://www.powershellgallery.com)模組。

要直接從 PowerShell 函式庫匯入模組:

1. 跳到https://www.powershellgallery.com並搜索要導入的模組。
2. 按下 **「在****」安裝選項**「下的**Azure 自動化**選項卡上部署到 Azure 自動化。 此操作將打開 Azure 門戶。 
3. 在「導入」頁上,選擇您的自動化帳戶,然後單擊 **「確定**」。

![PowerShell 函式庫匯入模組](../media/modules/powershell-gallery.png)

要直接從您的自動化帳戶導入 PowerShell 函式庫模組,請:

1. 在**分享資源**下選擇**模組**。 
2. 在"模組"頁上,按一下 **「流覽庫**」,然後搜索庫中的模組。 
3. 選擇要導入的模組,然後單擊「**導入**」。 
4. 在「導入」頁上,按一下「**確定」** 以啟動導入過程。

![從 Azure 門戶導入電源外殼庫](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>移除模組

如果模組出現問題或需要回滾到模組的早期版本,則可以將其從自動化帳戶中刪除。 無法刪除創建自動化帳戶時導入的[預設模組](#default-modules)的原始版本。 如果要刪除的模組是[預設模組](#default-modules)之一的較新版本,則它回滾到與自動化帳戶一起安裝的版本。 否則,您從自動化帳戶中刪除的任何模組都將被刪除。

### <a name="delete-modules-in-azure-portal"></a>移除 Azure 門戶中的模組

要刪除 Azure 門戶中的模組,請執行以下工作:

1. 導覽您的自動化帳戶,並在**分享資源**下選擇**模組**。 
2. 選擇要刪除的模組。 
3. 在 **「模組」** 頁上,選擇 **「刪除**」。 如果此模組是[預設模組](#default-modules)之一,它將回滾到創建自動化帳戶時存在的版本。

### <a name="delete-modules-using-powershell"></a>使用 PowerShell 移除模組

要透過 PowerShell 刪除模組,請執行以下指令:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>新增連線類型

您可以透過模組加入選擇的資料檔,提供在自動化帳戶中使用的自訂[連接類型](../automation-connections.md)。 此檔指定要與自動化帳戶中的模組 cmdlet 一起使用的 Azure 自動化連接類型。 要瞭解有關創作 PowerShell 模組的更多資訊,請參閱[如何編寫 PowerShell 文稿模組](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)。

![在 Azure 門戶中使用自訂連線](../media/modules/connection-create-new.png)

指定連接類型屬性的檔案名為**&lt;&gt;ModuleName -自動化.json,** 位於壓縮 **.zip**檔案的模組資料夾中。 此檔包含連接到模組表示的系統或服務所需的連接欄位。 配置允許在 Azure 自動化中創建連接類型。 使用此檔案,您可以設定欄位名稱、類型以及欄位是加密還是模組的連接類型的可選。 下面的範例是 **.json**檔案格式的樣本,用於定義使用者名稱和密碼屬性:

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

* 有關使用 Azure PowerShell 模組的詳細資訊,請參閱[使用 Azure PowerShell 入門](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)。
* 要瞭解有關建立 PowerShell 模組的更多資訊,請參閱[編寫 Windows PowerShell 模組](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)。
