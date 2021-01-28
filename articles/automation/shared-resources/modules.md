---
title: 在 Azure 自動化中管理模組
description: 此文章說明如何使用 PowerShell 模組，在 Runbook 中啟用 Cmdlet，以及在 DSC 設定中啟用 DSC 資源。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/25/2021
ms.topic: conceptual
ms.openlocfilehash: d62ed96f86078839e66a4cf2ce71f304de2abf4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936629"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自動化中管理模組

Azure 自動化會使用數個 PowerShell 模組，在 Runbook 中啟用 Cmdlet，以及在 DSC 設定中啟用 DSC 資源。 支援的模組包括：

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az)。
* [Azure PowerShell AzureRM.Automation](/powershell/module/azurerm.automation/)。
* 其他 PowerShell 模組。
* 內部 `Orchestrator.AssetManagement.Cmdlets` 模組。
* Python 2 模組。
* 您建立的自訂模組。

當您建立自動化帳戶時，Azure 自動化預設會匯入一些模組。 請參閱[預設模組](#default-modules)。

## <a name="sandboxes"></a>沙箱

當自動化執行 Runbook 和 DSC 編譯作業時，其會將模組載入至沙箱，Runbook 可在其中執行，而且 DSC 設定可在其中編譯。 自動化也會將所有 DSC 資源自動放置於 DSC 提取伺服器的模組中。 當機器套用 DSC 設定時，即可提取資源。

>[!NOTE]
>請確認只會匯入您的 Runbook 和 DSC 設定所需的模組。 不建議您匯入根 Az 模組。 該模組包含許多您可能不需要的其他模組，而其可能會導致效能問題。 請改為匯入個別模組，例如 Az.Compute。

雲端沙箱最多可支援48系統呼叫，並基於安全性理由限制所有其他呼叫。 雲端沙箱不支援其他功能，例如認證管理和某些網路功能。

由於包含的模組和 Cmdlet 數目，因此很難事先知道哪一個 Cmdlet 會產生不支援的呼叫。 一般而言，我們發現需要提高存取權的 Cmdlet 有問題，需要認證作為參數，或與網路相關的 Cmdlet。 沙箱中不支援任何執行完整堆疊網路作業的 Cmdlet，包括從 AIPService PowerShell 模組的連線 [AipService](/powershell/module/aipservice/connect-aipservice) ，以及從 Set-dnsclient 模組 [解析 DnsName](/powershell/module/dnsclient/resolve-dnsname) 。

這些是沙箱的已知限制。 建議的解決方法是部署 [混合式 Runbook 背景工作角色](../automation-hybrid-runbook-worker.md) ，或使用 [Azure Functions](../../azure-functions/functions-overview.md)。

## <a name="default-modules"></a>預設模組

下表列出當您建立自動化帳戶時，Azure 自動化預設會匯入的模組。 自動化可以匯入這些模組的較新版本。 不過，即使您刪除了較新版本，也無法從您的自動化帳戶移除原始版本。 請注意，這些預設模組會包含數個 AzureRM 模組。 

自動化不會將根 Az 模組自動匯入至任何新的或現有的自動化帳戶。 如需使用這些模組的詳細資訊，請參閱[移轉至 Az 模組](#migrate-to-az-modules)。

> [!NOTE]
> 不建議您在用來部署[停機期間啟動/停止 VM](../automation-solution-vm-management.md) 功能的自動化帳戶中更改模組和 Runbook。

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
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az 模組

針對 Az.Automation，大部分的 Cmdlet 都會與用於 AzureRM 模組的 Cmdlet 具有相同名稱，不同之處在於 `AzureRM` 前置詞已變更為 `Az`。 如需未遵循此命名慣例的 Az 模組清單，請參閱[例外狀況清單](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

## <a name="internal-cmdlets"></a>內部 Cmdlet

Azure 自動化支援適用於 Windows 之 Log Analytics 代理程式的內部 `Orchestrator.AssetManagement.Cmdlets` 模組 (預設會安裝)。 下表定義內部 Cmdlet。 這些 Cmdlet 的設計目的是代替 Azure PowerShell Cmdlet，用來與共用資源互動。 其可以從加密的變數、認證及加密的連線中擷取祕密。

>[!NOTE]
>只有當您在 Azure 沙箱環境或 Windows 混合式 Runbook 背景工作角色上執行 Runbook 時，才可以使用內部 Cmdlet。 

|名稱|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

請注意，內部 Cmdlet 的命名方式與 Az 和 AzureRM Cmdlet 不同。 內部 Cmdlet 名稱不會在名詞中包含 `Azure` 或 `Az` 之類的字組，但會使用 `Automation` 字組。 我們建議在 Azure 沙箱或 Windows 混合式 Runbook 背景工作角色中執行 Runbook 期間，使用 Az 或 AzureRM Cmdlet。 其所需的參數較少，且會於已在執行的作業內容中執行。

使用 Az 或 AzureRM Cmdlet 來操作 Runbook 內容以外的自動化資源。 

## <a name="python-modules"></a>Python 模組

您可以在 Azure 自動化中建立 Python 2 Runbook。 如需 Python 模組資訊，請參閱[管理 Azure 自動化中的 Python 2 套件](../python-packages.md)。

## <a name="custom-modules"></a>自訂模組

Azure 自動化支援您建立來搭配 Runbook 和 DSC 設定使用的自訂 PowerShell 模組。 其中一種自訂模組類型是整合模組，可選擇性地包含中繼資料的檔案來定義模組 Cmdlet 的自訂功能。 [新增連線類型](../automation-connections.md#add-a-connection-type)中提供整合模組的使用範例。

Azure 自動化可以匯入自訂模組，以使其 Cmdlet 可供使用。 就像其他模組一樣，其會在幕後儲存模組，並在 Azure 沙箱中加以使用。

## <a name="migrate-to-az-modules"></a>移轉至 Az 模組

此節說明如何在自動化中移轉至 Az 模組。 如需詳細資訊，請參閱[將 Azure PowerShell 從 AzureRM 移轉至 Az](/powershell/azure/migrate-from-azurerm-to-az)。

我們不建議在相同的自動化帳戶中執行 AzureRM 模組和 Az 模組。 當您確定要從 AzureRM 移轉至 Az 時，最好完全認可至完整移轉。 自動化通常會重複使用自動化帳戶內的沙箱，以節省啟動時間。 如果您未進行完整模組移轉，您可能會啟動僅使用 AzureRM 模組的作業，然後啟動另一個只使用 Az 模組的作業。 沙箱很快就會損毀，而您會收到錯誤，指出模組不相容。 這種情況會導致任何特定 Runbook 或設定隨機發生損毀。

>[!NOTE]
>當您建立新的自動化帳戶時，即使是在移轉至 Az 模組之後，自動化預設還是會安裝 AzureRM 模組。 您仍然可以使用 AzureRM Cmdlet 來更新教學課程 Runbook。 不過，您不應該執行這些 Runbook。

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>模組移轉之前，先測試您的 Runbook 和 DSC 設定

移轉至 Az 模組之前，請務必謹慎地在個別自動化帳戶中測試所有 Runbook 和 DSC 設定。 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止並取消排程使用 AzureRM 模組的所有 Runbook

為了確保您不會執行任何使用 AzureRM 模組的現有 Runbook 或 DSC 設定，您必須停止並取消排程所有受影響的 Runbook 和設定。 首先，確定您會分別檢閱每個 Runbook 或 DSC 設定及其排程，以確保您可以在未來視需要重新排程項目。

當您準備好移除排程時，您可以使用 Azure 入口網站或 [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) \(英文\) Cmdlet。 請參閱[移除排程](schedules.md#remove-a-schedule)。

### <a name="remove-azurerm-modules"></a>移除 AzureRM 模組

您可以先移除 AzureRM 模組，然後再匯入 Az 模組。 不過，如果這樣做，您可能會中斷原始程式碼控制同步處理，並導致任何仍排定的指令碼失敗。 如果您決定移除模組，請參閱[解除安裝 AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)。

### <a name="import-az-modules"></a>匯入 Az 模組

在您的自動化帳戶中匯入 Az 模組，不會在 Runbook 使用的 PowerShell 工作階段中自動匯入該模組。 模組會在下列情況下匯入到 PowerShell 工作階段：

* 當 Runbook 從模組中叫用 Cmdlet 時。
* 當 Runbook 使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) \(英文\) Cmdlet 明確匯入模組時。
* 當 Runbook 匯入另一個相依模組時。

您可以在 Azure 入口網站中匯入 Az 模組。 請記住，只匯入所需的 Az 模組，而不是整個 Az.Automation 模組。 由於 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) \(英文\) 是其他 Az 模組的相依性，因此，務必在任何其他模組之前匯入此模組。

1. 從您的自動化帳戶中，選取 [共用資源] 底下的 [模組]。
2. 選取 [瀏覽資源庫]。  
3. 在搜尋列中，輸入模組名稱 (例如 `Az.Accounts`)。
4. 在 PowerShell 模組頁面上，選取 [匯入]，以將模組匯入至您的自動化帳戶。

    ![將模組匯入至您自動化帳戶的螢幕擷取畫面](../media/modules/import-module.png)

您也可以透過 [PowerShell 資源庫](https://www.powershellgallery.com) \(英文\)，藉由搜尋要匯入的模組來進行此匯入。 當您找到模組時，請加以選取，然後選擇 [Azure 自動化] 索引標籤。選取 [部署至 Azure 自動化]。

![直接從 PowerShell 資源庫匯入模組的螢幕擷取畫面](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>測試您的 Runbook

將 Az 模組匯入至自動化帳戶之後，您就可以開始編輯 Runbook 和 DSC 設定，以使用新的模組。 測試 Runbook 的修改以使用新 Cmdlet 的方式之一，是在 Runbook 開頭使用 `Enable-AzureRmAlias -Scope Process` 命令。 藉由將此命令新增至您的 Runbook，不需變更指令碼就可加以執行。

## <a name="author-modules"></a>撰寫模組

當您撰寫自訂 PowerShell 模組以在 Azure 自動化中使用時，建議您遵循此節中的考慮事項。 若要準備要匯入的模組，您必須建立至少一個 .psd1、.psm1 或 PowerShell 模組 **.dll** 檔案，且該檔案的名稱與模組資料夾相同。 然後壓縮模組資料夾，讓 Azure 自動化能夠以單一檔案形式匯入。 **.zip** 套件的名稱應該與內含的模組資料夾相同。

若要深入了解如何撰寫 PowerShell 模組，請參閱[如何撰寫 PowerShell 指令碼模組](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module) \(部分機器翻譯\)。

### <a name="version-folder"></a>版本資料夾

PowerShell 並存模組版本設定可讓您在 PowerShell 中使用一個以上的模組版本。 如果您有較舊的腳本經過測試，而且只適用于特定版本的 PowerShell 模組，但其他腳本需要相同 PowerShell 模組的較新版本，則這會很有用。

若要建立 PowerShell 模組使其包含多個版本，請建立模組資料夾，然後在此模組資料夾中為您想要使用的每個模組版本建立資料夾。 在下列範例中，名為 *TestModule* 的模組會提供兩個版本1.0.0 和2.0.0。

```dos
TestModule
   1.0.0
   2.0.0
```

在每個版本的資料夾中，將組成模組的 .psm1、. .psd1 或 PowerShell 模組 **.dll** 檔案複製到各自的版本資料夾中。 壓縮模組資料夾，讓 Azure 自動化可以將它匯入為單一 .zip 檔案。 雖然自動化只會顯示已匯入之模組的最高版本，但如果模組套件包含模組的並存版本，它們全都可在 runbook 或 DSC 設定中使用。  

雖然自動化支援在相同套件中包含並存版本的模組，但不支援跨模組套件匯入使用多個模組版本的模組。 例如，您可以將 **模組 A**（包含第1版和第2版）匯入您的自動化帳戶。 稍後您會將 **模組 A** 更新為包含第3版和第4版，當您匯入至自動化帳戶時，只有第3版和第4版可在任何 RUNBOOK 或 DSC 設定中使用。 如果您需要所有版本-1、2、3和4可供使用，則您的匯入應包含版本1、2、3和4。

如果您要在 runbook 之間使用相同模組的不同版本，您應該一律使用 Cmdlet 宣告您要在 runbook 中使用的版本 `Import-Module` ，並包含參數 `-RequiredVersion <version>` 。 即使您想要使用的版本是最新的版本。 這是因為 runbook 作業可能會在相同的沙箱中執行。 如果沙箱已經明確載入特定版本號碼的模組，因為該沙箱中先前的工作所說的是，該沙箱中的未來作業將不會自動載入該模組的最新版本。 這是因為它的某些版本已經載入沙箱中。

若為 DSC 資源，請使用下列命令來指定特定版本：

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>說明資訊

在您的模組中包含每個 Cmdlet 的概要、描述和說明 URI。 在 PowerShell 中，您可以使用 `Get-Help` Cmdlet 來定義 Cmdlet 的說明資訊。 下列範例示範如何在 **.psm1** 模組檔案中定義概要和說明 URI。

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

  提供此資訊，即可透過 PowerShell 主控台中的 `Get-Help` Cmdlet 來顯示說明文字。 此文字也會顯示於 Azure 入口網站中。

  ![整合模組說明的螢幕擷取畫面](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>連線類型

如果模組連線至外部服務，請使用[自訂整合模組](#custom-modules)來定義連線類型。 模組中的每個 Cmdlet 都應該接受該連線類型的執行個體 (連線物件) 作為參數。 使用者會在每次呼叫 Cmdlet 時，將連線資產的參數對應至 Cmdlet 的對應參數。

![在 Azure 入口網站中使用自訂連線](../media/modules/connection-create-new.png)

下列 Runbook 範例會使用名為 `ContosoConnection` 的 Contoso 連線資產來存取 Contoso 資源，並從外部服務傳回資料。 在此範例中，欄位會對應至 `PSCredential` 物件的 `UserName` 和 `Password` 屬性，然後傳遞給 Cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

更容易且更好處理此行為的方式是直接將連線物件傳遞給 Cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

您可以透過讓 Cmdlet 能夠直接接受連線物件作為參數，而不是只接受連線欄位作為參數，來讓 Cmdlet 具有類似行為。 通常您會想要針對每個 Cmdlet 設定參數，讓未使用自動化的使用者可以呼叫 Cmdlet，而不需建構雜湊表作為連線物件。 `UserAccount` 參數集可用來傳遞連線欄位屬性。 `ConnectionObject` 則可讓您直接傳遞連線。

### <a name="output-type"></a>輸出類型

為模組中的所有 Cmdlet 定義輸出類型。 為 Cmdlet 定義輸出類型，可讓設計階段的 IntelliSense 在撰寫期間協助判斷 Cmdlet 的輸出屬性。 在撰寫圖形化 Runbook 期間，此練習特別有幫助，因為設計階段的知識是讓模組使用者獲得容易使用體驗的關鍵。

新增 `[OutputType([<MyOutputType>])]`，其中 `MyOutputType` 是有效的類型。 若要深入了解 `OutputType`，請參閱[關於函式 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下列程式碼是將 `OutputType` 新增至 Cmdlet 的範例：

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

  ![圖形化 Runbook 輸出類型的螢幕擷取畫面](../media/modules/runbook-graphical-module-output-type.png)

  此行為類似於 Cmdlet 在 PowerShell ISE 整合服務環境中輸出的「自動提示」功能，但不需加以執行。

  ![POSH IntelliSense 的螢幕擷取畫面](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet 狀態

讓模組中的所有 Cmdlet 變成無狀態。 多個 Runbook 作業可以同時在同一個 `AppDomain` 以及相同的程序和沙箱中執行。 如果那些層級上有任何共用的狀態，則作業可能會互相影響。 這種行為可能導致間歇性且難以診斷的問題。 以下是不需進行的作業範例：

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

確定模組會完全包含於可使用 xcopy 複製的套件中。 自動化模組會在 Runbook 執行時，散發到自動化沙箱。 模組必須在其執行所在的主機以外單獨運作。

您應該能夠壓縮並移動模組套件，在將其匯入至另一部主機的 PowerShell 環境時，讓其能夠正常運作。 若要這樣做，請確定模組不會相依於在將模組匯入自動化時所壓縮之模組資料夾以外的任何檔案。

您的模組不應相依於主機上任何唯一的登錄設定。 範例是安裝產品時所進行的設定。

### <a name="module-file-paths"></a>模組檔案路徑

確定模組中所有檔案的路徑均少於 140 個字元。 大小超過 140 個字元的路徑會導致匯入 Runbook 時發生問題。 自動化無法使用 `Import-Module`，將路徑大小超過 140 個字元的檔案匯入至 PowerShell 工作階段。

## <a name="import-modules"></a>匯入模組

此節定義數種方式，可讓您將模組匯入至您的自動化帳戶。

### <a name="import-modules-in-the-azure-portal"></a>在 Azure 入口網站中匯入模組

在 Azure 入口網站中匯入模組：

1. 移至自動化帳戶。
2. 在 [共用資源] 底下，選取 [模組]。
3. 選取 [新增模組]。
4. 選取包含您模組的 **.zip** 檔案。
5. 選取 [確定] 以開始匯入程序。

### <a name="import-modules-by-using-powershell"></a>使用 PowerShell 匯入模組

您可以使用 [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) \(英文\) Cmdlet，將模組匯入至您的自動化帳戶。 此 Cmdlet 會取得模組 .zip 套件的 URL。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

您也可以使用相同的 Cmdlet，直接從 PowerShell 資源庫匯入模組。 請務必從 [PowerShell 資源庫](https://www.powershellgallery.com) \(英文\) 擷取 `ModuleName` 和 `ModuleVersion`。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>從 PowerShell 資源庫匯入模組

您可以直接從資源庫或您的自動化帳戶匯入 [PowerShell 資源庫](https://www.powershellgallery.com) \(英文\) 模組。

直接從 PowerShell 資源庫匯入模組：

1. 移至 https://www.powershellgallery.com ，然後搜尋要匯入的模組。
2. 在 [安裝選項] 下方的 [Azure 自動化] 索引標籤中，選取 [部署至 Azure 自動化]。 此動作會開啟 Azure 入口網站。 
3. 在 [匯入] 頁面上，選取您的自動化帳戶，然後選取 [確定]。

![PowerShell 資源庫匯入模組的螢幕擷取畫面](../media/modules/powershell-gallery.png)

直接從您的自動化帳戶匯入 PowerShell 資源庫模組：

1. 在 [共用資源] 底下，選取 [模組]。 
2. 選取 [瀏覽資源庫]，然後在資源庫中搜尋模組。 
3. 選取要匯入的模組，然後選取 [匯入]。 
4. 選取 [確定] 以開始匯入程序。

![從 Azure 入口網站匯入 PowerShell 資源庫模組的螢幕擷取畫面](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>刪除模組

如果您遇到有關模組的問題，或需要復原為舊版模組，您可以從自動化帳戶中將其刪除。 當您建立自動化帳戶時，就無法刪除已匯入的[預設模組](#default-modules)原始版本。 如果要刪除的模組是其中一個[預設模組](#default-modules)的較新版本，則會復原為隨您的自動化帳戶一起安裝的版本。 否則，會移除您從自動化帳戶中刪除的任何模組。

### <a name="delete-modules-in-the-azure-portal"></a>在 Azure 入口網站中刪除模組

在 Azure 入口網站中移除模組：

1. 移至自動化帳戶。 在 [共用資源] 底下，選取 [模組]。
2. 選取您想要移除的模組。
3. 在 [模組] 頁面上，選取 [刪除]。 如果此模組是其中一個[預設模組](#default-modules)，則其會復原為建立自動化帳戶時就已存在的版本。

### <a name="delete-modules-by-using-powershell"></a>使用 PowerShell 刪除模組

若要透過 PowerShell 移除模組，請執行下列命令：

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>後續步驟

* 如需使用 Azure PowerShell 模組的詳細資訊，請參閱 [開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。

* 若要深入瞭解如何建立 PowerShell 模組，請參閱 [撰寫 Windows PowerShell 模組](/powershell/scripting/developer/module/writing-a-windows-powershell-module)。
