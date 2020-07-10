---
title: 編譯 Azure 自動化狀態設定中的 DSC 組態
description: 此文章說明如何針對 Azure 自動化編譯 Desired State Configuration (DSC) 組態。
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 3bb42886c653afbdf8975b532bd2e1e1c3c63ce9
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186532"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>編譯 Azure 自動化狀態設定中的 DSC 組態

您可以透過下列方式，在 Azure 自動化狀態設定中編譯 Desired State Configuration (DSC) 組態：

- Azure 狀態設定編譯服務
  - 互動式使用者介面的初學者方法
   - 輕鬆追蹤工作狀態

- Windows PowerShell
  - 在本機工作站或組建服務上從 Windows PowerShell 呼叫
  - 與開發測試管線整合
  - 提供複雜的參數值
  - 使用大規模的節點和非節點資料
  - 大幅改善效能

您也可以使用 Azure Resource Manager 範本搭配 Azure Desired State Configuration (DSC) 延伸模組，將組態推送至您的 Azure VM。 Azure DSC 延伸模組會使用「Azure VM 代理程式」架構來傳遞、套用在 Azure VM 上執行的 DSC 組態，並針對這些組態提出報告。 如需使用 Azure Resource Manager 範本的編譯詳細資料，請參閱[採用 Azure Resource Manager 範本的 Desired State Configuration 延伸模組](../virtual-machines/extensions/dsc-template.md#details)。 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>在 Azure 狀態設定中編譯 DSC 組態

### <a name="portal"></a>入口網站

1. 在您的自動化帳戶，按一下 [State Configuration (DSC)]。
1. 按一下 [組態] 索引標籤，然後按一下要編譯的組態名稱。
1. 按一下 [編譯] 。
1. 如果組態沒有參數，系統會提示您確認是否要加以編譯。 如果組態有參數，即會開啟 [編譯組態] 刀鋒視窗，讓您可以提供參數值。
1. [編譯工作] 頁面隨即開啟，讓您可以追蹤編譯作業狀態。 您也可以使用此頁面來追蹤放在 Azure 自動化狀態設定提取伺服器上的節點組態 (MOF 組態文件)。

### <a name="azure-powershell"></a>Azure PowerShell

您可以使用 [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob)，開始利用 Windows PowerShell 進行編譯。 下列範例程式碼會啟動名為 **SampleConfig** 的 DSC 組態編譯。

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` 會傳回可用來追蹤作業狀態的編譯作業物件。 接著，您可以使用此編譯作業物件與 [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) 來判斷編譯作業的狀態，並使用 [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) 來檢視其資料流 (輸出)。 下列範例會啟動 SampleConfig 組態的編譯，並在編譯完成後顯示其串流。

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>宣告基本參數

DSC 組態中的參數宣告 (包括參數類型和屬性) 的運作方式與 Azure 自動化 Runbook 中相同。 若要深入了解 Runbook 參數，請參閱 [在 Azure 自動化中啟動 Runbook](./start-runbooks.md) 。

下列範例會使用 `FeatureName` 和 `IsPresent` 參數，來判斷在編譯期間產生的 **ParametersExample.sample** 節點組態中的屬性值。

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

您可以在 Azure 自動化狀態設定入口網站或 Azure PowerShell 中編譯使用基本參數的 DSC 組態。

#### <a name="portal"></a>入口網站

在入口網站中，您可以在按一下 [編譯] 後輸入參數值。

![組態編譯參數](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 會要求您將參數放入 [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 中，其中的索引鍵必須符合參數名稱，且值等於參數值。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

如需如何將 `PSCredential` 物件傳入做為參數的相關資訊，請參閱下方的[認證資產](#credential-assets)。

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure 自動化中包含複合資源的編譯組態

**複合資源**功能可讓您使用 DSC 組態做為組態內的巢狀資源。 這項功能可讓您將多個組態應用到單一資源。 請參閱[複合資源：如需深入了解複合資源，請使用 DSC 組態做為資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。

> [!NOTE]
> 為了使包含複合資源的組態正確編譯，您必須先將複合所依賴的任何 DSC 資源匯入 Azure 自動化。 新增 DSC 複合資源與將任何 PowerShell 模組新增至 Azure 自動化並無差異。 此程式記載於[在 Azure 自動化中管理模組](./shared-resources/modules.md)。

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>在編譯 Azure 自動化中的組態時管理 ConfigurationData

`ConfigurationData` 可讓您在使用 PowerShell DSC 時，將結構化組態與任何環境特定組態進行區隔。 如需詳細資訊，請參閱[分隔 PowerShell DSC 中的「結構組態」與「環境組態」](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)。

> [!NOTE]
> 在 Azure 自動化狀態設定中進行編譯時，您可以使用 Azure PowerShell 中、而非 Azure 入口網站中的 `ConfigurationData`。

下列範例 DSC 組態會透過 `$ConfigurationData` 和 `$AllNodes` 關鍵字來使用 `ConfigurationData`。 在此範例中，您也需要 [xWebAdministration 模組](https://www.powershellgallery.com/packages/xWebAdministration/)。

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

您可以使用 Windows PowerShell 來編譯上述 DSC 組態。 下列指令碼會將兩個節點組態新增至 Azure 自動化狀態設定提取伺服器：**ConfigurationDataSample.MyVM1** 和 **ConfigurationDataSample.MyVM3**。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>在編譯期間使用 Azure 自動化中的資產

Azure 自動化狀態設定和 Runbook 中的資產參考是相同的。 如需詳細資訊，請參閱下列：

- [憑證](./shared-resources/certificates.md)
- [連線](automation-connections.md)
- [認證](./shared-resources/credentials.md)
- [變數](./shared-resources/variables.md)

#### <a name="credential-assets"></a>認證資產

Azure 自動化中的 DSC 組態可以使用 `Get-AutomationPSCredential` Cmdlet 參考自動化認證資產。 如果組態具有指定 `PSCredential` 物件的參數，請將 Azure 自動化認證資產的字串名稱傳遞給 Cmdlet 來使用 `Get-AutomationPSCredential` 以擷取認證。 然後將該物件用於需要 `PSCredential` 物件的參數。 會在背景中取出具有該名稱的 Azure 自動化認證資產，並傳遞至設定。 下列範例示範此案例的實際運作方式。

要在節點組態 (MOF 組態文件) 中保持認證的安全性，需要在節點組態 MOF 檔案中為認證加密。 目前您必須授與 PowerShell DSC 權限，以便在產生節點組態 MOF 期間，以純文字輸出認證。 PowerShell DSC 並不知道 Azure 自動化在透過編譯作業產生整個 MOF 檔案之後，就會將其加密。

您可以告知 PowerShell DSC，使用組態資料在所產生節點組態 MOF 中以純文字輸出認證是正常的。 您應針對每個出現在 DSC 組態中且使用認證的節點區塊名稱，透過 `ConfigurationData` 傳遞 `PSDscAllowPlainTextPassword = $true`。

下列範例說明使用自動化認證資產的 DSC 組態。

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

您可以使用 PowerShell 來編譯上述 DSC 設定。 下列 PowerShell 程式碼會將兩個節點組態新增至 Azure 自動化狀態設定提取伺服器：**CredentialSample.MyVM1** 和 **CredentialSample.MyVM2**。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 編譯完成時，您可能會收到錯誤訊息 `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` 您可以放心地忽略此訊息。

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>在 Windows PowerShell 中編譯 DSC 組態

PowerShell DSC 文件內包含在 Windows PowerShell 中編譯 DSC 組態的程序，[寫入、編譯及套用組態](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)。
您可以從開發人員工作站或在組建服務內執行此程序，例如 [Azure DevOps](https://dev.azure.com)。 接著，您可以將編譯組態所產生的 MOF 檔案匯入 Azure 狀態設定服務中。

在 Windows PowerShell 中進行編譯也提供簽署組態內容的選項。 DSC 代理程式會在受控節點上本機驗證已簽署的節點組態。 驗證可確保套用至節點的組態來自已授權的來源。

您也可以匯入在 Azure 外部編譯的節點組態 (MOF)。 匯入包含來自開發人員工作站或服務 (如 [Azure DevOps](https://dev.azure.com)) 的編譯。 這個方法有多項優點，包括效能和可靠性。

> [!NOTE]
> 節點組態檔不得大於 1 MB，才能匯入 Azure 自動化中。

如需簽署節點組態的詳細資訊，請參閱 [WMF 5.1 的改進 - 如何簽署組態和模組](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)。

### <a name="import-a-node-configuration-in-the-azure-portal"></a>在 Azure 入口網站中匯入節點組態

1. 從您的自動化帳戶中，按一下 [組態管理] 下方的 [State configuration (DSC)]。
1. 在 [State Configuration (DSC)] 頁面中，按一下 [組態] 索引標籤，然後按一下 [+ 新增]。
1. 在 [匯入] 頁面中，按一下資料夾圖示旁的 [節點組態檔] 欄位，以瀏覽本機電腦上的節點組態 MOF 檔案。

   ![瀏覽本機檔案](./media/automation-dsc-compile/import-browse.png)

1. 在 [組態名稱] 欄位中輸入名稱。 此名稱必須符合已編譯節點組態的組態名稱。
1. 按一下 [確定]。

### <a name="import-a-node-configuration-with-azure-powershell"></a>使用 Azure PowerShell 匯入節點組態

您可以使用 [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) Cmdlet，將節點組態匯入您的自動化帳戶中。

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 若要了解如何編譯 DSC 組態，以將其指派給目標節點，請參閱[編譯 Azure 自動化狀態設定中的 DSC 組態](automation-dsc-compile.md)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)。
- 如需在持續部署管線中使用狀態設定的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
