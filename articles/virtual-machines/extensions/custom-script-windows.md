---
title: 適用於 Windows 的 Azure 自訂指令碼擴充功能
description: 使用「自訂指令碼擴充功能」來自動執行 Windows VM 組態工作
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: e50c0b0fcb883b43650a5d99cea5aa39bae1cd94
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426260"
---
# <a name="custom-script-extension-for-windows"></a>Windows 的自訂指令碼延伸模組

「自訂指令碼擴充功能」會在 Azure 虛擬機器上下載並執行指令碼。 此擴充功能適用於部署後設定、軟體安裝或其他任何設定/管理工作。 您可以從 Azure 儲存體或 GitHub 下載指令碼，或是在擴充功能執行階段將指令碼提供給 Azure 入口網站。 「自訂指令碼擴充功能」會與 Azure Resource Manager 範本整合，並可使用 Azure CLI、PowerShell、Azure 入口網站或「Azure 虛擬機器 REST API」來加以執行。

本文件詳細說明如何透過 Azure PowerShell 模組、Azure Resource Manager 範本使用自訂指令碼擴充功能，同時也詳細說明 Windows 系統上的疑難排解步驟。

## <a name="prerequisites"></a>必要條件

> [!NOTE]  
> 請勿使用自訂指令碼擴充功能以相同的 VM 執行 Update-AzVM 作為其參數，因為它會等候其本身。  

### <a name="operating-system"></a>作業系統

適用於 Windows 的自訂指令碼擴充功能會在擴充功能所支援的擴充功能 OS 上執行；
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>指令碼位置

您可以將擴充功能設定為使用 Azure Blob 儲存體認證來存取 Azure Blob 儲存體。 指令碼可以位於任何位置，前提是 VM 可以路由傳送至該端點，例如 GitHub 或內部檔案伺服器。

### <a name="internet-connectivity"></a>網際網路連線

如果您需要在外部下載指令碼 (例如從 GitHub 或 Azure 儲存體)，則必須開放額外的防火牆和網路安全性群組連接埠。 例如，如果您的指令碼位於 Azure 儲存體，則可以允許使用[儲存體](../../virtual-network/security-overview.md#service-tags)適用的 Azure NSG 服務標記進行存取。

如果您的指令碼是在本機伺服器上，則仍然可能需要開放額外的防火牆和網路安全性群組連接埠。

### <a name="tips-and-tricks"></a>秘訣與技巧

* 這個擴充功能的最常見失敗原因是指令碼語法錯誤。請測試指令碼執行時沒有錯誤，而且也在指令碼中放入額外記錄，以方便找出失敗之處。
* 撰寫具有等冪性的指令碼。 這可確保如果不小心再次執行，不會造成系統變更。
* 請確定在指令碼執行時，不需要使用者輸入。
* 指令碼可執行的時間為 90 分鐘。若超過這個時間，將會導致擴充功能佈建失敗。
* 不要在指令碼中放置重新開機，此動作會導致正在安裝的其他擴充功能發生問題。 若發佈重新開機，擴充功能將不會在重新啟動之後繼續執行。
* 如果您的指令碼會造成重新開機，則請安裝應用程式並執行指令碼，您可以使用 Windows 排程工作來排程重新開機，也可以使用 DSC、Chef 或 Puppet 擴充功能等工具來進行。
* 建議您不要執行會導致 VM 代理程式停止或更新的腳本。 這可以讓延伸處於轉換狀態，進而導致超時。
* 擴充功能只會執行指令碼一次。如果您想要在每次開機時執行指令碼，則必須使用擴充功能建立 Windows 排程工作。
* 如果您想要排程指令碼的執行時間，則應該使用擴充功能來建立 Windows 排程工作。
* 當指令碼正在執行時，只能從 Azure 入口網站或 CLI 看到「正在轉換」擴充功能狀態。 如果您需要執行中指令碼更頻繁的狀態更新，便必須建立自己的解決方案。
* 自訂指令碼擴充功能未原生支援 Proxy 伺服器，但是您可以在指令碼中使用支援 Proxy 伺服器的檔案傳輸工具，例如 *Curl*
* 請留意指令碼或命令所依賴的非預設目錄位置是否具備處理此情形的邏輯。
* 自訂指令碼擴充功能將會以 LocalSystem 帳戶執行
* 如果您打算使用 *storageAccountName* 和 *storageAccountKey* 屬性，則這些屬性必須在 *protectedSettings*中共置。

## <a name="extension-schema"></a>擴充功能結構描述

「自訂指令碼擴充功能」組態會指定指令碼位置和要執行命令等項目。 您可將此組態儲存在組態檔中、在命令列中指定組態，或在 Azure Resource Manager 範本中指定組態。

您可將敏感性資料儲存在受保護的組態中，此組態會經過加密，並且只會在虛擬機器內解密。 當執行命令包含機密資料 (例如密碼) 時，受保護的組態會相當有用。

這些項目應被視為敏感性資料，並在擴充功能保護的設定組態中指定。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> managedIdentity 屬性**不得**與 storageAccountName 或 storageAccountKey 屬性一起使用

> [!NOTE]
> 一次只能在 VM 上安裝一個版本的擴充功能，若為相同的 VM 指定相同 Resource Manager 範本中的自訂指令碼兩次，作業將會失敗。

> [!NOTE]
> 我們可以在 VirtualMachine 資源內使用此結構描述，也可以將此結構描述作為獨立資源。 如果將此擴充功能作為 ARM 範本中的獨立資源，則資源名稱的格式必須是「virtualMachineName/extensionName」。

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | 字串 |
| type | CustomScriptExtension | 字串 |
| typeHandlerVersion | 1.10 | int |
| fileUris (例如) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (範例) | 123456789 | 32 位元整數 |
| commandToExecute (例如) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | 字串 |
| storageAccountName (例如) | examplestorageacct | 字串 |
| storageAccountKey (例如) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 字串 |
| managedIdentity (例如) | { } 或 { "clientId":"31b403aa-c364-4240-a7ff-d85fb6cd7232" } 或 { "objectId":"12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | JSON 物件 |

>[!NOTE]
>這些屬性名稱會區分大小寫。 為了避免發生部署問題，請使用如下所示的名稱。

#### <a name="property-value-details"></a>屬性值詳細資料

* `commandToExecute`：(**必要**，字串) 要執行的進入點指令碼。 如果您的命令包含機密資料 (例如密碼)，或您的 fileUris 是敏感資料，請改用此欄位。
* `fileUris`：(選擇性，字串陣列) 要下載之檔案的 URL。
* `timestamp` (選擇性，32 位元整數) 只有在透過變更此欄位的值來觸發指令碼的重新執行時，才需使用此欄位。  任何整數值都是可接受的；只要與先前的值不同即可。
* `storageAccountName`：(選用，字串) 儲存體帳戶的名稱。 如果您指定儲存體證明資料，則所有 `fileUris` 都必須是 Azure Blob 的 URL。
* `storageAccountKey`：(選用，字串) 儲存體帳戶的存取金鑰
* `managedIdentity`：(選用，JSON 物件) 用來下載檔案的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
  * `clientId`：(選用，字串) 受控識別的用戶端識別碼
  * `objectId`：(選用，字串) 受控識別的物件識別碼

下列值可以在公開或受保護的設定中設定，擴充功能將會拒絕任何同時在公開和受保護的設定中設定下列值的組態。

* `commandToExecute`

使用公開設定可能有助於偵錯，但建議您使用受保護的設定。

公開設定會以純文字的格式，傳送到執行指令碼所在的 VM。  受保護的設定會使用只有 Azure 和 VM 知道的金鑰來加密。 這些設定會在傳送時儲存到 VM 中。亦即，如果這些設定經過加密，也會在 VM 上以加密的形式儲存。 用來解密加密值的憑證會儲存在 VM 上，並在執行階段用於解密設定 (如有必要)。

####  <a name="property-managedidentity"></a>屬性：managedIdentity
> [!NOTE]
> **必須**在受保護的設定中，才能指定此屬性。

CustomScript (1.10 版之後) 支援使用[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)來從「fileUris」設定中提供的 URL 下載檔案。 其可讓 CustomScript 存取 Azure 儲存體私人 Blob 或容器，而不需要使用者傳遞 SAS 權杖或儲存體帳戶金鑰之類的秘密。

若要使用這項功能，使用者必須將[系統指派的](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)或[使用者指派的](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)身分識別新增至應執行 CustomScript 的 VM 或 VMSS，並[向受控識別授與 Azure 儲存體容器或 Blob 的存取權](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access)。

若要在目標 VM/VMSS 上使用系統指派的身分識別，請將 [managedidentity] 欄位設定為空的 JSON 物件。 

> 範例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

若要在目標 VM/VMSS 上使用使用者指派的身分識別，請使用用戶端識別碼或受控識別的物件識別碼來設定 [managedidentity] 欄位。

> 範例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity 屬性**不得**與 storageAccountName 或 storageAccountKey 屬性一起使用

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節詳述的 JSON 結構描述可以用於 Azure Resource Manager 範本，以在部署期間執行自訂指令碼擴充功能。 下列範例會說明如何使用自訂指令碼擴充功能：

* [教學課程：使用 Azure Resource Manager 範本部署虛擬機器擴充功能](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [在 Windows 和 Azure SQL DB 上部署兩層式應用程式](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzVMCustomScriptExtension` 命令可以用來將自訂指令碼擴充功能新增至現有的虛擬機器。 如需詳細資訊，請參閱 [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension)。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>其他範例

### <a name="using-multiple-scripts"></a>使用多個指令碼

在此範例中，您有三個可用來建置伺服器的指令碼。 **commandToExecute** 會呼叫第一個指令碼，然後您就可以選擇其他指令碼的呼叫方式。 例如，您可以擁有負責控制執行的主要指令碼，其中包含正確的錯誤處理、記錄和狀態管理。 指令碼會下載到本機電腦來執行。 例如，在 `1_Add_Tools.ps1` 中，您會藉由將 `.\2_Add_Features.ps1` 新增至指令碼來呼叫 `2_Add_Features.ps1`，並針對您在 `$settings` 中定義的其他指令碼重複此程序。

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>從本機共用執行指令碼

在此範例中，您可以使用本機 SMB 伺服器作為指令碼的位置。 這麼做的話，就不需要提供任何其他設定，但 **commandToExecute** 除外。

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>如何使用 CLI 多次執行自訂指令碼

如果您想要執行自訂指令碼延伸模組多次，您只有在下列情況下才能執行此動作：

* 擴充功能的 **Name** 參數等同於先前的擴充功能部署。
* 更新設定，否則不會重新執行命令。 您可以將動態屬性加入命令，例如時間戳記。

或者，您也可以將 [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) 屬性設定為 **true**。

### <a name="using-invoke-webrequest"></a>使用 Invoke-WebRequest

如果您要在指令碼中使用 [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest)，就必須指定 `-UseBasicParsing` 參數，否則會在檢查詳細狀態時收到下列錯誤：

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

若要在擴展集上部署自訂指令碼擴充功能，請參閱 [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>傳統 VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

若要在傳統 VM 上部署自訂指令碼擴充功能，您可以使用 Azure 入口網站或傳統 Azure PowerShell Cmdlet。

### <a name="azure-portal"></a>Azure 入口網站

瀏覽至您的傳統 VM 資源。 選取 [設定] 底下的 [擴充功能]。

按一下 [+ 新增]，然後在資源清單中選擇 [自訂指令碼擴充功能]。

在 [安裝擴充功能] 頁面上選取本機 PowerShell 檔案，並填寫任何引數，然後按一下 [確定]。

### <a name="powershell"></a>PowerShell

您可以使用 [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) Cmdlet，將自訂指令碼擴充功能新增至現有虛擬機器。

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell 模組，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請執行下列命令：

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

擴充功能輸出會記錄至目標虛擬機器上下列資料夾中的檔案。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定檔案會下載至目標虛擬機器上的下列資料夾之中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

其中 `<n>` 是十進位整數，可能會在擴充功能的執行之間變更。  `1.*` 值符合擴充功能目前實際的 `typeHandlerVersion` 值。  例如，實際的目錄可能是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`。  

執行 `commandToExecute` 命令時，擴充功能會將此目錄 (例如 `...\Downloads\2`) 設定為目前的工作目錄。 此程序可讓您使用相對路徑找出透過 `fileURIs` 屬性下載的檔案位置。 如需範例，請參閱下表。

由於絕對下載路徑會隨時間而改變，最好盡可能在 `commandToExecute` 字串中選擇相對的指令碼/檔案路徑。 例如：

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

會為透過 `fileUris` 屬性清單下載的檔案保留第一個 URI 區段後的路徑資訊。  如下表所示，下載的檔案會對應到下載的子目錄，以反映 `fileUris` 值結構。  

#### <a name="examples-of-downloaded-files"></a>下載檔案的範例

| fileUris 中的 URI | 相對下載位置 | 絕對下載位置 <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> 如同上述，絕對目錄路徑會隨 VM 生命週期而變更，但不會在 CustomScript 擴充的單次執行期間。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 您也可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
