---
title: 適用於 Windows 的 Azure 自訂文稿延伸
description: 使用「自訂指令碼擴充功能」來自動執行 Windows VM 組態工作
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 2c7cad2dfdcd55073a1cf09d79e5223b666ced5f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478161"
---
# <a name="custom-script-extension-for-windows"></a>Windows 的自訂指令碼延伸模組

「自訂指令碼擴充功能」會在 Azure 虛擬機器上下載並執行指令碼。 此擴展可用於部署後配置、軟體安裝或任何其他配置或管理任務。 您可以從 Azure 儲存體或 GitHub 下載指令碼，或是在擴充功能執行階段將指令碼提供給 Azure 入口網站。 自定義文稿擴展與 Azure 資源管理器範本整合,可以使用 Azure CLI、PowerShell、Azure 門戶或 Azure 虛擬機器 REST API 運行。

本文件詳細說明如何透過 Azure PowerShell 模組、Azure Resource Manager 範本使用自訂指令碼擴充功能，同時也詳細說明 Windows 系統上的疑難排解步驟。

## <a name="prerequisites"></a>Prerequisites

> [!NOTE]  
> 請勿使用自訂指令碼擴充功能以相同的 VM 執行 Update-AzVM 作為其參數，因為它會等候其本身。  

### <a name="operating-system"></a>作業系統

Windows 的自訂文稿擴展將在受擴展支援延伸延伸作業系統上執行,有關詳細資訊,請參閱此[Azure 延伸支援的作業系統](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)。

### <a name="script-location"></a>指令碼位置

可以將擴充設定為使用 Azure Blob 儲存認證存取 Azure Blob 儲存。 只要 VM 可以路由到該端點(如 GitHub 或內部檔伺服器),腳本位置可以位於任何位置。

### <a name="internet-connectivity"></a>網際網路連線

如果需要從外部下載腳本(如從 GitHub 或 Azure 存儲)下載腳本,則需要打開其他防火牆和網路安全組埠。 例如,如果文稿位於 Azure 儲存中,則可以使用 Azure NSG 服務標記進行[儲存](../../virtual-network/security-overview.md#service-tags)。

如果腳本位於本地伺服器上,則可能還需要打開其他防火牆和網路安全組埠。

### <a name="tips-and-tricks"></a>秘訣與技巧

* 此擴展的最高失敗率是腳本中的語法錯誤,測試腳本運行時沒有錯誤,並且還會在腳本中輸入其他日誌記錄,以便更輕鬆地查找失敗的位置。
* 編寫冪等的腳本。 這可確保如果它們再次意外運行,不會導致系統更改。
* 請確定在指令碼執行時，不需要使用者輸入。
* 指令碼可執行的時間為 90 分鐘。若超過這個時間，將會導致擴充功能佈建失敗。
* 不要在指令碼中放置重新開機，此動作會導致正在安裝的其他擴充功能發生問題。 若發佈重新開機，擴充功能將不會在重新啟動之後繼續執行。
* 如果文本會導致重新啟動,則安裝應用程式並運行文稿,則可以使用 Windows 計畫任務計畫重新啟動,或使用 DSC、Chef 或 Puppet 擴展等工具。
* 擴充功能只會執行指令碼一次。如果您想要在每次開機時執行指令碼，則必須使用擴充功能建立 Windows 排程工作。
* 如果您想要排程指令碼的執行時間，則應該使用擴充功能來建立 Windows 排程工作。
* 當指令碼正在執行時，只能從 Azure 入口網站或 CLI 看到「正在轉換」擴充功能狀態。 如果您需要執行中指令碼更頻繁的狀態更新，便必須建立自己的解決方案。
* 自訂文稿副檔名不支援代理伺服器,但您可以使用支援文稿中的代理伺服器的檔案傳輸工具,如*Curl*
* 請留意指令碼或命令所依賴的非預設目錄位置是否具備處理此情形的邏輯。
* 自訂文稿延伸會在本地端系統帳戶下執行

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
> 管理員**名稱**或儲存帳戶金鑰屬性結合使用

> [!NOTE]
> 一個時間點只能在 VM 上安裝一個版本的擴展,在同一資源管理器範本中為同一 VM 指定自訂文本兩次將失敗。

> [!NOTE]
> 我們可以在虛擬機資源中使用此架構,也可以用作獨立資源。 如果此擴展在 ARM 範本中用作獨立資源,則資源的名稱必須在此格式為「虛擬機名稱/擴展名稱」。

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
| 託管身份(例如) | [ ] 或 " 客戶端 Id":" 31b403a-c364-4240-a7ff-d85fb6cd7232" * 或 { "objectId":" 12dd289c-0583-46e3-b9b4-115d5c19ef4b" | | json 物件 |

>[!NOTE]
>這些屬性名稱會區分大小寫。 為了避免發生部署問題，請使用如下所示的名稱。

#### <a name="property-value-details"></a>屬性值詳細資料

* `commandToExecute`：(**必要**，字串) 要執行的進入點指令碼。 如果您的命令包含機密資料 (例如密碼)，或您的 fileUris 是敏感資料，請改用此欄位。
* `fileUris`：(選擇性，字串陣列) 要下載之檔案的 URL。
* `timestamp` (選擇性，32 位元整數) 只有在透過變更此欄位的值來觸發指令碼的重新執行時，才需使用此欄位。  任何整數值都是可接受的；只要與先前的值不同即可。
* `storageAccountName`：(選用，字串) 儲存體帳戶的名稱。 如果您指定儲存體證明資料，則所有 `fileUris` 都必須是 Azure Blob 的 URL。
* `storageAccountKey`：(選用，字串) 儲存體帳戶的存取金鑰
* `managedIdentity`:(可選,json 物件)用於下載檔案的[託管識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
  * `clientId`:(可選,字串)託管識別的客戶端 ID
  * `objectId`:(選擇,字串管理員管理員 ID

下列值可以在公開或受保護的設定中設定，擴充功能將會拒絕任何同時在公開和受保護的設定中設定下列值的組態。

* `commandToExecute`

使用公共設置可能可用於調試,但建議您使用受保護的設置。

公開設定會以純文字的格式，傳送到執行指令碼所在的 VM。  受保護的設定會使用只有 Azure 和 VM 知道的金鑰來加密。 這些設置在發送時保存到 VM,也就是說,如果這些設置已加密,則它們將保存在 VM 上。 用來解密加密值的憑證會儲存在 VM 上，並在執行階段用於解密設定 (如有必要)。

####  <a name="property-managedidentity"></a>屬性:託管識別
> [!NOTE]
> 此屬性**必須**僅在受保護設置中指定。

CustomScript(版本 1.10 以後)支援從 fileUris 設定中提供的網址下載檔案的[託管識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 它允許 CustomScript 訪問 Azure 儲存專用 Blob 或容器,而無需使用者傳遞 SAS 令牌或存儲帳戶金鑰等機密。

要使用此功能,使用者必須向預期執行 CustomScript 的 VM 或 VMSS 添加[系統配置](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)或[使用者配置的](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity)識別,並[授予對 Azure 儲存容器或 blob 的託管識別存取權限](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)。

要在目標 VM/VMSS 上使用系統分配的標識,將「託管標識」欄位設置為空 json 物件。 

> 範例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

要在目標 VM/VMSS 上使用使用者分配的標識,請使用用戶端 ID 或託管標識的物件 ID 配置「託管標識」欄位。

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
> 管理員**名稱**或儲存帳戶金鑰屬性結合使用

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 JSON 架構(上一節中詳細介紹)可用於 Azure 資源管理器範本,用於在部署期間運行自定義腳本擴展。 下列範例會說明如何使用自訂指令碼擴充功能：

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

### <a name="using-multiple-scripts"></a>使用多個文稿

這個功能中,您有三個文本用於建置伺服器。 **命令ToExecute**調用第一個腳本,然後你有關於如何調用其他腳本的選項。 例如,您可以有一個主腳本來控制執行,並具有正確的錯誤處理、日誌記錄和狀態管理。 文本將下載到本地電腦以進行運行。 例如,在`1_Add_Tools.ps1`中,您可以`2_Add_Features.ps1`通過添加`.\2_Add_Features.ps1`到 腳本來調用,`$settings`並在 中定義的其他腳本重複此過程。

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

在此範例中,您可能希望將本地 SMB 伺服器用於文稿位置。 以執行此操作,您不需要提供任何其他設定,但**命令ToExecute**。

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

* 擴展**名稱**參數與擴展的上一個部署相同。
* 更新配置,否則不會重新執行該命令。 您可以將動態屬性加入命令，例如時間戳記。

或者,您可以將[ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag)屬性設定為**true**。

### <a name="using-invoke-webrequest"></a>使用呼叫網頁要求

如果在文稿中使用[Invoke-WebRequest,](/powershell/module/microsoft.powershell.utility/invoke-webrequest)則必須`-UseBasicParsing`指定參數 ,否則在檢查詳細狀態時將收到以下錯誤:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

要在規模集中部署自訂文稿擴展,請參閱添加[AzVms 擴展](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>傳統 VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

要在經典 VM 上部署自訂文稿擴展,可以使用 Azure 門戶或經典 Azure PowerShell cmdlet。

### <a name="azure-portal"></a>Azure 入口網站

導航到經典 VM 資源。 選擇 **「 設定」****選單**。

按下 **+ 新增**, 並在資源清單中選擇**自訂文稿延伸**。

在 **「安裝副檔名**」頁上,選擇本地 PowerShell 檔,並填寫任何參數,然後按下「**確定**」。

### <a name="powershell"></a>PowerShell

使用[集-AzureVM 自定義文稿擴展](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension)cmdlet 可用於將自定義文本擴展添加到現有虛擬機器。

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

擴展輸出記錄到目標虛擬機器上的以下資料夾下的檔案。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定的檔案將下載到目標虛擬機器上的以下資料夾中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

其中 `<n>` 是十進位整數，可能會在擴充功能的執行之間變更。  `1.*` 值符合擴充功能目前實際的 `typeHandlerVersion` 值。  例如，實際的目錄可能是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`。  

執行 `commandToExecute` 命令時，擴充功能會將此目錄 (例如 `...\Downloads\2`) 設定為目前的工作目錄。 此程序可讓您使用相對路徑找出透過 `fileURIs` 屬性下載的檔案位置。 如需範例，請參閱下表。

由於絕對下載路徑會隨時間而改變，最好盡可能在 `commandToExecute` 字串中選擇相對的指令碼/檔案路徑。 例如：

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

保存第一個 URI 段後`fileUris`,通過 屬性清單下載的檔的路徑資訊。  如下表所示，下載的檔案會對應到下載的子目錄，以反映 `fileUris` 值結構。  

#### <a name="examples-of-downloaded-files"></a>下載檔案的範例

| fileUris 中的 URI | 相對下載位置 | 絕對下載位置<sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup>絕對目錄路徑在 VM 的生存期內更改,但在自訂文本擴展的單個執行中更改。

### <a name="support"></a>支援

如果本文中的任何一點都需要更多説明,則可以在[MSDN Azure 和堆疊溢出論壇](https://azure.microsoft.com/support/forums/)上聯繫 Azure 專家。 您也可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇「獲取支援」。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
