---
title: 如何建立適用于 Linux 的來賓設定原則
description: 瞭解如何建立適用于 Linux 的 Azure 原則來賓設定原則。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024977"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>如何建立適用于 Linux 的來賓設定原則

建立自訂原則之前，請閱讀[Azure 原則來賓](../concepts/guest-configuration.md)設定的總覽資訊。
 
若要瞭解如何建立 Windows 的來賓設定原則，請參閱[如何建立 windows 的來賓設定原則](./guest-configuration-create.md)頁面

在審核 Linux 時，來賓設定會使用[Chef InSpec](https://www.inspec.io/)。 InSpec 設定檔會定義機器應處於的條件。 如果設定的評估失敗，則會觸發原則效果**auditIfNotExists** ，並將機器視為**不相容**。

[Azure 原則來賓](../concepts/guest-configuration.md)設定只能用來審查電腦內的設定。 尚未提供機器內設定的補救功能。

使用下列動作來建立您自己的設定，以驗證 Azure 或非 Azure 機器的狀態。

> [!IMPORTANT]
> 具有「來賓設定」的自訂原則是一項預覽功能。
>
> 需要來賓設定延伸模組，才能在 Azure 虛擬機器中執行審核。
> 若要在所有 Linux 機器上大規模部署擴充功能，請指派下列原則定義：
>   - [部署必要條件，以在 Linux VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

建立來賓設定成品、自動測試成品、建立原則定義，以及發佈原則，都是使用 PowerShell 中的來賓設定模組來完全自動化。 此模組可以安裝在執行 Windows、macOS 或 Linux 的電腦上，且 PowerShell 6.2 或更新版本在本機執行，或使用[Azure Cloud Shell](https://shell.azure.com)或[Azure PowerShell 核心 Docker 映射](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 不支援編譯設定。

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>適用于 Linux 的來賓設定構件和原則

即使在 Linux 環境中，來賓設定還是會使用 Desired State Configuration 做為語言抽象概念。 此實作為以機器碼（c + +）為基礎，因此不需要載入 PowerShell。 不過，它確實需要一個設定 MOF，以描述有關環境的詳細資料。 DSC 會作為 InSpec 的包裝函式，以標準化執行方式、如何提供參數，以及如何將輸出傳回至服務。 使用自訂 InSpec 內容時，不需要 DSC 知識。

#### <a name="configuration-requirements"></a>組態需求

自訂設定的名稱在任何位置都必須一致。 內容套件的 .zip 檔案名稱、MOF 檔案中的設定名稱，以及 Resource Manager 範本中的來賓指派名稱，必須相同。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自訂來賓設定設定

Linux 上的`ChefInSpecResource`來賓設定會使用資源，以[InSpec 設定檔](https://www.inspec.io/docs/reference/profiles/)的名稱提供引擎。 [**名稱**] 是唯一必要的資源屬性。 建立 YaML 檔案和 Ruby 腳本檔案，如下所述。

首先，建立 InSpec 所使用的 YaML 檔案。 檔案會提供有關環境的基本資訊。 以下提供一個範例：

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

將名`inspec.yml`為的檔案儲存到專案目錄`linux-path`中名為的資料夾。

接下來，使用用來審查電腦的 InSpec 語言抽象來建立 Ruby 檔案。

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

將名`linux-path.rb`為的檔案儲存在`controls` `linux-path`目錄內名為的新資料夾中。

最後，建立設定、匯入**PSDesiredStateConfiguration**資源模組，並編譯設定。

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

以名稱`config.ps1`將此檔案儲存在專案資料夾中。 在終端機中執行， `./config.ps1`以在 PowerShell 中執行。 將會建立新的 mof 檔案。

此`Node AuditFilePathExists`命令在技術上並不是必要的， `AuditFilePathExists.mof`但它會產生名`localhost.mof`為的檔案，而不是預設的。 將此 mof 檔案名遵循設定，可讓您在大規模操作時輕鬆地整理許多檔案。



您現在應該具有如下所示的專案結構：

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

支援的檔案必須封裝在一起。 「來賓設定」會使用完成的封裝來建立 Azure 原則定義。

`New-GuestConfigurationPackage` Cmdlet 會建立封裝。 建立 Linux 內容`New-GuestConfigurationPackage`時的 Cmdlet 參數：

- **名稱**：來賓設定封裝名稱。
- 設定 **：已**編譯的設定檔完整路徑。
- **路徑**：輸出檔案夾路徑。 這是選擇性參數。 如果未指定，則會在目前的目錄中建立封裝。
- **ChefProfilePath**： InSpec 設定檔的完整路徑。 只有在建立內容以 audit Linux 時，才支援此參數。

執行下列命令，使用上一個步驟中指定的設定來建立封裝：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

建立設定套件之後，但在將它發佈至 Azure 之前，您可以從您的工作站或 CI/CD 環境測試套件。 GuestConfiguration Cmdlet `Test-GuestConfigurationPackage`在您的開發環境中包含的代理程式與在 Azure 機器內使用的相同。 使用此解決方案，您可以在發行至計費的雲端環境之前，在本機執行整合測試。

由於代理程式實際上是評估本機環境，因此在大部分的情況下，您需要在您打算進行審核的相同作業系統平臺上執行測試 Cmdlet。

`Test-GuestConfigurationPackage` Cmdlet 的參數：

- **名稱**：來賓設定原則名稱。
- **參數**：以 hashtable 格式提供的原則參數。
- **Path**：來賓設定封裝的完整路徑。

執行下列命令來測試上一個步驟所建立的套件：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

此 Cmdlet 也支援來自 PowerShell 管線的輸入。 將`New-GuestConfigurationPackage` Cmdlet 的輸出傳送至`Test-GuestConfigurationPackage` Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

下列檔案是由`New-GuestConfigurationPolicy`所建立：

- **auditIfNotExists json**
- **deployIfNotExists json**
- **方案. json**

Cmdlet 輸出會傳回物件，其中包含原則檔的計畫顯示名稱和路徑。

最後，使用`Publish-GuestConfigurationPolicy` Cmdlet 發佈原則定義。
Cmdlet 只有**Path**參數，它會指向所建立之 JSON 檔案的位置`New-GuestConfigurationPolicy`。

若要執行 [發佈] 命令，您需要在 Azure 中建立原則的存取權。 特定的授權需求記載于[Azure 原則總覽](../overview.md)] 頁面中。 最佳的內建角色是**資源原則參與者**。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 此`Publish-GuestConfigurationPolicy` Cmdlet 會接受來自 PowerShell 管線的路徑。 這項功能表示您可以建立原則檔案，並在一組輸送的命令中發佈它們。

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

使用 InSpec 時，參數通常會在執行時間或當做使用屬性的程式碼處理為輸入。 來賓設定會混淆此程式，以便在指派原則時提供輸入。 在機器內自動建立屬性檔案。 您不需要在專案中建立並新增檔案。 有兩個步驟可將參數新增至您的 Linux audit 專案。

定義 Ruby 檔案中的輸入，您可以在該檔案中編寫要在電腦上審核的內容。 以下提供一個範例。

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Cmdlet `New-GuestConfigurationPolicy`和`Test-GuestConfigurationPolicyPackage`包含名為**Parameters**的參數。 這個參數會採用 hashtable，其中包含每個參數的所有詳細資料，並自動建立用來建立每個 Azure 原則定義之檔案的所有必要區段。

下列範例會建立一個原則定義來審核檔案路徑，其中使用者會在原則指派時提供路徑。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

針對 Linux 原則，請在您的設定中包含屬性**AttributesYmlContent** ，並視需要覆寫這些值。 來賓設定代理程式會自動建立 InSpec 用來儲存屬性的 YAML 檔案。 請參閱下方的範例。

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>原則生命週期

若要釋放原則定義的更新，有兩個需要注意的欄位。

- **版本**：當您執行`New-GuestConfigurationPolicy` Cmdlet 時，您必須指定大於目前發行的版本號碼。 屬性會更新來賓設定指派的版本，讓代理程式能夠辨識更新的封裝。
- **contentHash**： `New-GuestConfigurationPolicy` Cmdlet 會自動更新此屬性。 這是所建立之封裝的雜湊值`New-GuestConfigurationPackage`。 屬性對於您發行的`.zip`檔案而言必須是正確的。 如果只更新**contentUri**屬性，延伸模組就不會接受內容套件。

釋放更新套件的最簡單方式是重複本文中所述的程式，並提供更新的版本號碼。 該進程可確保所有屬性都已正確更新。

## <a name="optional-signing-guest-configuration-packages"></a>選擇性：簽署來賓設定套件

來賓設定自訂原則會使用 SHA256 雜湊來驗證原則封裝未變更。
（選擇性）客戶也可以使用憑證來簽署套件，並強制來賓設定延伸模組只允許已簽署的內容。

若要啟用此案例，您需要完成兩個步驟。 執行 Cmdlet 來簽署內容套件，並將標記附加至需要簽署程式碼的電腦上。

若要使用簽章驗證功能，請`Protect-GuestConfigurationPackage`執行 Cmdlet 來簽署封裝，然後再發佈。 此 Cmdlet 需要「程式碼簽署」憑證。

`Protect-GuestConfigurationPackage` Cmdlet 的參數：

- **Path**：來賓設定封裝的完整路徑。
- **PublicGpgKeyPath**：公用 GPG 金鑰路徑。 只有在簽署適用于 Linux 的內容時，才支援此參數。

建立 GPG 金鑰以搭配 Linux 機器使用的良好參考，是由 GitHub 上的文章所提供，[產生新的 GPG 金鑰](https://help.github.com/en/articles/generating-a-new-gpg-key)。

GuestConfiguration 代理程式預期憑證公開金鑰會出現在 Linux 電腦上的`/usr/local/share/ca-certificates/extra`路徑中。 若要讓節點驗證已簽署的內容，請先在電腦上安裝憑證公開金鑰，再套用自訂原則。 此程式可以使用 VM 內的任何技術來完成，或使用 Azure 原則。 [這裡提供](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)範例範本。
Key Vault 存取原則必須允許計算資源提供者在部署期間存取憑證。 如需詳細步驟，請參閱[在 Azure Resource Manager 中設定虛擬機器的 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

發佈內容之後，請將具有名稱`GuestConfigPolicyCertificateValidation`和值`enabled`的標籤，附加至需要程式碼簽署的所有虛擬機器。 如需如何使用 Azure 原則大規模傳遞標記的詳細說明，請參閱[標記範例](../samples/built-in-policies.md#tags)。 一旦此標記已就緒，使用`New-GuestConfigurationPolicy` Cmdlet 產生的原則定義就會透過「來賓設定」延伸模組來啟用需求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>針對來賓設定原則指派進行疑難排解（預覽）

預覽中提供了一項工具，可協助 Azure 原則來賓設定指派進行疑難排解。 此工具目前為預覽狀態，並已發佈至 PowerShell 資源庫做為模組名稱[來賓設定疑難排解](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)員。

如需此工具中 Cmdlet 的詳細資訊，請在 PowerShell 中使用 Get-help 命令，以顯示內建的指導方針。 當此工具取得經常更新時，這是取得最新資訊的最佳方式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[來賓](../concepts/guest-configuration.md)設定來進行 vm 的審核。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[取得合規性資料](get-compliance-data.md)。
