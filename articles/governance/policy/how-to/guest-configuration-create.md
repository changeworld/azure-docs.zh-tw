---
title: 如何建立來賓設定原則
description: 瞭解如何使用 Azure PowerShell 為 Windows 或 Linux Vm 建立 Azure 原則來賓設定原則。
ms.date: 12/16/2019
ms.topic: how-to
ms.openlocfilehash: 8bd769b61ed87c9ded45ceca11586cfe105740c9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264579"
---
# <a name="how-to-create-guest-configuration-policies"></a>如何建立來賓設定原則

「來賓設定」會使用[Desired State Configuration](/powershell/scripting/dsc/overview/overview) （DSC）資源模組來建立 Azure 機器的審核設定。 DSC 設定會定義機器應處於的條件。 如果設定的評估失敗，則會觸發原則效果**auditIfNotExists** ，並將機器視為**不相容**。

[Azure 原則來賓](../concepts/guest-configuration.md)設定只能用來審查電腦內的設定。 尚未提供機器內設定的補救功能。

使用下列動作來建立您自己的設定，以驗證 Azure 機器的狀態。

> [!IMPORTANT]
> 具有「來賓設定」的自訂原則是一項預覽功能。

## <a name="add-the-guestconfiguration-resource-module"></a>新增 GuestConfiguration 資源模組

若要建立來賓設定原則，必須新增資源模組。 此資源模組可以搭配使用本機安裝的 PowerShell、 [Azure Cloud Shell](https://shell.azure.com)或[Azure PowerShell 核心 Docker 映射](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> 雖然**GuestConfiguration**模組適用于上述環境，但編譯 DSC 設定的步驟必須在 Windows PowerShell 5.1 中完成。

### <a name="base-requirements"></a>基底需求

來賓設定資源模組需要下列軟體：

- PowerShell。 如果尚未安裝，請依照[這些指示](/powershell/scripting/install/installing-powershell)操作。
- Azure PowerShell 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。

### <a name="install-the-module"></a>安裝模組

「來賓設定」會使用**GuestConfiguration**資源模組來建立 DSC 設定，並將其發佈至 Azure 原則：

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

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>建立自訂來賓設定設定和資源

建立來賓設定之自訂原則的第一個步驟是建立 DSC 設定。 如需 DSC 概念和術語的總覽，請參閱[POWERSHELL Dsc 總覽](/powershell/scripting/dsc/overview/overview)。

如果您的設定只需要安裝來賓設定代理程式內建的資源，則您只需要撰寫設定 MOF 檔案。 如果您需要執行其他腳本，則需要撰寫自訂資源模組。

### <a name="requirements-for-guest-configuration-custom-resources"></a>來賓設定自訂資源的需求

當來賓設定審核電腦時，它會先執行 `Test-TargetResource` 以判斷它是否處於正確的狀態。 函數所傳回的布林值會決定來賓指派的 Azure Resource Manager 狀態是否應該符合規範。 如果設定中的任何資源都 `$false` 布林值，則提供者會 `Get-TargetResource`執行。 如果布林值為 `$true` 則 `Get-TargetResource` 不會呼叫。

#### <a name="configuration-requirements"></a>組態需求

來賓設定若要使用自訂設定，唯一的需求是讓設定的名稱在使用時保持一致。 此名稱需求包括內容套件的 .zip 檔案名稱、儲存在內容套件內之 MOF 檔案中的設定名稱，以及 Resource Manager 範本中用來做為來賓指派名稱的設定名稱。

#### <a name="get-targetresource-requirements"></a>TargetResource 需求

函式 `Get-TargetResource` 具有 Windows Desired State Configuration 尚未需要之來賓設定的特殊需求。

- 傳回的雜湊表必須包含名為「**原因**」的屬性。
- 原因屬性必須是陣列。
- 陣列中的每個專案都應該是具有名為**Code**和**片語**之索引鍵的雜湊表。

當電腦不符合規範時，服務會使用 [原因] 屬性來標準化資訊的呈現方式。 您可以將每個專案視為「原因」，原因是該資源不符合規範。 屬性是一個陣列，因為資源可能會因為一個以上的原因而不符合規範。

服務應該會有屬性**代碼**和**片語**。 撰寫自訂資源時，請將您想要顯示的文字（通常是 stdout）設定為資源不符合 [**片語**] 值的原因。 程式**代碼**有特定的格式需求，因此報告可以清楚顯示用來執行 audit 的資源相關資訊。 此解決方案可讓來賓設定可擴充。 只要輸出可以被捕捉並當做 [**片語**] 屬性的字串值傳回，即可執行任何命令來審核電腦。

- **Code** （字串）：資源的名稱，重複，再加上不含空格的簡短名稱作為原因的識別碼。 這三個值應該以冒號分隔，且不含空格。
  - 範例 `registry:registry:keynotpresent`
- **片語**（字串）：人們看得懂的文字，以說明設定不符合規範的原因。
  - 範例 `The registry key $key is not present on the machine.`

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

#### <a name="scaffolding-a-guest-configuration-project"></a>設定來賓設定專案的架構

對於想要加速開始使用和從範例程式碼執行程式的開發人員，名為「來賓設定**專案**」的社區專案會以[Plaster](https://github.com/powershell/plaster) PowerShell 模組的範本形式存在。 這項工具可用來 scaffold 專案，包括可運作的設定和範例資源，以及一組用來驗證專案的[Pester](https://github.com/pester/pester)測試。 此範本也包含 Visual Studio Code 的工作執行程式，可自動建立及驗證來賓設定套件。 如需詳細資訊，請參閱 GitHub 專案[來賓設定專案](https://github.com/microsoft/guestconfigurationproject)。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自訂來賓設定設定

Linux 上的「來賓設定」 DSC 設定會使用 `ChefInSpecResource` 資源來提供引擎[Chef InSpec](https://www.chef.io/inspec/)定義的名稱。 [**名稱**] 是唯一必要的資源屬性。

下列範例會建立名為**基準**的設定、匯入**GuestConfiguration**資源模組，並使用 `ChefInSpecResource` 資源將 InSpec 定義的名稱設定為**linux-patch-基準**：

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

如需詳細資訊，請參閱[撰寫、編譯及](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)套用設定。

### <a name="custom-guest-configuration-configuration-on-windows"></a>Windows 上的自訂來賓設定設定

只有來賓設定代理程式會使用 Azure 原則來賓設定的 DSC 設定，它不會與 Windows PowerShell Desired State Configuration 衝突。

下列範例會建立名為**AuditBitLocker**的設定、匯入**GuestConfiguration**資源模組，並使用 `Service` 資源來審核執行中的服務：

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

如需詳細資訊，請參閱[撰寫、編譯及](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)套用設定。

## <a name="create-guest-configuration-custom-policy-package"></a>建立來賓設定自訂原則封裝

一旦完成 MOF 編譯，就必須將支援的檔案封裝在一起。 「來賓設定」會使用完成的封裝來建立 Azure 原則定義。 套件包含：

- 以 MOF 形式編譯的 DSC 設定
- Modules 資料夾
  - GuestConfiguration 模組
  - DscNativeResources 模組
  - 廠商具有 Chef InSpec 定義和其他內容的資料夾
  - 時段未內建的 DSC 資源模組

`New-GuestConfigurationPackage` Cmdlet 會建立封裝。 下列格式是用來建立自訂套件：

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

`New-GuestConfigurationPackage` Cmdlet 的參數：

- **名稱**：來賓設定封裝名稱。
- 設定 **：已**編譯的 DSC 設定檔完整路徑。
- **路徑**：輸出檔案夾路徑。 這是選擇性參數。 如果未指定，則會在目前的目錄中建立封裝。
- **ChefProfilePath**： InSpec 設定檔的完整路徑。 只有在建立內容以 audit Linux 時，才支援此參數。

完成的封裝必須儲存在受管理的虛擬機器可存取的位置。 範例包括 GitHub 存放庫、Azure 儲存機制或 Azure 儲存體。 如果您不想讓套件公開，您可以在 URL 中包含[SAS 權杖](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。
雖然這項設定只適用于存取封裝，而不是與服務通訊，但您也可以在私人網路中執行機器的[服務端點](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)。

## <a name="test-a-guest-configuration-package"></a>測試來賓設定套件

建立設定套件之後，但在將它發佈至 Azure 之前，您可以從您的工作站或 CI/CD 環境測試套件的功能。 GuestConfiguration 模組包含 Cmdlet `Test-GuestConfigurationPackage`，會在您的開發環境中載入與 Azure 機器內部使用的相同代理程式。 使用此解決方案，您可以在發行至計費的測試/QA/生產環境之前，在本機執行整合測試。

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

`Test-GuestConfigurationPackage` Cmdlet 的參數：

- **名稱**：來賓設定原則名稱。
- **參數**：以 hashtable 格式提供的原則參數。
- **Path**：來賓設定封裝的完整路徑。

此 Cmdlet 也支援來自 PowerShell 管線的輸入。 透過管道將 `New-GuestConfigurationPackage` Cmdlet 的輸出傳送至 `Test-GuestConfigurationPackage` Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

如需如何使用參數進行測試的詳細資訊，請參閱下面的[使用自訂來賓設定原則中的參數](#using-parameters-in-custom-guest-configuration-policies)一節。

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>建立 Azure 原則定義和計畫部署檔案

建立來賓設定自訂原則封裝並上傳至電腦可存取的位置之後，請建立 Azure 原則的來賓設定原則定義。 `New-GuestConfigurationPolicy` Cmdlet 會採用可公開存取的來賓設定自訂原則套件，並建立**auditIfNotExists**和**deployIfNotExists**原則定義。 同時也會建立同時包含這兩個原則定義的原則計畫定義。

下列範例會從適用于 Windows 的來賓設定自訂原則套件，在指定的路徑中建立原則和計畫定義，並提供名稱、描述和版本：

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

`New-GuestConfigurationPolicy` Cmdlet 的參數：

- **ContentUri**：來賓設定內容套件的公用 HTTP （s） uri。
- **DisplayName**：原則顯示名稱。
- **描述**：原則描述。
- **參數**：以 hashtable 格式提供的原則參數。
- **版本**：原則版本。
- **路徑**：原則定義建立所在的目的地路徑。
- **平臺**：來賓設定原則和內容套件的目標平臺（Windows/Linux）。

下列檔案是由 `New-GuestConfigurationPolicy`所建立：

- **auditIfNotExists json**
- **deployIfNotExists json**
- **方案. json**

Cmdlet 輸出會傳回物件，其中包含原則檔的計畫顯示名稱和路徑。

如果您想要使用此命令來 scaffold 自訂原則專案，您可以對這些檔案進行變更。 其中一個範例會修改 ' If ' 區段，以評估機器是否有特定標記。 如需建立原則的詳細資訊，請參閱以程式設計[方式建立原則](./programmatically-create.md)。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>在自訂來賓設定原則中使用參數

「來賓設定」支援在執行時間覆寫設定的屬性。 這項功能表示封裝中 MOF 檔案中的值不一定要被視為靜態。 覆寫值是透過 Azure 原則提供，而且不會影響撰寫或編譯設定的方式。

Cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含名為**Parameters**的參數。 這個參數會採用 hashtable 定義，包括每個參數的所有詳細資料，並自動建立用來建立每個 Azure 原則定義之檔案的所有必要區段。

下列範例會建立 Azure 原則來審核服務，而使用者會在原則指派時從服務清單中選取。

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
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

針對 Linux 原則，請在您的設定中包含屬性**AttributesYmlContent** ，並視需要覆寫這些值。 來賓設定代理程式會自動建立 InSpec 用來儲存屬性的 YAML 檔案。 請參閱下方的範例。

```powershell
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

針對每個額外的參數，將 hashtable 加入至陣列。 在原則檔案中，您會看到新增至 [來賓設定] configurationName 的屬性，可識別資源類型、名稱、屬性和值。

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>發行至 Azure 原則

**GuestConfiguration** resource 模組可讓您透過 `Publish-GuestConfigurationPolicy` Cmdlet，在 Azure 中建立原則定義和計畫定義。
Cmdlet 只有**Path**參數，會指向 `New-GuestConfigurationPolicy`所建立的三個 JSON 檔案的位置。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

`Publish-GuestConfigurationPolicy` Cmdlet 會接受來自 PowerShell 管線的路徑。 這項功能表示您可以建立原則檔案，並在一組輸送的命令中發佈它們。

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

透過在 Azure 中建立的原則和計畫定義，最後一步就是指派方案。 瞭解如何使用[入口網站](../assign-policy-portal.md)、 [Azure CLI](../assign-policy-azurecli.md)和[Azure PowerShell](../assign-policy-powershell.md)來指派方案。

> [!IMPORTANT]
> 來賓設定原則必須**一律**使用結合_AuditIfNotExists_和_DeployIfNotExists_原則的計畫來指派。 如果只指派_AuditIfNotExists_原則，則不會部署必要條件，原則一律會顯示「0」伺服器符合規範。

## <a name="policy-lifecycle"></a>原則生命週期

當您使用自訂內容套件發佈自訂 Azure 原則之後，如果您想要發佈新的版本，則必須更新兩個欄位。

- **版本**：當您執行 `New-GuestConfigurationPolicy` Cmdlet 時，您必須指定大於目前發行的版本號碼。 屬性會更新新原則檔案中的來賓設定指派版本，讓擴充功能能夠辨識封裝已更新。
- **contentHash**： `New-GuestConfigurationPolicy` Cmdlet 會自動更新此屬性。 這是 `New-GuestConfigurationPackage`所建立之封裝的雜湊值。 對於您發行的 `.zip` 檔案而言，屬性必須是正確的。 如果只更新**contentUri**屬性（例如，在有人可以從入口網站手動變更原則定義的情況下），延伸模組就不會接受內容套件。

釋放更新套件的最簡單方式是重複本文中所述的程式，並提供更新的版本號碼。 該進程可確保所有屬性都已正確更新。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>將 Windows 群組原則內容轉換成 Azure 原則來賓設定

「來賓設定」在「Windows 機器」執行時，是 PowerShell Desired State Configuration 語法的實施。 DSC 社區已發佈工具，可將匯出的群組原則範本轉換成 DSC 格式。 將此工具與上述的來賓設定 Cmdlet 搭配使用，您就可以轉換 Windows 群組原則內容，並將其封裝/發佈以供 Azure 原則進行審核。 如需使用此工具的詳細資訊，請參閱[快速入門：將群組原則轉換成 DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart)一文。
內容轉換後，上述步驟會建立套件，並將其發佈為 Azure 原則將與任何 DSC 內容相同。

## <a name="optional-signing-guest-configuration-packages"></a>選擇性：簽署來賓設定套件

根據預設，來賓設定自訂原則會使用 SHA256 雜湊來驗證原則封裝是否已從發行到正在進行審核的伺服器讀取時不會變更。
（選擇性）客戶也可以使用憑證來簽署套件，並強制來賓設定延伸模組只允許已簽署的內容。

若要啟用此案例，您需要完成兩個步驟。 執行 Cmdlet 來簽署內容套件，並將標記附加至需要簽署程式碼的電腦上。

若要使用簽章驗證功能，請執行 `Protect-GuestConfigurationPackage` Cmdlet，以在套件發佈之前進行簽署。 此 Cmdlet 需要「程式碼簽署」憑證。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` Cmdlet 的參數：

- **Path**：來賓設定封裝的完整路徑。
- **憑證**：用來簽署封裝的程式碼簽署憑證。 只有在簽署 Windows 的內容時，才支援這個參數。
- **PrivateGpgKeyPath**：私用 GPG 金鑰路徑。 只有在簽署適用于 Linux 的內容時，才支援此參數。
- **PublicGpgKeyPath**：公用 GPG 金鑰路徑。 只有在簽署適用于 Linux 的內容時，才支援此參數。

GuestConfiguration 代理程式預期憑證公開金鑰會出現在 Windows 電腦上的「受信任的根憑證授權」中，以及 Linux 機器的路徑 `/usr/local/share/ca-certificates/extra`。 若要讓節點驗證已簽署的內容，請先在電腦上安裝憑證公開金鑰，再套用自訂原則。 此程式可以使用 VM 內的任何技術來完成，或使用 Azure 原則。 [這裡提供](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)範例範本。
Key Vault 存取原則必須允許計算資源提供者在部署期間存取憑證。 如需詳細步驟，請參閱[在 Azure Resource Manager 中設定虛擬機器的 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

以下是從簽署憑證匯出公開金鑰以匯入到電腦的範例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

建立 GPG 金鑰以搭配 Linux 機器使用的良好參考，是由 GitHub 上的文章所提供，[產生新的 GPG 金鑰](https://help.github.com/en/articles/generating-a-new-gpg-key)。

發佈內容之後，請將名稱為 `GuestConfigPolicyCertificateValidation` 的標籤和值 `enabled` 附加至需要程式碼簽署的所有虛擬機器。 如需如何使用 Azure 原則大規模傳遞標記的詳細說明，請參閱[標記範例](../samples/built-in-policies.md#tags)。 一旦此標記備妥，使用 `New-GuestConfigurationPolicy` Cmdlet 產生的原則定義就會透過來賓設定延伸模組來啟用需求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>針對來賓設定原則指派進行疑難排解（預覽）

預覽中提供了一項工具，可協助 Azure 原則來賓設定指派進行疑難排解。 此工具目前為預覽狀態，並已發佈至 PowerShell 資源庫做為模組名稱[來賓設定疑難排解](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)員。

如需此工具中 Cmdlet 的詳細資訊，請在 PowerShell 中使用 Get-help 命令，以顯示內建的指導方針。 當此工具取得經常更新時，這是取得最新資訊的最佳方式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[來賓](../concepts/guest-configuration.md)設定來進行 vm 的審核。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[取得合規性資料](get-compliance-data.md)。
