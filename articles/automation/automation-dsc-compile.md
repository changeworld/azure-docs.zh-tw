---
title: 編譯 Azure Automation State Configuration 中的組態
description: 此文章說明如何針對 Azure 自動化編譯期望狀態設定 (DSC) 組態。
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4a46837e366caf2860aa0d6597ec7aebc94905a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373540"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>編譯 Azure Automation State Configuration 中的 DSC 組態

您可以透過下列方式，在 Azure 自動化狀態設定中編譯 Desired State Configuration （DSC）設定：

- Azure 狀態設定編譯服務
  - 具有互動式使用者介面的初學者方法
   - 輕鬆追蹤工作狀態

- Windows PowerShell
  - 在本機工作站或組建服務上從 Windows PowerShell 呼叫
  - 與開發測試管線整合
  - 提供複雜的參數值
  - 使用大規模的節點和非節點資料
  - 大幅改善效能

如需編譯詳細資料，請參閱[具有 Azure Resource Manager 範本的 Desired State Configuration 延伸模組](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)。

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>在 Azure 狀態設定中編譯 DSC 設定

### <a name="portal"></a>Portal

1. 從您的自動化帳戶，按一下 [State Configuration (DSC)]。
1. 按一下 [組態] 索引標籤，然後按一下要編譯的組態名稱。
1. 按一下 [編譯]。
1. 如果設定沒有任何參數，系統會提示您確認是否要進行編譯。 如果設定具有參數，則會開啟 [**編譯**設定] 分頁，讓您可以提供參數值。
1. [編譯工作] 頁面隨即開啟，讓您可以追蹤編譯作業狀態。 您也可以使用此頁面來追蹤放在 Azure 自動化狀態設定提取伺服器上的節點設定（MOF 設定檔）。

### <a name="azure-powershell"></a>Azure PowerShell

您可以使用[AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob)開始使用 Windows PowerShell 進行編譯。 下列範例程式碼會開始編譯稱為 SampleConfig 的 DSC 設定。

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**AzAutomationDscCompilationJob**會傳回可用來追蹤工作狀態的編譯工作物件。 接著，您可以使用此編譯工作物件搭配[AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob)來判斷編譯工作的狀態，並[取得 AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration)以查看其資料流程（輸出）。 下列範例會開始編譯 SampleConfig 設定，等待它完成，然後顯示其資料流程。

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

DSC 組態中的參數宣告 (包括參數類型和屬性) 的運作方式與 Azure 自動化 Runbook 中相同。 若要深入了解 Runbook 參數，請參閱 [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 。

下列範例會使用兩*個名為*工作名稱和*IsPresent*的參數，來判斷在編譯期間產生的 parametersexample.sample 節點設定中的屬性值。

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

您可以在 Azure 自動化狀態設定入口網站中或使用 Azure PowerShell，編譯使用基本參數的 DSC 設定。

#### <a name="portal"></a>Portal

在入口網站中，您可以在按一下 [編譯]後輸入參數值。

![組態編譯參數](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 需要[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)中的參數，其中索引鍵符合參數名稱，而值等於參數值。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

如需如何將 PSCredentials 傳入作為參數的相關資訊，請參閱下方的[認證資產](#credential-assets)。

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure 自動化中包含複合資源的編譯設定

**複合資源**功能可讓您在設定中使用 DSC 設定作為嵌套資源。 這項功能可讓您將多個設定應用到單一資源。 如需深入瞭解複合資源，請參閱[複合資源：使用 DSC 設定做為資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。

> [!NOTE]
> 為了使包含複合資源的設定正確編譯，您必須先匯入到 Azure 自動化複合所依賴的任何 DSC 資源。 新增 DSC 複合資源與將任何 PowerShell 模組新增至 Azure 自動化並無不同。 此程式記載于[管理 Azure 自動化中的模組](/azure/automation/shared-resources/modules)。

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>在 Azure 自動化中編譯設定時管理 ConfigurationData

**ConfigurationData** 可讓您在使用 PowerShell DSC 時，將結構化設定與任何環境特定設定進行區隔。 如需詳細資訊，請參閱[在 POWERSHELL DSC 中分隔 "What" 與 "Where"](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)。

> [!NOTE]
> 在 Azure 自動化狀態設定中進行編譯時，您可以使用 Azure PowerShell 中的**ConfigurationData** ，而不是在 Azure 入口網站中。

下列範例 DSC 設定會透過 $ConfigurationData 和 $AllNodes 關鍵字來使用**ConfigurationData** 。 在此範例中，您也需要[請參閱 xwebadministration 模組](https://www.powershellgallery.com/packages/xWebAdministration/)。

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

您可以使用 Windows PowerShell 來編譯上述 DSC 設定。 下列腳本會將兩個節點設定新增至 Azure 自動化狀態設定提取服務： Configurationdatasample.myvm1. MyVM1 和 Configurationdatasample.myvm1. MyVM3。

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

Azure 自動化狀態設定和 runbook 中的資產參考都相同。 如需詳細資訊，請參閱下列內容：

- [憑證](automation-certificates.md)
- [連線](automation-connections.md)
- [認證](automation-credentials.md)
- [變數](automation-variables.md)

#### <a name="credential-assets"></a>認證資產

Azure 自動化中的 DSC 設定可以使用**AutomationPSCredential** Cmdlet 來參考自動化認證資產。 如果設定具有指定**PSCredential**物件的參數，請使用**AutomationPSCredential** ，方法是將 Azure 自動化認證資產的字串名稱傳遞給 Cmdlet，以取得認證。 然後將該物件用於需要**PSCredential**物件的參數。 會在背景中取出具有該名稱的 Azure 自動化認證資產，並傳遞至設定。 下列範例示範此案例的實際運作方式。

要在節點組態 (MOF 組態文件) 中保持認證的安全性，需要在節點組態 MOF 檔案中為認證加密。 目前您必須授與 PowerShell DSC 許可權，以便在節點設定 MOF 產生期間以純文字輸出認證。 PowerShell DSC 並不知道 Azure 自動化在透過編譯作業產生整個 MOF 檔案之後，就會將它加密。

您可以告訴 PowerShell DSC，使用設定資料在產生的節點設定 Mof 中以純文字輸出認證是正常的。 您應該針對 DSC 設定中出現的每個節點區塊名稱，透過**ConfigurationData**傳遞 `PSDscAllowPlainTextPassword = $true`，並使用認證。

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

您可以使用 PowerShell 來編譯上述 DSC 設定。 下列 PowerShell 程式碼會將兩個節點設定新增至 Azure 自動化狀態設定提取伺服器： Credentialsample.myvm2. MyVM1 和 Credentialsample.myvm2. MyVM2。

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
> 編譯完成時，您可能會收到錯誤訊息「未匯入「Microsoft. PowerShell. 管理」模組，因為已經匯入 ' Microsoft. PowerShell 管理」嵌入式管理單元。」 您可以放心地忽略此訊息。

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>在 Windows PowerShell 中編譯 DSC 設定

您也可以匯入在 Azure 外部編譯的節點組態 (MOF)。 匯入包含從開發人員工作站或服務（如[Azure DevOps](https://dev.azure.com)）進行編譯。 這個方法有多項優點，包括效能和可靠性。

Windows PowerShell 中的編譯也提供簽署設定內容的選項。 DSC 代理程式會在受管理節點上本機驗證已簽署的節點設定。 驗證可確保套用至節點的設定來自于已授權的來源。

> [!NOTE]
> 節點組態檔不得大於 1 MB，才能匯入到 Azure 自動化。

如需有關簽署節點設定的詳細資訊，請參閱[WMF 5.1 中的增強功能-如何簽署設定和模組](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)。

### <a name="compile-the-dsc-configuration"></a>編譯 DSC 設定

在 Windows PowerShell 中編譯 DSC 設定的套裝程式含在 PowerShell DSC 檔中：[寫入、編譯及](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)套用設定。
您可以從開發人員工作站或在組建服務中執行此程式，例如[Azure DevOps](https://dev.azure.com)。 接著，您可以將編譯設定所產生的 MOF 檔案匯入 Azure 狀態設定服務。

### <a name="import-a-node-configuration-in-the-azure-portal"></a>匯入 Azure 入口網站中的節點設定

1. 從您的自動化帳戶，按一下 [組態管理] 下方的 [State configuration (DSC)]。
1. 在 [State configuration （DSC **）] 頁面**上，按一下 [設定] 索引標籤，然後按一下 [ **+ 新增**]。
1. 在 [匯入] 頁面上，按一下 [**節點設定檔**] 文字方塊旁的資料夾圖示，以流覽本機電腦上的節點設定檔（MOF）。

   ![瀏覽本機檔案](./media/automation-dsc-compile/import-browse.png)

1. 在 [設定**名稱**] 欄位中輸入名稱。 此名稱必須符合已編譯節點組態的組態名稱。
1. 按一下 [確定]。

### <a name="import-a-node-configuration-with-azure-powershell"></a>使用 Azure PowerShell 匯入節點設定

您可以使用[AzAutomationDscNodeConfiguration 指令程式](/powershell/module/az.automation/import-azautomationdscnodeconfiguration)，將節點設定匯入到您的自動化帳戶。

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[Azure 自動化狀態設定](automation-dsc-getting-started.md)使用者入門。
- 若要瞭解如何編譯 DSC 設定，讓您可以將它們指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯](automation-dsc-compile.md)設定。
- 如需 PowerShell Cmdlet 參考，請參閱[Azure 自動化狀態設定 Cmdlet](/powershell/module/az.automation)。
- 如需定價資訊，請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 持續部署至虛擬機器](automation-dsc-cd-chocolatey.md)。
