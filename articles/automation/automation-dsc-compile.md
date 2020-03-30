---
title: 編譯 Azure Automation State Configuration 中的組態
description: 此文章說明如何針對 Azure 自動化編譯期望狀態設定 (DSC) 組態。
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: 48593920bdfcf743fceaeaeec891c0d5c4f2e108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057641"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>編譯 Azure Automation State Configuration 中的 DSC 組態

您可以通過以下方式在 Azure 自動化狀態配置中編譯所需的狀態配置 （DSC） 配置：

- Azure 狀態配置編譯服務
  - 具有互動式使用者介面的初學者方法
   - 輕鬆追蹤工作狀態

- Windows PowerShell
  - 從本地工作站或生成服務上的 Windows PowerShell 呼叫
  - 與開發測試管道集成
  - 提供複雜的參數值
  - 大規模使用節點和非節點資料
  - 顯著的性能改進

有關編譯詳細資訊，請參閱[Azure 資源管理器範本所需的狀態配置擴展](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)。

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>在 Azure 狀態配置中編譯 DSC 配置

### <a name="portal"></a>入口網站

1. 從您的自動化帳戶，按一下 [State Configuration (DSC)]****。
1. 按一下 [組態]**** 索引標籤，然後按一下要編譯的組態名稱。
1. 按一下 [編譯] ****。
1. 如果配置沒有參數，系統將提示您確認是否要編譯該配置。 如果配置具有參數，則將打開 **"編譯配置"** 邊欄選項卡，以便提供參數值。
1. 將打開"編譯作業"頁，以便可以跟蹤編譯作業狀態。 您還可以使用此頁面跟蹤放置在 Azure 自動化狀態配置拉取伺服器上的節點配置（MOF 配置文檔）。

### <a name="azure-powershell"></a>Azure PowerShell

您可以使用[啟動-Az自動化編譯作業](/powershell/module/az.automation/start-azautomationdsccompilationjob)開始使用 Windows PowerShell 編譯。 以下示例代碼開始編譯稱為示例配置的 DSC 配置。

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**啟動-AzAutomationDsc編譯作業**返回可用於跟蹤作業狀態的編譯工作物件。 然後，您可以使用此編譯工作物件與[Get-AzAutomationDsc 編譯作業](/powershell/module/az.automation/get-azautomationdsccompilationjob)來確定編譯作業的狀態，[然後獲取-AzAutomationD編譯作業輸出](/powershell/module/az.automation/get-azautomationdscconfiguration)以查看其流（輸出）。 以下示例開始編譯 SampleConfig 配置，等待它完成，然後顯示其流。

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>聲明基本參數

DSC 組態中的參數宣告 (包括參數類型和屬性) 的運作方式與 Azure 自動化 Runbook 中相同。 若要深入了解 Runbook 參數，請參閱 [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 。

下列範例使用兩個名為 *FeatureName* 和 *IsPresent* 的參數，來判斷在編譯期間產生的 ParametersExample.sample 節點組態中的屬性值。

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

可以編譯在 Azure 自動化狀態配置門戶或與 Azure PowerShell 中使用基本參數的 DSC 配置。

#### <a name="portal"></a>入口網站

在入口網站中，您可以在按一下 [編譯] **** 後輸入參數值。

![組態編譯參數](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 需要[雜湊表中](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的參數，其中鍵與參數名稱匹配，值等於參數值。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

如需如何將 PSCredentials 傳入作為參數的相關資訊，請參閱下方的[認證資產](#credential-assets)。

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>編譯包含 Azure 自動化中複合資源的配置

**複合資源**功能允許您將 DSC 配置用作配置中的嵌套資源。 此功能支援將多個配置應用於單個資源。 請參閱[複合資源：使用 DSC 配置作為資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)來瞭解有關複合資源的更多資訊。

> [!NOTE]
> 因此，包含複合資源的配置必須首先導入 Azure 自動化，以將複合依賴于的任何 DSC 資源導入 Azure 自動化。 添加 DSC 複合資源與向 Azure 自動化添加任何 PowerShell 模組沒有什麼不同。 此過程記錄[在 Azure 自動化中的管理模組中](/azure/automation/shared-resources/modules)。

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>在 Azure 自動化中編譯配置時管理配置資料

**ConfigurationData** 可讓您在使用 PowerShell DSC 時，將結構化設定與任何環境特定設定進行區隔。 有關詳細資訊，請參閱在[PowerShell DSC 中將"什麼"與"地點"分開](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)。

> [!NOTE]
> 在 Azure 自動化狀態配置中編譯時，可以在 Azure PowerShell 中使用配置資料，但不能在 Azure 門戶中使用**配置資料**。

下列範例 DSC 組態會透過 $ConfigurationData 和 $AllNodes 關鍵字來使用 **ConfigurationData**。 對於此示例，您還需要[xWeb 管理模組](https://www.powershellgallery.com/packages/xWebAdministration/)。

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

您可以使用 Windows PowerShell 編譯前面的 DSC 配置。 以下腳本將兩個節點配置添加到 Azure 自動化狀態配置拉取服務：配置資料示例.MyVM1 和配置資料示例.MyVM3。

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>在編譯期間處理 Azure 自動化中的資產

在 Azure 自動化狀態配置和 Runbook 中，資產引用都是相同的。 如需詳細資訊，請參閱下列：

- [證書](automation-certificates.md)
- [連線](automation-connections.md)
- [認證](automation-credentials.md)
- [變數](automation-variables.md)

#### <a name="credential-assets"></a>憑據資產

Azure 自動化中的 DSC 配置可以使用**獲取自動化 PS憑據**Cmdlet 引用自動化憑據資產。 如果配置具有指定**PS憑據**物件的參數，請使用**獲取自動化PS憑據**，通過將 Azure 自動化憑據資產的字串名稱傳遞給 Cmdlet 來檢索憑據。 然後，將該物件用於需要**PSCredential**物件的參數。 會在背景中取出具有該名稱的 Azure 自動化認證資產，並傳遞至設定。 下面的示例顯示了此方案在操作中。

要在節點組態 (MOF 組態文件) 中保持認證的安全性，需要在節點組態 MOF 檔案中為認證加密。 目前，您必須授予 PowerShell DSC 在節點配置 MOF 生成期間以純文字輸出憑據的許可權。 PowerShell DSC 不知道 Azure 自動化在生成整個 MOF 檔案後通過編譯作業對其進行加密。

您可以告訴 PowerShell DSC，可以使用配置資料在生成的節點配置 MOF 中以純文字輸出憑據。 您應該`PSDscAllowPlainTextPassword = $true`通過**配置資料來**傳遞 DSC 配置中顯示的每個節點塊名稱並使用憑據。

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

您可以使用 PowerShell 來編譯上述 DSC 設定。 以下 PowerShell 代碼將兩個節點配置添加到 Azure 自動化狀態配置拉取伺服器：憑據示例.MyVM1 和憑據示例.MyVM2。

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
> 編譯完成後，您可能會收到錯誤訊息"'Microsoft.PowerShell.Management'"模組未導入，因為"Microsoft.PowerShell.管理"的卡入式已導入。 您可以放心忽略這個訊息。

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>在 Windows PowerShell 中編譯 DSC 配置

您也可以匯入在 Azure 外部編譯的節點組態 (MOF)。 導入包括來自開發人員工作站或服務（如[Azure DevOps）](https://dev.azure.com)的編譯。 這種方法具有多種優點，包括性能和可靠性。

在 Windows PowerShell 中編譯還提供對配置內容進行簽名的選項。 DSC 代理在託管節點上本地驗證已簽名的節點配置。 驗證可確保應用於節點的配置來自授權源。

> [!NOTE]
> 節點組態檔不得大於 1 MB，才能匯入到 Azure 自動化。

有關節點配置簽名的詳細資訊，請參閱[WMF 5.1 中的改進 - 如何對配置和模組進行簽名](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)。

### <a name="compile-the-dsc-configuration"></a>編譯 DSC 配置

在 Windows PowerShell 中編譯 DSC 配置的過程包含在 PowerShell DSC 文檔["寫入、編譯和應用配置"中](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)。
可以從開發人員工作站或在生成服務（如[Azure DevOps）](https://dev.azure.com)中執行此過程。 然後，可以通過將配置編譯到 Azure 狀態佈建服務來導入生成的 MOF 檔案。

### <a name="import-a-node-configuration-in-the-azure-portal"></a>在 Azure 門戶中導入節點配置

1. 從您的自動化帳戶，按一下 [組態管理]**** 下方的 [State configuration (DSC)]****。
1. 在"狀態配置 （DSC）"頁上，按一下"**配置"** 選項卡，然後按一下 **"添加**"。
1. 在"導入"頁上，按一下 **"節點設定檔"** 文字方塊旁邊的資料夾圖示，以流覽本地電腦上的節點設定檔 （MOF）。

   ![瀏覽本機檔案](./media/automation-dsc-compile/import-browse.png)

1. 在 **"配置名稱"** 欄位中輸入名稱。 此名稱必須符合已編譯節點組態的組態名稱。
1. 按一下 [確定]****。

### <a name="import-a-node-configuration-with-azure-powershell"></a>使用 Azure PowerShell 導入節點配置

您可以使用[導入-AzAutomationDscNode配置](/powershell/module/az.automation/import-azautomationdscnodeconfiguration)Cmdlet 將節點配置導入到自動化帳戶中。

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>後續步驟

- 要開始，請參閱[開始使用 Azure 自動化狀態配置](automation-dsc-getting-started.md)。
- 要瞭解如何編譯 DSC 配置以便將它們分配給目標節點，請參閱[在 Azure 自動化狀態配置中編譯配置](automation-dsc-compile.md)。
- 有關 PowerShell Cmdlet 引用，請參閱[Azure 自動化狀態配置 Cmdlet](/powershell/module/az.automation)。
- 有關定價資訊，請參閱[Azure 自動化狀態配置定價](https://azure.microsoft.com/pricing/details/automation/)。
- 要查看在連續部署管道中使用 Azure 自動化狀態配置的示例，請參閱[使用 Azure 自動化狀態配置和巧克力 連續部署到虛擬機器](automation-dsc-cd-chocolatey.md)。
