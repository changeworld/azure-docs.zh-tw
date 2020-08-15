---
title: 如何建立 Windows 的客體設定原則
description: 了解如何建立 Windows 的 Azure 原則客體設定原則。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 31c40640babea961ef3bb255112306f59772bae2
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236534"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>如何建立 Windows 的客體設定原則

建立自訂原則定義之前，建議先閱讀 [Azure 原則客體設定](../concepts/guest-configuration.md)頁面 上的概念概觀資訊。
 
若要了解如何建立 Linux 的客體設定原則，請參閱[如何建立 Linux 的客體設定原則](./guest-configuration-create-linux.md)頁面

在稽核 Windows 時，客體設定會使用[預期狀態設定](/powershell/scripting/dsc/overview/overview) (DSC) 資源模組來建立設定檔。 DSC 設定會定義電腦所應處的條件。
如果設定的評估失敗，則會觸發 **auditIfNotExists** 的原則效果，並將該電腦視為**不符合規範**。

[Azure 原則客體設定](../concepts/guest-configuration.md)目前只會稽核電腦內的設定， 尚不提供補救電腦內的設定。

使用下列動作來建立自己的設定，以驗證 Azure 或非 Azure 電腦的狀態。

> [!IMPORTANT]
> 具有客體設定的自訂原則是一項預覽功能。
>
> 您需要客體設定延伸模組，才能在 Azure 虛擬機器中執行稽核。
> 若要在所有 Windows 電腦間大規模部署延伸模組，請指派下列原則定義：
>   - [部署必要條件，以在 Windows VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

客體設定模組會自動建立自訂內容，包括：

- 建立客體設定內容成品 (.zip)
- 自動測試成品
- 建立原則定義
- 發佈原則

此模組可安裝在執行 Windows、macOS 或 Linux 的電腦上，並搭配本機執行的 PowerShell 6.2 或更新版本、搭配 [Azure Cloud Shell](https://shell.azure.com)，或搭配 [Azure PowerShell Core Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 尚不支援設定的編譯。

### <a name="base-requirements"></a>基底需求

可安裝模組的作業系統：

- Linux
- macOS
- Windows

客體設定資源模組需要下列軟體：

- PowerShell 6.2 或更新版本。 如果尚未安裝，請依照[這些指示](/powershell/scripting/install/installing-powershell)操作。
- Azure PowerShell 1.5.0 或更新版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
  - 只需要 AZ 模組的 'Az.Accounts' 與 'Az.Resources'。

### <a name="install-the-module"></a>安裝模組

若要在 PowerShell 中安裝**GuestConfiguration**模組：

1. 從 PowerShell 提示字元中執行下列命令：

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 驗證已匯入模組：

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows 的客體設定成品和原則

客體設定會使用 PowerShell 預期狀態設定作為語言抽象概念，以撰寫要在 Windows 中進行的稽核。 代理程式會載入獨立的 PowerShell 6.2 執行個體，因此不會與 Windows PowerShell 5.1 中的 PowerShell DSC 使用方式發生衝突，也不需要預先安裝 PowerShell 6.2 或更新版本。

如需 DSC 概念和術語的概觀，請參閱 [PowerShell DSC 概觀](/powershell/scripting/dsc/overview/overview)。

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>客體設定模組與 Windows PowerShell DSC 模組有何不同

當來賓設定審核電腦時，事件的順序會與 Windows PowerShell DSC 中的不同。

1. 代理程式會先執行 `Test-TargetResource` 來判斷設定是否處於正確狀態。
1. 函式所傳回布林值會判斷客體指派的 Azure Resource Manager 狀態是否符合規範。
1. 提供者會執行 `Get-TargetResource` 以傳回每個設定的目前狀態，藉此得知為何電腦不符合規範以及確認目前的狀態是否符合規範，這兩項詳細資料。

在 Azure 原則中，將值傳遞給來賓設定指派的參數必須是 _字串_ 類型。
即使 DSC 資源支援陣列，也無法透過參數傳遞陣列。

### <a name="get-targetresource-requirements"></a>Get-TargetResource 需求

針對 Windows 預期狀態設定尚不需要的客體設定，函式 `Get-TargetResource` 會具有特殊需求。

- 所傳回雜湊表必須包含名為 **Reasons** 的屬性。
- Reasons 屬性必須為陣列。
- 陣列中的每個項目都應該是一個雜湊表，並具有名為 **Code** 與 **Phrase** 的索引鍵。

當電腦不符合規範時，服務會使用 Reasons 屬性來標準化資訊的呈現方式。 您可將 Reasons 中的每個專案視為「原因」，即該資源不符合規範的原因。 屬性是一個陣列，因為資源可能會因為一個以上的原因而不符合規範。

服務會預期 **Code** 與 **Phrase** 的屬性。 撰寫自訂資源時，請將想要顯示的文字 (通常為 stdout)，設定為資源不符合 **Phrase** 值規範的原因。 **Code** 有特定的格式設定需求，因此報告可清楚顯示用來執行稽核的資源資訊。 這項解決方案讓客體設定可具有擴充性。 只要可將輸出作為 **Phrase** 屬性的字串值傳回，即可執行任何命令。

- **Code** (字串):資源的名稱，重複一次名稱，再以不含空格的簡短名稱作為原因識別碼。 這三個值應以冒號來分隔，且不含空格。
  - 例如 `registry:registry:keynotpresent`
- **Phrase** (字串)：人類可讀取的文字，以說明為何設定不符合規範。
  - 例如 `The registry key $key is not present on the machine.`

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

您也必須將 Reasons 屬性作為內嵌的類別來新增至資源結構描述 MOF 中。

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>組態需求

自訂設定的名稱在任何位置都必須一致。 內容套件的 .zip 檔案名稱、MOF 檔案中的設定名稱，以及 Azure Resource Manager 範本 (ARM 範本) 中的來賓指派名稱必須相同。

### <a name="scaffolding-a-guest-configuration-project"></a>為客體設定專案建立結構

開發人員若希望加快從範例程式碼開始與工作的流程，則可安裝名為**客體設定專案**的社群專案。 此專案會安裝 [Plaster](https://github.com/powershell/plaster) PowerShell 模組的範本。 這項工具可用來為專案建立結構，包括運作中的設定和範例資源，以及一組 [Pester](https://github.com/pester/pester) 測試來驗證專案。 此範本也包含 Visual Studio Code 的工作執行器，可自動建置及驗證客體設定套件。 如需詳細資訊，請參閱 GitHub 專案[客體設定專案](https://github.com/microsoft/guestconfigurationproject)。

如需一般使用設定的詳細資訊，請參閱[撰寫、編譯及套用設定](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)。

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>客體設定成品的預期內容

客體設定會使用完成的套件來建立 Azure 原則定義。 此套件包含：

- 以 MOF 形式編譯的 DSC 設定
- Modules 資料夾
  - GuestConfiguration 模組
  - DscNativeResources 模組
  - (Windows) MOF 所需的 DSC 資源模組

PowerShell Cmdlet 可協助建立此套件，
而無需根層級資料夾或版本資料夾。
套件格式必須為 .zip 檔案。

### <a name="storing-guest-configuration-artifacts"></a>儲存客體設定成品

.zip 套件必須儲存於受控虛擬機器可存取的位置。
例如 GitHub 存放庫、Azure 存放庫或 Azure 儲存體。 如果不想讓套件公開，您可在 URL 中包含 [SAS 權杖](../../../storage/common/storage-sas-overview.md)。
您也可以為私人網路中的電腦實作[服務端點](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)，但此設定僅適用於存取套件，且無法與服務通訊。

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>逐步建立 Windows 的自訂客體設定稽核原則

建立 DSC 設定來稽核設定。 下列 PowerShell 指令碼範例會建立名為 **AuditBitLocker** 的設定、匯入 **PsDscResources** 資源模組，並使用 `Service` 資源來稽核執行中的服務。 您可從 Windows 或 macOS 電腦執行設定指令碼。

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

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
AuditBitLocker ./Config
```

將名為 `config.ps1` 的檔案儲存在專案資料夾中。 在終端機中執行 `./config.ps1`，以在 PowerShell 中執行該檔案。 新的 MOF 檔案隨即建立。

就技術上而言，`Node AuditBitlocker` 命令不是必要的，但該命令會產生名為 `AuditBitlocker.mof` 的檔案，而非預設的 `localhost.mof`。 使 MOF 檔案名稱遵循設定，以供在大規模作業時輕鬆地整理許多檔案。

完成 MOF 編譯後，必須將支援的檔案封裝在一起。 客體設定會使用完成的套件來建立 Azure 原則定義。

`New-GuestConfigurationPackage` Cmdlet 會建立套件。 設定所需的模組必須在 `$Env:PSModulePath` 中提供。 建立 Windows 內容時所使用的 `New-GuestConfigurationPackage` Cmdlet 參數：

- **Name**：客體設定套件名稱。
- **設定**：已編譯的 DSC 設定文件完整路徑。
- **路徑**：輸出資料夾路徑。 這是選擇性參數。 如果未指定，則會在目前的目錄中建立套件。

執行下列命令，以使用上一個步驟中指定的設定來建立套件：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

建立設定套件之後，在將其發佈至 Azure 前，您可在工作站或 CI/CD 環境中測試套件。 GuestConfiguration Cmdlet `Test-GuestConfigurationPackage` 在開發環境中所包含的代理程式，會與 Azure 電腦所使用的代理程式相同。 使用此解決方案，即可先在本機進行整合式測試，再發行到需要計費的雲端環境。

由於代理程式實際上是評估本機環境，因此在大部分的情況下會需要在打算進行稽核的相同 OS 平台上執行 Test- Cmdlet。 該測試只會使用內容套件中包含的模組。

`Test-GuestConfigurationPackage` Cmdlet 的參數：

- **Name**：客體設定原則名稱。
- **參數**：以雜湊表格式提供的原則參數。
- **路徑**：客體設定套件的完整路徑。

執行下列命令來測試上一個步驟所建立的套件：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

此 Cmdlet 也支援來自 PowerShell 管線的輸入。 透過管線將 `New-GuestConfigurationPackage` Cmdlet 的輸出傳送至 `Test-GuestConfigurationPackage` Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

下一個步驟是將檔案發佈至 blob 儲存體。 下列指令碼包含可供用來自動執行這項工作的函式。 `publish` 函式中使用的命令需要 `Az.Storage` 模組。

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

在完成建立並上傳客體設定自訂原則套件後，請建立客體設定原則定義。 `New-GuestConfigurationPolicy` Cmdlet 會採納自訂原則套件，並建立原則定義。

`New-GuestConfigurationPolicy` Cmdlet 的參數：

- **ContentUri**：客體設定內容套件的公用 http(s) URI。
- **DisplayName**：原則顯示名稱。
- **描述**：原則描述。
- **參數**：以雜湊表格式提供的原則參數。
- **版本**：原則版本。
- **路徑**：建立原則定義的目的地路徑。
- **平台**：客體設定原則和內容套件的目標平台 (Windows/Linux)。
- **Tag** 會將一或多個標籤篩選新增至原則定義
- **Category** 會在原則定義中，設定類別中繼資料欄位

下列範例會從自訂原則套件在指定的路徑中建立原則定義：

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

下列檔案是由 `New-GuestConfigurationPolicy` 所建立：

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

Cmdlet 輸出會傳回物件，其中包含原則檔案的方案顯示名稱和路徑。

最後，使用 `Publish-GuestConfigurationPolicy` Cmdlet 來發佈原則定義。 此 Cmdlet 只有 **Path** 參數，該參數會指向由 `New-GuestConfigurationPolicy` 所建立的 JSON 檔案位置。

若要執行發佈命令，則需要在 Azure 中建立原則的存取權。 如需特定的授權需求，請參閱 [Azure 原則概觀](../overview.md)頁面。 最佳的內建角色為**資源原則參與者**。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy` Cmdlet 會接受來自 PowerShell 管線的路徑。 這項功能表示您可建立原則檔案，並透過管線以單一命令組來發佈這些檔案。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

在 Azure 中建立原則之後，即可指派方案作為最後一步。 請參閱使用[入口網站](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 以及 [Azure PowerShell](../assign-policy-powershell.md) 來指派方案的方法。

> [!IMPORTANT]
> 客體設定原則必須**一律**使用結合 _AuditIfNotExists_ 與 _DeployIfNotExists_ 原則的方案來指派。 如果僅指派 _AuditIfNotExists_ 原則，則不會部署先決條件，而原則一律會顯示「0」部伺服器符合規範。

若要搭配 _DeployIfNotExists_ 效果指派原則定義，則需要額外的存取層級。 若要授與最小權限，您可建立自訂角色定義來擴充**資源原則參與者**。 下列範例會建立名為**資源原則參與者 DINE** 的角色，並具有 _Microsoft.Authorization/roleAssignments/write_ 的額外權限。

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

### <a name="filtering-guest-configuration-policies-using-tags"></a>使用標籤來篩選客體設定原則

在客體設定模組中，由 Cmdlet 所建立的原則定義可選擇性地包含標籤篩選。 `New-GuestConfigurationPolicy` 的 **Tag** 參數支援包含個別標籤項目的雜湊表陣列。 標籤會新增至原則定義的 `If` 區段，且無法由原則指派加以修改。

以下提供篩選標籤的原則定義範例程式碼片段。

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>在自訂客體設定原則定義中使用參數

客體設定支援在執行階段覆寫設定的屬性。 這項功能表示套件中 MOF 檔案的值不一定要被視為靜態。 覆寫值是透過 Azure 原則提供，且不會影響撰寫或編譯設定的方式。

Cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含名為 **參數**的參數。 此參數會採納雜湊表定義，包括每個參數的所有詳細資料，並為每個檔案建立必要區段，以用於 Azure 原則定義。

下列範例會建立原則定義來稽核服務，使用者可在原則指派時從清單中進行選取。

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
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>使用第三方工具來擴充客體設定

您可擴充客體設定的成品套件，以包含第三方工具。
擴充客體設定需要開發兩種元件。

- 預期狀態設定資源，可處理有關管理第三方工具的所有活動
  - 安裝
  - 叫用
  - 轉換輸出
- 工具的正確格式內容，以便以原生方式使用

如果社群解決方案尚不存在，則需要自訂開發 DSC 資源。
在 PowerShell 資源庫中搜尋 [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) 標籤，即可探索社群解決方案。

> [!Note]
> 客體設定擴充性為「自備授權」的案例。 在使用前，請確定您已符合所有第三方工具的條款及條件。

在開發環境中安裝 DSC 資源之後，請使用 **FilesToInclude** 參數進行 `New-GuestConfigurationPackage`，以在內容成品中包含第三方平台的內容。

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>逐步建立使用第三方工具的內容成品

只有 `New-GuestConfigurationPackage` Cmdlet 需要變更 DSC 內容成品的逐步指南。 在本範例中，使用 `gcInSpec` 模組擴充客體設定，使其使用 InSpec 平台 (而非 Linux 所使用的內建模組) 來稽核 Windows 電腦。 [在 GitHub 中，會以開放原始碼專案的形式](https://github.com/microsoft/gcinspec)來維護此社群模組。

在開發環境中安裝所需的模組：

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

首先，建立 InSpec 所使用的 YaML 檔案。 該檔案會提供有關環境的基本資訊。 範例如下所示：

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

將名為的檔案儲存 `wmi_service.yml` 在專案目錄中名為的資料夾中 `wmi_service` 。

接下來，使用用於稽核電腦的 InSpec 語言抽象來建立 Ruby 檔案。

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

將此檔案儲存 `wmi_service.rb` 在目錄內名為的新資料夾中 `controls` `wmi_service` 。

最後，建立設定、匯入 **GuestConfiguration** 資源模組，並使用 `gcInSpec` 資源來設定 InSpec 設定檔的名稱。

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

您現在應該具有如下所示的專案結構：

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

支援的檔案必須封裝在一起。 客體設定會使用完成的套件來建立 Azure 原則定義。

`New-GuestConfigurationPackage` Cmdlet 會建立套件。 針對第三方內容，請使用 **FilesToInclude** 參數，將 InSpec 內容新增至套件。 您不需要為 Linux 套件指定 **ChefProfilePath**。

- **Name**：客體設定套件名稱。
- **設定**：已編譯的設定文件完整路徑。
- **路徑**：輸出資料夾路徑。 這是選擇性參數。 如果未指定，則會在目前的目錄中建立套件。
- **FilesoInclude**：InSpec 設定檔的完整路徑。

執行下列命令，以使用上一個步驟中指定的設定來建立套件：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>原則生命週期

如果您想要將更新發行至原則，則需要注意兩個欄位。

- **版本**：當執行 `New-GuestConfigurationPolicy` Cmdlet 時，您必須指定大於目前發佈的版本號碼。 屬性會更新客體設定指派的版本，讓代理程式能夠辨識更新的套件。
- **contentHash**：`New-GuestConfigurationPolicy` Cmdlet 會自動更新此屬性。 此為 `New-GuestConfigurationPackage` 所建立套件的雜湊值。 針對您發佈的 `.zip` 檔案而言，此屬性必須是正確的。 如果只更新 **contentUri** 屬性，延伸模組就不會接受內容套件。

發行更新套件的最簡單方式就是重複本文中所述程序，並提供更新的版本號碼。 此程序可確保所有屬性都已正確更新。

## <a name="optional-signing-guest-configuration-packages"></a>選擇性：簽署客體設定套件

使用 SHA256 雜湊來驗證原則套件的客體設定自訂原則並未變更。
客戶也可選擇性地使用憑證來簽署套件，並強制客體設定延伸模組只允許已簽署的內容。

若要啟用此案例，您需要完成兩個步驟。 執行 Cmdlet 來簽署內容套件，並將標籤附加至需要簽署程式碼的電腦上。

若要使用簽章驗證功能，請執行 `Protect-GuestConfigurationPackage` Cmdlet，以在套件發佈之前進行簽署。 此 Cmdlet 需要「程式碼簽署」憑證。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` Cmdlet 的參數：

- **路徑**：客體設定套件的完整路徑。
- **Certificate**：用來簽署套件的程式碼簽署憑證。 只有在簽署 Windows 的內容時，才支援此參數。

GuestConfiguration 代理程式預期憑證公開金鑰會出現在 Windows 電腦上、以及 Linux 電腦路徑 `/usr/local/share/ca-certificates/extra` 的「受信任根憑證授權」中。 若要讓節點驗證已簽署的內容，請先在電腦上安裝憑證公開金鑰，再套用自訂原則。 此流程可使用 VM 內的任何技術、或使用 Azure 原則來完成。 請參閱[這裡](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)的範例範本。
Key Vault 存取原則必須允許計算資源提供者在部署期間存取憑證。 如需詳細步驟，請參閱[在 Azure Resource Manager 中設定虛擬機器的 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

下列是從簽署憑證匯出公開金鑰，以匯入到電腦的範例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

發佈內容之後，請將標籤 (名稱為 `GuestConfigPolicyCertificateValidation` 且值為 `enabled`) 附加至需要程式碼簽署的所有虛擬機器。 如需如何使用 Azure 原則大規模傳遞標籤的詳細說明，請參閱[標籤範例](../samples/built-in-policies.md#tags)。 備妥此標籤後，使用 `New-GuestConfigurationPolicy` Cmdlet 產生的原則定義就會透過客體設定延伸模組來啟用需求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>針對客體設定原則指派進行疑難排解 (預覽)

您可使用預覽中提供的工具來協助針對 Azure 原則客體設定指派進行疑難排解。 此工具目前為預覽狀態，並已發佈至 PowerShell 資源庫，其模組名稱為 [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/) (客體設定疑難排解員)。

如需此工具中 Cmdlet 的詳細資訊，請在 PowerShell 中使用 Get-Help 命令，以顯示內建指南。 由於此工具頻繁更新，所以建議以此方式來取得最新資訊。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[客體設定](../concepts/guest-configuration.md)來稽核 VM。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[取得合規性資料](get-compliance-data.md)。
