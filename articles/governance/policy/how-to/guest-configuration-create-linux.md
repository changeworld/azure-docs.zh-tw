---
title: 如何為 Linux 創建來賓配置策略
description: 瞭解如何為 Linux 創建 Azure 策略來賓配置策略。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f93aafc8f2c016218b1b7fea82558ea6ba4b4ff8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365401"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>如何為 Linux 創建來賓配置策略

在創建自訂策略之前，請閱讀 Azure[策略來賓配置](../concepts/guest-configuration.md)上的概述資訊。
 
要瞭解如何為 Windows 創建來賓配置策略，請參閱["如何為 Windows 創建來賓配置策略](./guest-configuration-create.md)"頁

審核 Linux 時，來賓配置使用[Chef InSpec](https://www.inspec.io/)。 InSpec 設定檔定義電腦應處於的條件。 如果配置評估失敗，將觸發策略效果**審核IfNotExists，** 並且電腦被視為**不符合**。

[Azure 策略來賓配置](../concepts/guest-configuration.md)只能用於審核電腦內的設置。 機器內部設置的修正尚不可用。

使用以下操作創建自己的配置以驗證 Azure 或非 Azure 電腦的狀態。

> [!IMPORTANT]
> 具有來賓配置的自訂策略是預覽功能。

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

使用 PowerShell 中的來賓配置模組，創建來賓設定項目、自動測試專案、創建策略定義和發佈策略是完全可自動的。 該模組可以安裝在運行 Windows、macOS 或 Linux 的電腦上，其中 PowerShell 6.2 或更高版本在本地運行，或者使用[Azure 雲外殼](https://shell.azure.com)，或者使用[Azure PowerShell 核心 Docker 映射](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 上不支援配置編譯。

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Linux 的來賓配置工件和策略

即使在 Linux 環境中，來賓配置也使用所需的狀態配置作為語言抽象。 實現基於本機代碼 （C++），因此不需要載入 PowerShell。 但是，它確實需要配置 MOF 描述有關環境的詳細資訊。 DSC 充當 InSpec 的包裝器，以標準化其執行方式、參數的提供方式以及輸出如何返回到服務。 使用自訂 InSpec 內容時，不需要對 DSC 瞭解甚少。

#### <a name="configuration-requirements"></a>組態需求

自訂配置的名稱必須在所有位置保持一致。 內容包的 .ZIP 檔案的名稱、MOF 檔案中的配置名稱以及資源管理器範本中的來賓分配名稱必須相同。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自訂來賓配置配置

Linux 上的來賓配置使用`ChefInSpecResource`資源向引擎提供[InSpec 設定檔](https://www.inspec.io/docs/reference/profiles/)的名稱。 **名稱**是唯一必需的資源屬性。 創建 YaML 檔和 Ruby 指令檔，詳情如下。

首先，創建 InSpec 使用的 YaML 檔。 該檔提供有關環境的基本資訊。 下面給出了一個示例：

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

將此檔保存到專案目錄中指定的`linux-path`資料夾。

接下來，使用用於審核電腦的 InSpec 語言抽象創建 Ruby 檔。

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

將此檔保存在`controls``linux-path`目錄中命名的新資料夾中。

最後，創建配置、導入**來賓配置**資源模組，`ChefInSpecResource`並使用該資源設置 InSpec 設定檔的名稱。

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
AuditFilePathExists -out ./Config
```

該`Node AuditFilePathExists`命令在技術上不是必需的，但它生成名為`AuditFilePathExists.mof`而不是預設值的檔。 `localhost.mof` 使 .mof 檔案名遵循配置，因此在大規模運行時很容易組織許多檔。

現在，您應該有如下專案結構：

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

支援檔必須打包在一起。 來賓配置使用已完成的包創建 Azure 策略定義。

Cmdlet`New-GuestConfigurationPackage`創建包。 創建 Linux`New-GuestConfigurationPackage`內容時的 Cmdlet 參數：

- **名稱**：來賓配置包名稱。
- **配置**：編譯的配置文檔完整路徑。
- **路徑**：輸出檔案夾路徑。 這是選擇性參數。 如果未指定，則在目前的目錄中創建包。
- **Chef 設定檔路徑**：通往 InSpec 設定檔的完整路徑。 僅當創建內容以審核 Linux 時，才會支援此參數。

運行以下命令，使用上一步驟中給出的配置創建包：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof'
  -ChefProfilePath './'
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
  -Path ./AuditFilePathExists.zip
```

Cmdlet 還支援從 PowerShell 管道輸入。 將 Cmdlet 的`New-GuestConfigurationPackage`輸出輸送到`Test-GuestConfigurationPackage`Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

以下檔由 創建`New-GuestConfigurationPolicy`。

- **審計IfNotExists.json**
- **部署"不存在.json"**
- **倡議.json**

Cmdlet 輸出返回一個包含策略檔的計畫顯示名稱和路徑的物件。

最後，使用`Publish-GuestConfigurationPolicy`Cmdlet 發佈策略定義。
Cmdlet 僅具有**指向**由 創建的`New-GuestConfigurationPolicy`JSON 檔的位置的路徑參數。

要運行"發佈"命令，需要訪問才能在 Azure 中創建策略。 特定的授權要求記錄在 Azure[策略概述](../overview.md)頁中。 最好的內置角色是**資源策略參與者**。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy` Cmdlet 接受來自 PowerShell 管道的路徑。 此功能意味著您可以創建策略檔，並在一組管道命令中發佈它們。

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

使用 InSpec 時，參數通常在運行時作為輸入處理，或者使用屬性作為代碼處理。 來賓配置混淆了此過程，以便在分配策略時提供輸入。 屬性檔在電腦中自動創建。 您無需在專案中創建和添加檔。 向 Linux 審核專案添加參數有兩個步驟。

定義 Ruby 檔中的輸入，在其中編寫要在電腦上審核的內容。 下面給出了一個示例。

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Cmdlet，`New-GuestConfigurationPolicy`並`Test-GuestConfigurationPolicyPackage`包含一個名為**參數的參數**。 此參數採用雜湊表，包括有關每個參數的所有詳細資訊，並自動創建用於創建每個 Azure 策略定義的檔的所有必需部分。

下面的示例創建一個策略定義來審核檔路徑，使用者在分配策略時提供路徑。

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

對於 Linux 策略，請在配置中包括**屬性屬性屬性屬性YmlContent，** 並根據需要覆蓋值。 來賓配置代理會自動創建 InSpec 用於存儲屬性的 YAML 檔。 請參閱下方的範例。

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

## <a name="policy-lifecycle"></a>策略生命週期

要發佈策略定義的更新，需要注意兩個欄位。

- **版本**：運行`New-GuestConfigurationPolicy`Cmdlet 時，必須指定大於當前發佈的版本號的版本號。 屬性更新來賓配置分配的版本，以便代理識別更新的包。
- **內容雜湊**：此屬性由`New-GuestConfigurationPolicy`Cmdlet 自動更新。 它是 創建的`New-GuestConfigurationPackage`包的雜湊值。 屬性必須對發佈的`.zip`檔正確。 如果僅更新**內容 Uri**屬性，則擴展不會接受內容包。

發佈更新的包的最簡單方法是重複本文中描述的過程並提供更新的版本號。 此過程可確保已正確更新所有屬性。

## <a name="optional-signing-guest-configuration-packages"></a>可選：對來賓配置包進行簽名

來賓配置自訂策略使用 SHA256 雜湊來驗證策略包未更改。
或者，客戶還可以使用證書對包進行簽名，並強制來賓配置擴展僅允許簽名內容。

要啟用此方案，您需要完成兩個步驟。 運行 Cmdlet 對內容包進行簽名，並將標記追加到需要簽名的電腦。

要使用簽名驗證功能，請運行`Protect-GuestConfigurationPackage`Cmdlet 在發佈包之前對包進行簽名。 此 Cmdlet 需要"代碼簽名"證書。

Cmdlet`Protect-GuestConfigurationPackage`的參數：

- **路徑**：來賓配置包的完整路徑。
- **公共GpgKeyPath：** 公共GPG金鑰路徑。 僅當對 Linux 的內容進行簽名時，才支援此參數。

在 GitHub 上一篇文章提供了創建 GPG 金鑰以與 Linux 電腦一起使用的良好參考，[該文章生成了新的 GPG 金鑰](https://help.github.com/en/articles/generating-a-new-gpg-key)。

Guest 配置代理希望證書公開金鑰存在於 Linux 電腦的`/usr/local/share/ca-certificates/extra`路徑中。 要驗證簽名內容的節點，請在應用自訂策略之前在電腦上安裝證書公開金鑰。 此過程可以使用 VM 中的任何技術或使用 Azure 策略來完成。 [此處提供了](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)一個示例範本。
金鑰保存庫訪問策略必須允許計算資來源提供者在部署期間訪問證書。 有關詳細步驟，請參閱[在 Azure 資源管理器中為虛擬機器設置金鑰保存庫](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

發佈內容後，將名稱`GuestConfigPolicyCertificateValidation`和值`enabled`標記追加到需要代碼簽名的所有虛擬機器。 有關如何使用 Azure 策略大規模交付標記，請參閱[標記示例](../samples/built-in-policies.md#tags)。 此標記就位後，使用`New-GuestConfigurationPolicy`Cmdlet 生成的策略定義可通過來賓配置擴展啟用要求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>排除來賓配置策略分配（預覽）

預覽版中提供了一個工具，可説明解決 Azure 策略來賓配置分配的故障。 該工具處於預覽狀態，並已作為模組名稱來賓[配置疑難排解](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)發佈到 PowerShell 庫。

有關此工具中的 Cmdlet 的詳細資訊，請使用 PowerShell 中的"獲取説明"命令來顯示內置指南。 由於該工具正在頻繁更新，這是獲取最新資訊的最佳方式。

## <a name="next-steps"></a>後續步驟

- 瞭解有關使用[來賓配置](../concepts/guest-configuration.md)審核 VM 的事。
- 瞭解如何[以程式設計方式創建策略](programmatically-create.md)。
- 瞭解如何[獲取合規性資料](get-compliance-data.md)。
