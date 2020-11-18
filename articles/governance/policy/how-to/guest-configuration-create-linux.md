---
title: 如何建立 Linux 的客體設定原則
description: 了解如何建立 Linux 的 Azure 原則客體設定原則。
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1e81d1a5157cc5872ba2628c8d6cb408e35ab9c6
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694237"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>如何建立 Linux 的客體設定原則

建立自訂原則之前，請參閱 [Azure 原則客體設定](../concepts/guest-configuration.md)的概觀資訊。
 
若要了解如何建立 Windows 的客體設定原則，請參閱[如何建立 Windows 的客體設定原則](./guest-configuration-create.md)頁面

在審核 Linux 時，客體設定會使用 [Chef InSpec](https://www.inspec.io/)。 InSpec 設定檔會定義電腦所應處的條件。 如果設定的評估失敗，則會觸發 **auditIfNotExists** 的原則效果，並將該電腦視為 **不符合規範**。

[Azure 原則客體設定](../concepts/guest-configuration.md)目前只會稽核電腦內的設定， 尚不提供補救電腦內的設定。

使用下列動作來建立自己的設定，以驗證 Azure 或非 Azure 電腦的狀態。

> [!IMPORTANT]
> 在 Azure Government 和 Azure 中國環境中具有來賓設定的自訂原則定義是預覽功能。
>
> 需要客體設定擴充功能，才能在 Azure 虛擬機器中執行稽核。 若要在所有 Linux 電腦上大規模部署擴充功能，請指派下列原則定義： `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`
> 
> 請勿在自訂內容套件中使用秘密或機密資訊。

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

客體設定模組會自動建立自訂內容，包括：

- 建立客體設定內容成品 (.zip)
- 自動測試成品
- 建立原則定義
- 發佈原則

此模組可安裝在執行 Windows、macOS 或 Linux 的電腦上，並搭配本機執行的 PowerShell 6.2 或更新版本、搭配 [Azure Cloud Shell](https://shell.azure.com)，或搭配 [Azure PowerShell Core Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 不支援設定的編譯。

### <a name="base-requirements"></a>基底需求

可安裝模組的作業系統：

- Linux
- macOS
- Windows

> [!NOTE]
> Cmdlet `Test-GuestConfigurationPackage` 需要 OpenSSL 1.0 版，因為相依于 OMI。 這會導致 OpenSSL 1.1 或更新版本的任何環境發生錯誤。
>
> `Test-GuestConfigurationPackage`只有 Windows For Guest 設定模組版本2.1.0 才支援執行 Cmdlet。

客體設定資源模組需要下列軟體：

- PowerShell 6.2 或更新版本。 如果尚未安裝，請依照[這些指示](/powershell/scripting/install/installing-powershell)操作。
- Azure PowerShell 1.5.0 或更新版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
  - 只有 Az 模組 ' Az. Accounts ' 和 ' Az. Resources ' 是必要的。

### <a name="install-the-module"></a>安裝模組

若要在 PowerShell 中安裝 **GuestConfiguration** 模組：

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Linux 的客體設定成品和原則

即使在 Linux 環境中，客體設定還是會使用 Desired State Configuration 作為語言抽象概念。 此實作以機器碼 (C++) 為基礎，因此不需要載入 PowerShell。 不過確實需要一個組態 MOF，以描述有關環境的詳細資料。
DSC 會作為 InSpec 的包裝函式，以將執行方式、參數提供方式，以及將輸出傳回至服務的方式標準化。 您不需要充份了解 DSC 也能使用自訂 InSpec 內容。

#### <a name="configuration-requirements"></a>組態需求

自訂設定的名稱在任何位置都必須一致。 內容套件的 .zip 檔名稱、MOF 檔案中的設定名稱，以及 Azure Resource Manager 範本 (ARM 範本) 的來賓指派名稱必須相同。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自訂客體設定組態

Linux 上的客體設定會使用 `ChefInSpecResource` 資源，以 [InSpec 設定檔](https://www.inspec.io/docs/reference/profiles/)的名稱提供引擎。 **Name** 是唯一需要的資源屬性。 建立 YaML 檔案和 Ruby 指令檔案，如下所述。

首先，建立 InSpec 所使用的 YaML 檔案。 該檔案會提供有關環境的基本資訊。 範例如下所示：

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

將名為 `inspec.yml` 的檔案儲存到專案目錄中名為 `linux-path` 的資料夾。

接下來，使用用於稽核電腦的 InSpec 語言抽象來建立 Ruby 檔案。

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

將名為 `linux-path.rb` 的檔案儲存在 `linux-path` 目錄內名為 `controls` 的新資料夾中。

最後，建立設定並匯入 **PSDesiredStateConfiguration** 資源模組，然後編譯設定。

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

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
AuditFilePathExists -out ./Config
```

將名為 `config.ps1` 的檔案儲存在專案資料夾中。 在終端機中執行 `./config.ps1`，以在 PowerShell 中執行該檔案。 將會建立 MOF 檔案。

就技術上而言，`Node AuditFilePathExists` 命令不是必要的，但該命令會產生名為 `AuditFilePathExists.mof` 的檔案，而非預設的 `localhost.mof`。 使 MOF 檔案名稱遵循設定，以供在大規模作業時輕鬆地整理許多檔案。

您現在應該具有如下所示的專案結構：

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb 
```

支援的檔案必須封裝在一起。 客體設定會使用完成的套件來建立 Azure 原則定義。

`New-GuestConfigurationPackage` Cmdlet 會建立套件。 建立 Linux 內容時所使用的 `New-GuestConfigurationPackage` Cmdlet 參數：

- **Name**：客體設定套件名稱。
- **設定**：已編譯的設定文件完整路徑。
- **路徑**：輸出資料夾路徑。 這是選擇性參數。 如果未指定，則會在目前的目錄中建立套件。
- **ChefInspecProfilePath**： InSpec 設定檔的完整路徑。 只有在建立用來稽核 Linux 的內容時，才支援此參數。

執行下列命令，以使用上一個步驟中指定的設定來建立套件：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

建立設定套件之後，但在將它發佈至 Azure 之前，您可以從您的工作站或持續整合和持續部署 (CI/CD) 環境中測試套件。 GuestConfiguration Cmdlet `Test-GuestConfigurationPackage` 在開發環境中所包含的代理程式，會與 Azure 電腦所使用的代理程式相同。 使用此解決方案，即可先在本機執行整合式測試，再發行到需要計費的雲端環境。

由於代理程式實際上是評估本機環境，因此在大部分的情況下會需要在打算進行稽核的相同 OS 平台上執行 Test- Cmdlet。

`Test-GuestConfigurationPackage` Cmdlet 的參數：

- **Name**：客體設定原則名稱。
- **參數**：以雜湊表格式提供的原則參數。
- **路徑**：客體設定套件的完整路徑。

執行下列命令來測試上一個步驟所建立的套件：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

此 Cmdlet 也支援來自 PowerShell 管線的輸入。 透過管線將 `New-GuestConfigurationPackage` Cmdlet 的輸出傳送至 `Test-GuestConfigurationPackage` Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

下一步是要將檔案發佈至 Azure Blob 儲存體。  此命令 `Publish-GuestConfigurationPackage` 需要 `Az.Storage` 模組。

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

下列檔案是由 `New-GuestConfigurationPolicy` 所建立：

- **auditIfNotExists.json**

Cmdlet 輸出會傳回物件，其中包含原則檔案的方案顯示名稱和路徑。

最後，使用 `Publish-GuestConfigurationPolicy` Cmdlet 來發佈原則定義。 此 Cmdlet 只有 **Path** 參數，該參數會指向由 `New-GuestConfigurationPolicy` 所建立的 JSON 檔案位置。

若要執行發佈命令，則需要在 Azure 中建立原則的存取權。 如需特定的授權需求，請參閱 [Azure 原則概觀](../overview.md)頁面。 最佳的內建角色為 **資源原則參與者**。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

 `Publish-GuestConfigurationPolicy` Cmdlet 會接受來自 PowerShell 管線的路徑。 這項功能表示您可建立原則檔案，並透過管線以單一命令組來發佈這些檔案。

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

在 Azure 中建立原則後，最後一個步驟就是指派定義。 瞭解如何使用 [入口網站](../assign-policy-portal.md)、 [Azure CLI](../assign-policy-azurecli.md)和 [Azure PowerShell](../assign-policy-powershell.md)指派定義。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>在自訂客體設定原則中使用參數

客體設定支援在執行階段覆寫設定的屬性。 這項功能表示套件中 MOF 檔案的值不一定要被視為靜態。 覆寫值是透過 Azure 原則提供，且不會影響撰寫或編譯設定的方式。

使用 InSpec 時，參數通常會在執行階段當作輸出處理，或當作使用屬性的程式碼。 客體設定會混淆此流程，以便系統在指派原則時能提供輸入。 屬性檔案會在電腦內自動建立。 您不需要在專案中建立並新增檔案。 有兩個步驟可將參數新增至您的 Linux 稽核專案。

定義 Ruby 檔案中的輸入，您可以在該檔案中編寫要在電腦上稽核的內容。 範例如下所示。

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含參數命名 **參數**。 此參數會使用雜湊表定義，包括每個參數的所有詳細資料，並為用來建立每個 Azure 原則定義的檔案建立所有必要區段。

下列範例會建立原則定義來審核檔案路徑，其中使用者會在原則指派時提供路徑。

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
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

針對 Linux 原則，請在您的設定中包含 **AttributesYmlContent** 屬性，並視需要覆寫這些值。 客體設定代理程式會自動建立 InSpec 用來儲存屬性的 YAML 檔案。 請參閱下方的範例。

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

若要釋放原則定義的更新，有三個需要注意的欄位。

> [!NOTE]
> `version`來賓設定指派的屬性只會影響 Microsoft 所裝載的封裝。 版本控制自訂內容的最佳作法是在檔案名中包含版本。

- **版本**：當執行 `New-GuestConfigurationPolicy` Cmdlet 時，您必須指定大於目前發佈的版本號碼。
- **contentUri**：當您執行 `New-GuestConfigurationPolicy` Cmdlet 時，您必須指定封裝位置的 URI。 在檔案名中包含套件版本，可確保每個版本中的這個屬性值都有變更。
- **contentHash**：`New-GuestConfigurationPolicy` Cmdlet 會自動更新此屬性。 此為 `New-GuestConfigurationPackage` 所建立套件的雜湊值。 針對您發佈的 `.zip` 檔案而言，此屬性必須是正確的。 如果只更新 **contentUri** 屬性，延伸模組就不會接受內容套件。

發行更新套件的最簡單方式就是重複本文中所述程序，並提供更新的版本號碼。 此程序可確保所有屬性都已正確更新。

### <a name="filtering-guest-configuration-policies-using-tags"></a>使用標籤來篩選客體設定原則

在客體設定模組中，由 Cmdlet 所建立的原則可選擇性地包含標籤篩選。 `New-GuestConfigurationPolicy` 的 **-Tag** 參數支援包含個別標籤項目的雜湊表陣列。 標籤會新增至原則定義的 `If` 區段，且無法由原則指派加以修改。

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>選擇性：簽署客體設定套件

使用 SHA256 雜湊來驗證原則套件的客體設定自訂原則並未變更。
客戶也可選擇性地使用憑證來簽署套件，並強制客體設定延伸模組只允許已簽署的內容。

若要啟用此案例，您需要完成兩個步驟。 執行 Cmdlet 來簽署內容套件，並將標籤附加至需要簽署程式碼的電腦上。

若要使用簽章驗證功能，請執行 `Protect-GuestConfigurationPackage` Cmdlet，以在套件發佈之前進行簽署。 此 Cmdlet 需要「程式碼簽署」憑證。

`Protect-GuestConfigurationPackage` Cmdlet 的參數：

- **路徑**：客體設定套件的完整路徑。
- **PublicGpgKeyPath**：公用 GPG 金鑰路徑。 只有在簽署 Linux 的內容時，才支援此參數。

GitHub 上的[產生新的 GPG 金鑰](https://help.github.com/en/articles/generating-a-new-gpg-key)一文提供了建立 GPG 金鑰以搭配 Linux 機器使用的良好範例。

GuestConfiguration 代理程式預期憑證公開金鑰會出現在 Linux 電腦上的 `/usr/local/share/ca-certificates/extra` 路徑中。 若要讓節點驗證已簽署的內容，請先在電腦上安裝憑證公開金鑰，再套用自訂原則。 此流程可使用 VM 內的任何技術、或使用 Azure 原則來完成。 請參閱[這裡](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)的範例範本。
Key Vault 存取原則必須允許計算資源提供者在部署期間存取憑證。 如需詳細步驟，請參閱[在 Azure Resource Manager 中設定虛擬機器的 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

發佈內容之後，請將標籤 (名稱為 `GuestConfigPolicyCertificateValidation` 且值為 `enabled`) 附加至需要程式碼簽署的所有虛擬機器。 如需如何使用 Azure 原則大規模傳遞標籤的詳細說明，請參閱[標籤範例](../samples/built-in-policies.md#tags)。 備妥此標籤後，使用 `New-GuestConfigurationPolicy` Cmdlet 產生的原則定義就會透過客體設定延伸模組來啟用需求。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[客體設定](../concepts/guest-configuration.md)來稽核 VM。
- 了解如何[以程式設計方式建立原則](./programmatically-create.md)。
- 了解如何[取得合規性資料](./get-compliance-data.md)。
