---
title: 如何為 Windows 創建來賓配置策略
description: 瞭解如何為 Windows 創建 Azure 策略來賓配置策略。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24069ff6518c4244026378e48216d4568fffeb8a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365474"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>如何為 Windows 創建來賓配置策略

在創建自訂策略之前，最好在[Azure 策略來賓配置](../concepts/guest-configuration.md)頁中閱讀概念概述資訊。
 
要瞭解如何為 Linux 創建來賓配置策略，請參閱[如何為 Linux 創建來賓配置策略](./guest-configuration-create-linux.md)的頁面

審核 Windows 時，來賓配置使用[所需的狀態配置](/powershell/scripting/dsc/overview/overview)（DSC） 資源模組和設定檔。 DSC 配置定義電腦應處於的條件。
如果配置評估失敗，將觸發策略效果**審核IfNotExists，** 並且電腦被視為**不符合**。

[Azure 策略來賓配置](../concepts/guest-configuration.md)只能用於審核電腦內的設置。 機器內部設置的修正尚不可用。

使用以下操作創建自己的配置以驗證 Azure 或非 Azure 電腦的狀態。

> [!IMPORTANT]
> 具有來賓配置的自訂策略是預覽功能。

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

使用 PowerShell 中的來賓配置模組，創建來賓設定項目、自動測試專案、創建策略定義和發佈策略是完全可自動的。 該模組可以安裝在運行 Windows、macOS 或 Linux 的電腦上，其中 PowerShell 6.2 或更高版本在本地運行，或者使用[Azure 雲外殼](https://shell.azure.com)，或者使用[Azure PowerShell 核心 Docker 映射](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 上還不支援配置編譯。

### <a name="base-requirements"></a>基底需求

可安裝模組的作業系統：

- Linux
- macOS
- Windows

來賓配置資源模組需要以下軟體：

- PowerShell 6.2 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/scripting/install/installing-powershell)操作。
- Azure 電源外殼 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
  - 只需要 AZ 模組"Az.帳戶"和"Az.Resources"。

### <a name="install-the-module"></a>安裝模組

要在 PowerShell 中安裝**來賓配置**模組，請執行以下操作：

1. 從 PowerShell 提示字元中執行下列命令：

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 驗證模組是否已導入：

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows 的來賓設定項目和策略

來賓配置使用 PowerShell 所需的狀態配置作為語言抽象來編寫要在 Windows 中審核的內容。 代理載入 PowerShell 6.2 的獨立實例，因此與 Windows PowerShell 5.1 中 PowerShell DSC 的使用沒有衝突，並且不需要預先安裝 PowerShell 6.2 或更高版本。

有關 DSC 概念和術語的概述，請參閱[PowerShell DSC 概述](/powershell/scripting/dsc/overview/overview)。

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>來賓配置模組與 Windows PowerShell DSC 模組有何不同

當來賓配置審核電腦時，它首先運行`Test-TargetResource`以確定其是否處於正確的狀態。 函數返回的布林值確定來賓分配的 Azure 資源管理器狀態是否應為"相容/不相容"。 接下來，提供程式將`Get-TargetResource`運行以返回每個設置的目前狀態，以便提供有關電腦不符合原因的詳細資訊，或確認目前狀態是否合規。

### <a name="get-targetresource-requirements"></a>獲取目標資源要求

該功能`Get-TargetResource`對 Windows 所需狀態配置不需要的來賓配置有特殊要求。

- 返回的雜湊必須包括名為 **"原因"的屬性**。
- "原因"屬性必須是陣列。
- 陣列中的每個項都應是具有名為 **"代碼**"和"**短語**"的鍵的雜湊表。

服務使用"原因"屬性來標準化當電腦不符合要求時資訊的顯示方式。 您可以將"原因"中的每個專案視為資源不符合的"原因"。 屬性是一個陣列，因為資源可能由於多個原因而不符合要求。

服務應使用**屬性"代碼**"和 **"短語**"。 創作自訂資源時，將要顯示的文本（通常超時）設置為資源不符合**短語**的值的原因。 **代碼**具有特定的格式要求，因此報告可以清楚地顯示有關用於執行審核的資源的資訊。 此解決方案使來賓配置可擴展。 只要輸出可以作為**Phrases**屬性的字串值返回，就可以運行任何命令。

- **代碼**（字串）：資源的名稱重複，然後作為原因沒有空格作為識別碼的簡短名稱。 這三個值應為冒號分隔，沒有空格。
  - 例如`registry:registry:keynotpresent`
- **短語**（字串）：人可讀文本，以解釋設置不符合的原因。
  - 例如`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```
### <a name="configuration-requirements"></a>組態需求

自訂配置的名稱必須在所有位置保持一致。 內容包的 .ZIP 檔案的名稱、MOF 檔案中的配置名稱以及資源管理器範本中的來賓分配名稱必須相同。

### <a name="scaffolding-a-guest-configuration-project"></a>設置來賓設定項目的腳手架

希望加快入門和從示例代碼中工作的開發人員可以安裝名為 **"來賓設定項目**"的社區專案。 該專案為[Plaster](https://github.com/powershell/plaster) PowerShell 模組安裝範本。 此工具可用於基架專案，包括工作配置和示例資源，以及一組用於驗證專案的[Pester](https://github.com/pester/pester)測試。 該範本還包括 Visual Studio 代碼的任務運行程式，用於自動構建和驗證來賓配置包。 有關詳細資訊，請參閱 GitHub 專案[來賓設定項目](https://github.com/microsoft/guestconfigurationproject)。

有關使用配置的詳細資訊，請參閱[寫入、編譯和應用配置](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)。

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>來賓設定項目的預期內容

來賓配置使用已完成的包創建 Azure 策略定義。 該套裝軟體包括：

- 編譯的 DSC 配置為 MOF
- Modules 資料夾
  - 來賓配置模組
  - DscNativeResources 模組
  - （視窗）MOF 所需的 DSC 資源模組

PowerShell Cmdlet 可説明創建包。
不需要根級資料夾或版本資料夾。
包格式必須是 .ZIP 檔案。

### <a name="storing-guest-configuration-artifacts"></a>存儲來賓設定項目

.zip 包必須存儲在託管虛擬機器可訪問的位置。
示例包括 GitHub 存儲庫、Azure 存儲庫或 Azure 存儲。 如果您不希望將包公開，則可以在 URL 中包含[SAS 權杖](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。
您還可以為私人網路絡中的電腦實現[服務終結點](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)，儘管此配置僅適用于訪問包，不適用於與服務通信。

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>逐步創建 Windows 的自訂來賓配置稽核原則

創建 DSC 配置。 下面的 PowerShell 腳本示例創建名為**AuditBitLocker**的配置，導入**PsDscResources**資源模組`Service`，並使用資源對正在運行的服務進行審核。 配置腳本可以從 Windows 或 macOS 電腦執行。

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker -out ./Config
```

該`Node AuditBitlocker`命令在技術上不是必需的，但它生成名為`AuditBitlocker.mof`而不是預設值的檔。 `localhost.mof` 使 .mof 檔案名遵循配置，因此在大規模運行時很容易組織許多檔。

編譯 MOF 後，必須將支援檔打包在一起。 來賓配置使用已完成的包創建 Azure 策略定義。

Cmdlet`New-GuestConfigurationPackage`創建包。 創建 Windows`New-GuestConfigurationPackage`內容時的 Cmdlet 參數：

- **名稱**：來賓配置包名稱。
- **配置**：編譯 DSC 配置文檔完整路徑。
- **路徑**：輸出檔案夾路徑。 這是選擇性參數。 如果未指定，則在目前的目錄中創建包。

運行以下命令，使用上一步驟中給出的配置創建包：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

創建配置包後，但在將其發佈到 Azure 之前，可以從工作站或 CI/CD 環境中測試該包。 Guest 配置 Cmdlet`Test-GuestConfigurationPackage`在開發環境中包括與 Azure 電腦中使用的代理相同的代理。 使用此解決方案，您可以在本地執行集成測試，然後再發佈到計費的雲環境。

由於代理實際上是在評估本地環境，因此在大多數情況下，您需要在計畫審核的同一作業系統平臺上運行 Test-Cmdlet。

Cmdlet`Test-GuestConfigurationPackage`的參數：

- **名稱**：來賓配置策略名稱。
- **參數**：以可雜湊格式提供的策略參數。
- **路徑**：來賓配置包的完整路徑。

運行以下命令以測試上一步驟創建的包：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Cmdlet 還支援從 PowerShell 管道輸入。 將 Cmdlet 的`New-GuestConfigurationPackage`輸出輸送到`Test-GuestConfigurationPackage`Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

下一步是將檔發佈到 Blob 存儲。 下面的腳本包含可用於自動執行此任務的函數。 `publish`函數中使用的命令需要模組`Az.Storage`。

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

創建並上載來賓配置自訂策略包後，創建來賓配置策略定義。 Cmdlet`New-GuestConfigurationPolicy`採用自訂策略包並創建策略定義。

Cmdlet`New-GuestConfigurationPolicy`的參數：

- **內容Uri**：來賓配置內容包的公共 HTTP（s） uri。
- **顯示名稱**：策略顯示名稱。
- **說明**：策略說明。
- **參數**：以可雜湊格式提供的策略參數。
- **版本**：策略版本。
- **路徑**：創建策略定義的目標路徑。
- **平臺**：用於來賓配置策略和內容包的目標平臺（Windows/Linux）。

以下示例從自訂策略包創建指定路徑中的策略定義：

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

以下檔由 創建`New-GuestConfigurationPolicy`。

- **審計IfNotExists.json**
- **部署"不存在.json"**
- **倡議.json**

Cmdlet 輸出返回一個包含策略檔的計畫顯示名稱和路徑的物件。

最後，使用`Publish-GuestConfigurationPolicy`Cmdlet 發佈策略定義。 Cmdlet 僅具有**指向**由 創建的`New-GuestConfigurationPolicy`JSON 檔的位置的路徑參數。

要運行"發佈"命令，需要訪問才能在 Azure 中創建策略。 特定的授權要求記錄在 Azure[策略概述](../overview.md)頁中。 最好的內置角色是**資源策略參與者**。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy` Cmdlet 接受來自 PowerShell 管道的路徑。 此功能意味著您可以創建策略檔，並在一組管道命令中發佈它們。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

在 Azure 中創建策略後，最後一步是分配計畫。 瞭解如何使用[門戶](../assign-policy-portal.md)[、Azure CLI](../assign-policy-azurecli.md)和[Azure PowerShell](../assign-policy-powershell.md)分配計畫。

> [!IMPORTANT]
> **必須始終使用**組合_審核不存在_和_部署"不存在"_ 策略的計畫來分配來賓配置策略。 如果只分配_了 AuditIfNotExists_策略，則不會部署先決條件，並且該策略始終顯示"0"伺服器符合要求。

使用_DeployIfNotExists_效果分配策略定義需要額外的存取層級。 要授予最低許可權，您可以創建擴展**資源策略參與者**的自訂角色定義。 下面的示例創建一個名為 **"資源策略參與者 DINE"** 的角色，該角色具有_Microsoft.授權/角色指派/寫入_的其他許可權。

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>在自訂來賓配置策略中使用參數

來賓配置支援在運行時覆蓋配置的屬性。 此功能意味著包中的 MOF 檔案中的值不必被視為靜態值。 重寫值通過 Azure 策略提供，不會影響配置的創作或編譯方式。

Cmdlet，`New-GuestConfigurationPolicy`並`Test-GuestConfigurationPolicyPackage`包含一個名為**參數的參數**。 此參數採用可雜湊定義，包括有關每個參數的所有詳細資訊，並創建用於 Azure 策略定義的每個檔所需的部分。

下面的示例創建一個策略定義來審核服務，使用者在策略分配時從清單中選擇該服務。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>策略生命週期

如果要發佈策略的更新，有兩個欄位需要注意。

- **版本**：運行`New-GuestConfigurationPolicy`Cmdlet 時，必須指定大於當前發佈的版本號的版本號。 屬性更新來賓配置分配的版本，以便代理識別更新的包。
- **內容雜湊**：此屬性由`New-GuestConfigurationPolicy`Cmdlet 自動更新。 它是 創建的`New-GuestConfigurationPackage`包的雜湊值。 屬性必須對發佈的`.zip`檔正確。 如果僅更新**內容 Uri**屬性，則擴展不會接受內容包。

發佈更新的包的最簡單方法是重複本文中描述的過程並提供更新的版本號。 此過程可確保已正確更新所有屬性。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>將 Windows 群組原則內容轉換為 Azure 策略來賓配置

來賓配置在審核 Windows 電腦時，是 PowerShell 所需狀態配置語法的實現。 DSC 社區發佈了將匯出的群組原則範本轉換為 DSC 格式的工具。 通過將此工具與上述來賓配置 Cmdlet 一起使用，可以轉換 Windows 群組原則內容和包/發佈它，以便 Azure 策略進行審核。 有關使用該工具的詳細資訊，請參閱"[快速入門：將群組原則轉換為 DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart)" 一文。
轉換內容後，上述創建包並將其發佈為 Azure 策略的步驟與任何 DSC 內容的步驟相同。

## <a name="optional-signing-guest-configuration-packages"></a>可選：對來賓配置包進行簽名

來賓配置自訂策略使用 SHA256 雜湊來驗證策略包未更改。
或者，客戶還可以使用證書對包進行簽名，並強制來賓配置擴展僅允許簽名內容。

要啟用此方案，您需要完成兩個步驟。 運行 Cmdlet 對內容包進行簽名，並將標記追加到需要簽名的電腦。

要使用簽名驗證功能，請運行`Protect-GuestConfigurationPackage`Cmdlet 在發佈包之前對包進行簽名。 此 Cmdlet 需要"代碼簽名"證書。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Cmdlet`Protect-GuestConfigurationPackage`的參數：

- **路徑**：來賓配置包的完整路徑。
- **證書**：代碼簽章憑證以對包進行簽名。 僅當為 Windows 對內容進行簽名時，才支援此參數。

Guest 配置代理希望證書公開金鑰存在於 Windows 電腦上的"受信任的根憑證授權單位"和 Linux`/usr/local/share/ca-certificates/extra`電腦上的路徑中。 要驗證簽名內容的節點，請在應用自訂策略之前在電腦上安裝證書公開金鑰。 此過程可以使用 VM 中的任何技術或使用 Azure 策略來完成。 [此處提供了](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)一個示例範本。
金鑰保存庫訪問策略必須允許計算資來源提供者在部署期間訪問證書。 有關詳細步驟，請參閱[在 Azure 資源管理器中為虛擬機器設置金鑰保存庫](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

下面是從簽章憑證匯出公開金鑰的示例，以便導入到電腦。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

發佈內容後，將名稱`GuestConfigPolicyCertificateValidation`和值`enabled`標記追加到需要代碼簽名的所有虛擬機器。 有關如何使用 Azure 策略大規模交付標記，請參閱[標記示例](../samples/built-in-policies.md#tags)。 此標記就位後，使用`New-GuestConfigurationPolicy`Cmdlet 生成的策略定義可通過來賓配置擴展啟用要求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>排除來賓配置策略分配（預覽）

預覽版中提供了一個工具，可説明解決 Azure 策略來賓配置分配的故障。 該工具處於預覽狀態，並已作為模組名稱來賓[配置疑難排解](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)發佈到 PowerShell 庫。

有關此工具中的 Cmdlet 的詳細資訊，請使用 PowerShell 中的"獲取説明"命令來顯示內置指南。 由於該工具正在頻繁更新，這是獲取最新資訊的最佳方式。

## <a name="next-steps"></a>後續步驟

- 瞭解有關使用[來賓配置](../concepts/guest-configuration.md)審核 VM 的事。
- 瞭解如何[以程式設計方式創建策略](programmatically-create.md)。
- 瞭解如何[獲取合規性資料](get-compliance-data.md)。
