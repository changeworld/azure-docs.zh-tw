---
title: 如何建立 Windows 的來賓設定原則
description: 瞭解如何建立適用于 Windows 的 Azure 原則來賓設定原則。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f09bb543f73e37bd211a55e2238808f57585bb18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024892"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>如何建立 Windows 的來賓設定原則

建立自訂原則之前，最好先閱讀[Azure 原則來賓](../concepts/guest-configuration.md)設定頁面上的概念總覽資訊。
 
若要瞭解如何建立適用于 Linux 的來賓設定原則，請參閱[如何建立適用于 linux 的來賓設定原則](./guest-configuration-create-linux.md)頁面

在審核 Windows 時，來賓設定會使用[Desired State Configuration](/powershell/scripting/dsc/overview/overview) （DSC）資源模組和設定檔案。 DSC 設定會定義機器應處於的條件。
如果設定的評估失敗，則會觸發原則效果**auditIfNotExists** ，並將機器視為**不相容**。

[Azure 原則來賓](../concepts/guest-configuration.md)設定只能用來審查電腦內的設定。 尚未提供機器內設定的補救功能。

使用下列動作來建立您自己的設定，以驗證 Azure 或非 Azure 機器的狀態。

> [!IMPORTANT]
> 具有「來賓設定」的自訂原則是一項預覽功能。
>
> 需要來賓設定延伸模組，才能在 Azure 虛擬機器中執行審核。
> 若要在所有 Windows 電腦上大規模部署擴充功能，請指派下列原則定義：
>   - [部署必要條件，以在 Windows VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

建立來賓設定成品、自動測試成品、建立原則定義，以及發佈原則，都是使用 PowerShell 中的來賓設定模組來完全自動化。 此模組可以安裝在執行 Windows、macOS 或 Linux 的電腦上，且 PowerShell 6.2 或更新版本在本機執行，或使用[Azure Cloud Shell](https://shell.azure.com)或[Azure PowerShell 核心 Docker 映射](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 上尚不支援設定的編譯。

### <a name="base-requirements"></a>基底需求

可安裝模組的作業系統：

- Linux
- macOS
- Windows

來賓設定資源模組需要下列軟體：

- PowerShell 6.2 或更新版本。 如果尚未安裝，請依照[這些指示](/powershell/scripting/install/installing-powershell)操作。
- Azure PowerShell 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
  - 只需要 AZ 模組 ' Az. Accounts ' 和 ' Az. Resources '。

### <a name="install-the-module"></a>安裝模組

若要在 PowerShell 中安裝**GuestConfiguration**模組：

1. 從 PowerShell 提示字元中執行下列命令：

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 驗證模組是否已匯入：

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows 的來賓設定構件和原則

「來賓設定」會使用 PowerShell Desired State Configuration 作為語言抽象概念，來撰寫要在 Windows 中進行的審核。 代理程式會載入獨立的 PowerShell 6.2 實例，因此不會與 Windows PowerShell 5.1 中的 PowerShell DSC 使用方式發生衝突，也不需要預先安裝 PowerShell 6.2 或更新版本。

如需 DSC 概念和術語的總覽，請參閱[POWERSHELL Dsc 總覽](/powershell/scripting/dsc/overview/overview)。

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>來賓設定模組與 Windows PowerShell DSC 模組有何不同

當來賓設定審核電腦時：

1. 代理程式會先`Test-TargetResource`執行，以判斷設定是否處於正確的狀態。
1. 函數所傳回的布林值會決定來賓指派的 Azure Resource Manager 狀態是否應該符合規範。
1. 提供者會`Get-TargetResource`執行以傳回每個設定的目前狀態，因此詳細資訊可同時適用于為何電腦不符合規範，以及如何確認目前的狀態是否符合規範。

### <a name="get-targetresource-requirements"></a>TargetResource 需求

此函`Get-TargetResource`式具有 Windows Desired State Configuration 尚未需要之來賓設定的特殊需求。

- 傳回的雜湊表必須包含名為「**原因**」的屬性。
- 原因屬性必須是陣列。
- 陣列中的每個專案都應該是具有名為**Code**和**片語**之索引鍵的雜湊表。

當電腦不符合規範時，服務會使用 [原因] 屬性來標準化資訊的呈現方式。 您可以將每個專案視為「原因」，原因是該資源不符合規範。 屬性是一個陣列，因為資源可能會因為一個以上的原因而不符合規範。

服務應該會有屬性**代碼**和**片語**。 撰寫自訂資源時，請將您想要顯示的文字（通常是 stdout）設定為資源不符合 [**片語**] 值的原因。 程式**代碼**有特定的格式需求，因此報告可以清楚地顯示用來執行 audit 之資源的相關資訊。 此解決方案可讓來賓設定可擴充。 只要可以將輸出當做**片語**屬性的字串值傳回，就可以執行任何命令。

- **Code** （字串）：資源的名稱，重複，再加上不含空格的簡短名稱作為原因的識別碼。 這三個值應該以冒號分隔，且不含空格。
  - 例如，`registry:registry:keynotpresent`
- **片語**（字串）：人們看得懂的文字，以說明設定不符合規範的原因。
  - 例如，`The registry key $key is not present on the machine.`

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

[原因] 屬性也必須加入至資源的架構 MOF，做為內嵌類別。

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

自訂設定的名稱在任何位置都必須一致。 內容套件的 .zip 檔案名稱、MOF 檔案中的設定名稱，以及 Resource Manager 範本中的來賓指派名稱，必須相同。

### <a name="scaffolding-a-guest-configuration-project"></a>設定來賓設定專案的架構

想要加速開始使用和從範例程式碼執行的程式開發人員，可以安裝名為「**來賓設定專案**」的社區專案。 此專案會安裝[Plaster](https://github.com/powershell/plaster) PowerShell 模組的範本。 這項工具可用來 scaffold 專案，包括可運作的設定和範例資源，以及一組用來驗證專案的[Pester](https://github.com/pester/pester)測試。 此範本也包含 Visual Studio Code 的工作執行程式，可自動建立及驗證來賓設定套件。 如需詳細資訊，請參閱 GitHub 專案[來賓設定專案](https://github.com/microsoft/guestconfigurationproject)。

如需一般使用設定的詳細資訊，請參閱[撰寫、編譯和](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)套用設定。

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>來賓設定成品的預期內容

「來賓設定」會使用完成的封裝來建立 Azure 原則定義。 套件包含：

- 以 MOF 形式編譯的 DSC 設定
- Modules 資料夾
  - GuestConfiguration 模組
  - DscNativeResources 模組
  - 時段MOF 所需的 DSC 資源模組

PowerShell Cmdlet 會協助建立封裝。
不需要根層級資料夾或版本資料夾。
封裝格式必須是 .zip 檔案。

### <a name="storing-guest-configuration-artifacts"></a>儲存來賓設定成品

.Zip 封裝必須儲存在受管理的虛擬機器可存取的位置。
範例包括 GitHub 存放庫、Azure 儲存機制或 Azure 儲存體。 如果您不想讓套件公開，您可以在 URL 中包含[SAS 權杖](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。
雖然這項設定只適用于存取封裝，而不是與服務通訊，但您也可以在私人網路中執行機器的[服務端點](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)。

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>逐步解說：建立適用于 Windows 的自訂來賓設定稽核原則

建立 DSC 設定以進行審核設定。 下列 PowerShell 腳本範例會建立名為**AuditBitLocker**的設定、匯入**PsDscResources**資源模組，並使用`Service`資源來審查執行中的服務。 設定腳本可以從 Windows 或 macOS 電腦執行。

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
AuditBitLocker ./Config
```

以名稱`config.ps1`將此檔案儲存在專案資料夾中。 在終端機中執行， `./config.ps1`以在 PowerShell 中執行。 將會建立新的 mof 檔案。

此`Node AuditBitlocker`命令在技術上並不是必要的， `AuditBitlocker.mof`但它會產生名`localhost.mof`為的檔案，而不是預設的。 將此 mof 檔案名遵循設定，可讓您在大規模操作時輕鬆地整理許多檔案。

一旦完成 MOF 編譯，就必須將支援的檔案封裝在一起。 「來賓設定」會使用完成的封裝來建立 Azure 原則定義。

`New-GuestConfigurationPackage` Cmdlet 會建立封裝。 設定所需的模組必須在中`$Env:PSModulePath`提供。 建立 Windows 內容`New-GuestConfigurationPackage`時的 Cmdlet 參數：

- **名稱**：來賓設定封裝名稱。
- 設定 **：已**編譯的 DSC 設定檔完整路徑。
- **路徑**：輸出檔案夾路徑。 這是選擇性參數。 如果未指定，則會在目前的目錄中建立封裝。

執行下列命令，使用上一個步驟中指定的設定來建立封裝：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

建立設定套件之後，但在將它發佈至 Azure 之前，您可以從您的工作站或 CI/CD 環境測試套件。 GuestConfiguration Cmdlet `Test-GuestConfigurationPackage`在您的開發環境中包含的代理程式與在 Azure 機器內使用的相同。 使用此解決方案，您可以先在本機進行整合測試，再發行到計費的雲端環境。

由於代理程式實際上是評估本機環境，因此在大部分的情況下，您需要在您打算進行審核的相同作業系統平臺上執行測試 Cmdlet。 測試只會使用內容套件中包含的模組。

`Test-GuestConfigurationPackage` Cmdlet 的參數：

- **名稱**：來賓設定原則名稱。
- **參數**：以 hashtable 格式提供的原則參數。
- **Path**：來賓設定封裝的完整路徑。

執行下列命令來測試上一個步驟所建立的套件：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

此 Cmdlet 也支援來自 PowerShell 管線的輸入。 將`New-GuestConfigurationPackage` Cmdlet 的輸出傳送至`Test-GuestConfigurationPackage` Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

下一個步驟是將檔案發行至 blob 儲存體。 下列腳本包含可讓您用來自動化這項工作的函式。 函式中`publish`使用的命令需要`Az.Storage`模組。

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

建立並上傳來賓設定自訂原則套件之後，請建立來賓設定原則定義。 此`New-GuestConfigurationPolicy` Cmdlet 會採用自訂原則套件，並建立原則定義。

`New-GuestConfigurationPolicy` Cmdlet 的參數：

- **ContentUri**：來賓設定內容套件的公用 HTTP （s） uri。
- **DisplayName**：原則顯示名稱。
- **描述**：原則描述。
- **參數**：以 hashtable 格式提供的原則參數。
- **版本**：原則版本。
- **路徑**：原則定義建立所在的目的地路徑。
- **平臺**：來賓設定原則和內容套件的目標平臺（Windows/Linux）。

下列範例會從自訂原則封裝，在指定的路徑中建立原則定義：

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

下列檔案是由`New-GuestConfigurationPolicy`所建立：

- **auditIfNotExists json**
- **deployIfNotExists json**
- **方案. json**

Cmdlet 輸出會傳回物件，其中包含原則檔的計畫顯示名稱和路徑。

最後，使用`Publish-GuestConfigurationPolicy` Cmdlet 發佈原則定義。 Cmdlet 只有**Path**參數，它會指向所建立之 JSON 檔案的位置`New-GuestConfigurationPolicy`。

若要執行 [發佈] 命令，您需要在 Azure 中建立原則的存取權。 特定的授權需求記載于[Azure 原則總覽](../overview.md)] 頁面中。 最佳的內建角色是**資源原則參與者**。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

此`Publish-GuestConfigurationPolicy` Cmdlet 會接受來自 PowerShell 管線的路徑。 這項功能表示您可以建立原則檔案，並在一組輸送的命令中發佈它們。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

在 Azure 中建立原則之後，最後一個步驟是指派方案。 瞭解如何使用[入口網站](../assign-policy-portal.md)、 [Azure CLI](../assign-policy-azurecli.md)和[Azure PowerShell](../assign-policy-powershell.md)來指派方案。

> [!IMPORTANT]
> 來賓設定原則必須**一律**使用結合_AuditIfNotExists_和_DeployIfNotExists_原則的計畫來指派。 如果只指派_AuditIfNotExists_原則，則不會部署必要條件，原則一律會顯示「0」伺服器符合規範。

指派具有_DeployIfNotExists_效果的原則定義需要額外的存取層級。 若要授與最低許可權，您可以建立擴充**資源原則參與者**的自訂角色定義。 下列範例會建立名為「**資源原則參與者 DINE** 」的角色，其中包含其他許可權_Microsoft. Authorization/roleAssignments/write_。

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>在自訂來賓設定原則中使用參數

「來賓設定」支援在執行時間覆寫設定的屬性。 這項功能表示封裝中 MOF 檔案中的值不一定要被視為靜態。 覆寫值是透過 Azure 原則提供，而且不會影響撰寫或編譯設定的方式。

Cmdlet `New-GuestConfigurationPolicy`和`Test-GuestConfigurationPolicyPackage`包含名為**Parameters**的參數。 這個參數會採用 hashtable 定義，包括每個參數的所有詳細資料，並建立用於 Azure 原則定義之每個檔案的必要區段。

下列範例會建立原則定義來審查服務，而使用者會在原則指派時從清單中進行選取。

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

## <a name="policy-lifecycle"></a>原則生命週期

如果您想要將更新發行至原則，則需要注意兩個欄位。

- **版本**：當您執行`New-GuestConfigurationPolicy` Cmdlet 時，您必須指定大於目前發行的版本號碼。 屬性會更新來賓設定指派的版本，讓代理程式能夠辨識更新的封裝。
- **contentHash**： `New-GuestConfigurationPolicy` Cmdlet 會自動更新此屬性。 這是所建立之封裝的雜湊值`New-GuestConfigurationPackage`。 屬性對於您發行的`.zip`檔案而言必須是正確的。 如果只更新**contentUri**屬性，延伸模組就不會接受內容套件。

釋放更新套件的最簡單方式是重複本文中所述的程式，並提供更新的版本號碼。 該進程可確保所有屬性都已正確更新。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>將 Windows 群組原則內容轉換成 Azure 原則來賓設定

「來賓設定」在「Windows 機器」執行時，是 PowerShell Desired State Configuration 語法的實施。 DSC 社區已發佈工具，可將匯出的群組原則範本轉換成 DSC 格式。 將此工具與上述的來賓設定 Cmdlet 搭配使用，您就可以轉換 Windows 群組原則內容，並將其封裝/發佈以供 Azure 原則進行審核。 如需使用此工具的詳細資訊，請參閱[快速入門：將群組原則轉換成 DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart)一文。
內容轉換後，上述步驟會建立套件，並將其發佈為 Azure 原則與任何 DSC 內容相同。

## <a name="optional-signing-guest-configuration-packages"></a>選擇性：簽署來賓設定套件

來賓設定自訂原則會使用 SHA256 雜湊來驗證原則封裝未變更。
（選擇性）客戶也可以使用憑證來簽署套件，並強制來賓設定延伸模組只允許已簽署的內容。

若要啟用此案例，您需要完成兩個步驟。 執行 Cmdlet 來簽署內容套件，並將標記附加至需要簽署程式碼的電腦上。

若要使用簽章驗證功能，請`Protect-GuestConfigurationPackage`執行 Cmdlet 來簽署封裝，然後再發佈。 此 Cmdlet 需要「程式碼簽署」憑證。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` Cmdlet 的參數：

- **Path**：來賓設定封裝的完整路徑。
- **憑證**：用來簽署封裝的程式碼簽署憑證。 只有在簽署 Windows 的內容時，才支援這個參數。

GuestConfiguration 代理程式預期憑證公開金鑰會出現在 Windows 電腦上的「受信任的根憑證授權」中，以及`/usr/local/share/ca-certificates/extra` Linux 電腦上的路徑中。 若要讓節點驗證已簽署的內容，請先在電腦上安裝憑證公開金鑰，再套用自訂原則。 此程式可以使用 VM 內的任何技術來完成，或使用 Azure 原則。 [這裡提供](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)範例範本。
Key Vault 存取原則必須允許計算資源提供者在部署期間存取憑證。 如需詳細步驟，請參閱[在 Azure Resource Manager 中設定虛擬機器的 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

以下是從簽署憑證匯出公開金鑰以匯入到電腦的範例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

發佈內容之後，請將具有名稱`GuestConfigPolicyCertificateValidation`和值`enabled`的標籤，附加至需要程式碼簽署的所有虛擬機器。 如需如何使用 Azure 原則大規模傳遞標記的詳細說明，請參閱[標記範例](../samples/built-in-policies.md#tags)。 一旦此標記已就緒，使用`New-GuestConfigurationPolicy` Cmdlet 產生的原則定義就會透過「來賓設定」延伸模組來啟用需求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>針對來賓設定原則指派進行疑難排解（預覽）

預覽中提供了一項工具，可協助 Azure 原則來賓設定指派進行疑難排解。 此工具目前為預覽狀態，並已發佈至 PowerShell 資源庫做為模組名稱[來賓設定疑難排解](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)員。

如需此工具中 Cmdlet 的詳細資訊，請在 PowerShell 中使用 Get-help 命令，以顯示內建的指導方針。 當此工具取得經常更新時，這是取得最新資訊的最佳方式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[來賓](../concepts/guest-configuration.md)設定來進行 vm 的審核。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[取得合規性資料](get-compliance-data.md)。
