---
title: 建立 Azure Image Builder 範本 (預覽)
description: 了解如何建立範本以搭配 Azure Image Builder 使用。
author: danielsollondon
ms.author: danis
ms.date: 07/09/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: d48153fa747ed9757eb8467eaf1d7c17cde3630e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085583"
---
# <a name="preview-create-an-azure-image-builder-template"></a>預覽：建立 Azure Image Builder 範本 

Azure Image Builder 會使用 .json 檔案，將資訊傳遞至 Image Builder 服務。 在本文中，我們將探討 json 檔案的各個區段，以便您建置自行的範本。 若要查看完整的 .json 檔案範例，請參閱 [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)。

這是基本的範本格式：

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>類型和 API 版本

`type` 是資源類型，其必須為 `"Microsoft.VirtualMachineImages/imageTemplates"`。 當 API 變更時，`apiVersion` 會隨著時間變更，但應該是 `"2020-02-14"` 以供預覽。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Location

位置是將要建立自訂映像的區域。 在 Image Builder 預覽版中，支援下列區域：

- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2
- 北歐
- 西歐


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
根據預設，Image Builder 會使用「Standard_D1_v2」建置 VM，您可以覆寫此項，例如，若想要自訂 GPU VM 的映像，則需要 GPU VM 大小。 這是選擇性的。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

根據預設，Image Builder 不會變更映像的大小，其會使用來源映像的大小。 您**只能**增加 OS 磁片（Win 和 Linux）的大小，這是選擇性的，值為0表示保留與來源映射相同的大小。 您無法將 OS 磁片大小減少為小於來源映射的大小。

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
如果您未指定任何 VNET 屬性，則 Image Builder 會建立自己的 VNET、公用 IP 和 NSG。 公用 IP 可供服務與建置 VM 通訊，不過，如果您不希望公用 IP 或 Image Builder 能夠存取現有的 VNET 資源 (例如設定伺服器 (DSC、Chef、Puppet、Ansible)、檔案共用等)，則可指定 VNET。 如需詳細資訊，請檢閱[網路功能文件](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)，這是選擇性動作。

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Tags

以下是您可以為所產生的映像指定的索引鍵/值組。

## <a name="depends-on-optional"></a>取決於 (選擇性)

這個選擇性區段可用來確保相依性已完成，再繼續進行。 

```json
    "dependsOn": [],
```

如需詳細資訊，請參閱[定義資源相依性](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)。

## <a name="identity"></a>身分識別

必要-若要讓映射產生器擁有讀取/寫入映射的許可權，請從 Azure 儲存體讀取腳本，您必須建立具有個別資源許可權的 Azure 使用者指派身分識別。 如需有關「映射產生器」許可權的工作方式和相關步驟的詳細資訊，請參閱[檔](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


影像產生器支援使用者指派的身分識別：
* 僅支援單一身分識別
* 不支援自訂功能變數名稱

若要深入了解，請參閱[什麼是 Azure 資源的受控識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
如需部署此功能的詳細資訊，請參閱[使用 Azure CLI 在 Azure VM 上設定 Azure 資源的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)。

## <a name="properties-source"></a>屬性：source

`source` 區段包含 Image Builder 所要使用的來源映像相關資訊。

API 需要可定義映像建置來源的 'SourceType'，目前有三種類型：
- PlatformImage - 指出來源映像是 Marketplace 映像。
- ManagedImage - 從一般受控映像啟動時，使用此類型。
- SharedImageVersion - 當您使用共用映像庫中的映像版本作為來源時，使用此類型。

> [!NOTE]
> 使用現有的 Windows 自訂映射時，您可以在單一 Windows 映像上執行 Sysprep 命令最多8次，如需詳細資訊，請參閱[Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)檔。

### <a name="platformimage-source"></a>PlatformImage 來源 
Azure Image Builder 支援 Windows Server 和用戶端，以及 Linux Azure Marketplace 映像，請參閱[這裡](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support)以取得完整清單。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


這裡的屬性與用來建立 VM 的屬性相同，使用 AZ CLI 執行下列內容以取得屬性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

您可以使用「最新」版本，系統會在進行映像建置時評估版本，而不是在提交範本時評估。 如果您使用此功能搭配共享映像庫目的地，則可避免重新提交範本，而且定期重新執行映像建置，以便從最新的映像重建您的映像。

#### <a name="support-for-market-place-plan-information"></a>支援市場計畫資訊
您也可以指定方案資訊，例如：
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage 來源

將來源映像設定為一般化 VHD 或 VM 的現有受控映像。 來源受控映像必須屬於受支援的 OS，且位於與 Azure Image Builder 範本相同的區域中。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` 應該是受控映像的 ResourceId。 使用 `az image list` 列出可用的映像。


### <a name="sharedimageversion-source"></a>SharedImageVersion 來源
將來源映像設定為共用映像庫中的現有映像版本。 映像版本必須屬於受支援的 OS，且必須將映像複寫到與 Azure Image Builder 範本相同的區域。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` 應該是映像版本的 ResourceId。 使用 [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) 來列出映像版本。


## <a name="properties-buildtimeoutinminutes"></a>屬性：buildTimeoutInMinutes

根據預設，Image Builder 將會持續執行 240 分鐘。 在那之後，不論映像建置是否完成，都會逾時並停止。 如果達到逾時，您會看到類似下面的錯誤：

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

如果您未指定 buildTimeoutInMinutes 值，或將其設定為 0，則會使用預設值。 您可以增加或減少此值，最多可達 960 分鐘 (16 小時)。 對於 Windows，我們不建議將此值設為低於 60 分鐘。 如果您發現即將達到逾時，請檢閱[記錄](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)，查看自訂步驟是否正在等待使用者輸入之類的內容。 

如果您發現需要更多時間才能完成自訂，請將此值設為您所需的值，但有一些額外負荷。 但請勿將其設得太高，因為您可能必須等候到逾時，才能看到錯誤。 


## <a name="properties-customize"></a>屬性：customize

Image Builder 支援多個「自訂工具」。 自訂工具是用來自訂映像的函式，例如執行指令碼或重新啟動伺服器。 

使用 `customize` 時： 
- 您可以使用多個自訂工具，但必須有唯一的 `name`。
- 自訂工具會依照範本中指定的順序執行。
- 如果某個自訂工具失敗，則整個自訂元件將會失敗並回報錯誤。
- 強烈建議您先徹底測試指令碼，再於範本中使用。 在您自己的 VM 上進行指令碼偵錯會變得更容易。
- 請勿將敏感性資料放入指令碼中。 
- 除非您使用 [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)，否則指令碼位置必須可公開存取。

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
customize 區段是一個陣列。 Azure Image Builder 將會循序執行自訂工具。 任何自訂工具中的任何失敗都將導致建置程序失敗。 

> [!NOTE]
> 可以在影像範本定義中查看內嵌命令，並在協助支援案例時 Microsoft 支援服務。 如果您有機密資訊，則應該將它移到 Azure 儲存體中的腳本，其中的存取需要驗證。
 
### <a name="shell-customizer"></a>Shell 自訂工具

Shell 自訂工具支援執行 Shell 指令碼，但這些指令碼必須可公開存取，IB 才能加以存取。

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS 支援：Linux 
 
Customize 屬性：

- **type** – Shell 
- **name** - 用於追蹤自訂的名稱 
- **scriptUri** - 檔案位置的 URI 
- **inline** - Shell 命令的陣列 (以逗號分隔)。
- **sha256Checksum** - 檔案的 sha256 總和檢查碼值，您會在本機產生此值，然後 Image Builder 會進行總和檢查和驗證。
    * 若要產生 sha256Checksum，請使用 Mac/Linux 上的終端機執行：`sha256sum <fileName>`


對於要以超級使用者權限執行的命令，必須在其前面加上 `sudo`。

> [!NOTE]
> 內嵌命令會儲存為映射範本定義的一部分，您可以在傾印映射定義時看到這些專案，而且在進行疑難排解時，Microsoft 支援服務也會看到這些事件。 如果您有敏感性的命令或值，強烈建議將它們移到腳本中，然後使用使用者身分識別向 Azure 儲存體進行驗證。

### <a name="windows-restart-customizer"></a>Windows 重新啟動自訂工具 
重新啟動自訂工具可讓您重新啟動 Windows VM，並等候其重新上線，這可讓您安裝需要重新啟動的軟體。  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS 支援：Windows
 
Customize 屬性：
- **類型**：WindowsRestart
- **restartCommand** - 用於執行重新啟動的命令 (選擇性)。 預設值為 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **restartCheckCommand** – 用於檢查重新啟動是否成功的命令 (選擇性)。 
- **restartTimeout** - 以數量和單位的字串指定的重新啟動逾時。 例如，`5m` (5 分鐘) 或 `2h` (2 小時)。 預設值為：'5m'

### <a name="linux-restart"></a>Linux 重新啟動  
沒有 Linux 重新啟動自訂工具；不過，如果您要安裝驅動程式或需要重新啟動的元件，您可加以安裝，並使用 Shell 自訂工具叫用重新啟動，建置 VM 有 20 分鐘的 SSH 逾時。

### <a name="powershell-customizer"></a>PowerShell 自訂工具 
Shell 自訂工具支援執行 Shell 指令碼，但這些指令碼必須可公開存取，IB 才能加以存取。

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS 支援：Windows 與 Linux

Customize 屬性：

- **type** – PowerShell。
- **scriptUri** - PowerShell 指令碼檔案位置的 URI。 
- **inline** – 要執行的內嵌命令 (以逗號分隔)。
- **validExitCodes** – 選擇性，可從指令碼/內嵌命令傳回的有效程式碼，這會避免指令碼/內嵌命令的回報失敗。
- **runElevated** – 選擇性布林值，支援以較高的權限執行命令和指令碼。
- **sha256Checksum** - 檔案的 sha256 總和檢查碼值，您會在本機產生此值，然後 Image Builder 會進行總和檢查和驗證。
    * 若要產生 sha256Checksum，請在 Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)上使用 PowerShell


### <a name="file-customizer"></a>檔案自訂工具

檔案自訂工具可讓 Image Builder 從 GitHub 或 Azure 儲存體下載檔案。 如果您有依賴組建成品的映像建置管線，則可接著將檔案自訂工具設定為從組建共用下載，然後將成品移至映像。  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

OS 支援：Linux 和 Windows 

檔案自訂工具屬性：

- **sourceUri** - 可存取的儲存端點，這可以是 GitHub 或 Azure 儲存體。 您只能下載一個檔案，而不是整個目錄。 如果您需要下載目錄，請使用壓縮檔案，然後使用 Shell 或 PowerShell 自訂工具將其解壓縮。 
- **destination** – 這是完整目的地路徑和檔案名稱。 任何參考的路徑和子目錄都必須存在，請使用 Shell 或 PowerShell 自訂工具來預先設定這些項目。 您可以使用指令碼自訂工具來建立路徑。 

這受到 Windows 目錄和 Linux 路徑的支援，但有一些差異： 
- Linux OS – Image builder 唯一可寫入的路徑是 /tmp。
- Windows – 沒有路徑限制，但路徑必須存在。
 
 
如果嘗試下載檔案時發生錯誤，或是將檔案放入指定的目錄，則自訂步驟會失敗，而這會記錄在 customization.log 中。

> [!NOTE]
> 檔案自訂工具僅適用於小型檔案下載 (< 20MB)。 對於較大的檔案下載，請使用指令碼或內嵌命令、使用程式碼來下載檔案，例如 Linux `wget` 或 `curl`、Windows `Invoke-WebRequest`。

您可使用 [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)，從 Azure 儲存體下載檔案自訂工具中的檔案。

### <a name="windows-update-customizer"></a>Windows Update 自訂工具
此自訂工具是以適用於 Packer 的[社群 Windows Update 佈建工具](https://packer.io/docs/provisioners/community-supported.html)為建置基礎，這是由 Packer 社群所維護的開放原始碼專案。 Microsoft 會使用 Image Builder 服務來測試及驗證佈建工具，並支援用以調查其問題並解決問題，但 Microsoft 並未正式支援開放原始碼專案。 如需 Windows Update 佈建工具的詳細文件和說明，請參閱專案存放庫。

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Customize 屬性：
- **type**  – WindowsUpdate。
- **searchCriteria** - 選擇性，定義已安裝的更新類型 (建議、重要等)，BrowseOnly=0 和 IsInstalled=0 (建議) 是預設值。
- **filters** – 選擇性，可讓您指定用以包含或排除更新的篩選條件。
- **updateLimit** – 選擇性，定義可安裝的更新數目，預設值為 1000。
 
 

### <a name="generalize"></a>一般化 
根據預設，Azure Image Builder 也會在每個映像自訂階段結束時執行 ‘deprovision’ 程式碼，以將映像「一般化」。 一般化是設定映像的程序，因此可重複用於建立多個 VM。 對於 Windows VM，Azure Image Builder 會使用 Sysprep。 對於 Linux，Azure Image Builder 會執行 ‘waagent -deprovision’。 

Image Builder 使用者用於一般化的命令可能不適合每種情況，因此 Azure Image Builder 會允許您自訂此命令 (如有需要)。 

如果您要遷移現有的自訂，而且使用不同的 Sysprep/waagent 命令，則可使用 Image Builder 一般命令，而如果 VM 建立失敗，則使用自己的 Sysprep 或 waagent 命令。

如果 Azure Image Builder 成功建立 Windows 自訂映像，而您從中建立 VM，然後發現 VM 建立失敗或未順利完成，您就需要檢閱 Windows Server Sysprep 文件，或向 Windows Server Sysprep 客戶服務支援小組提出支援要求，他們可以進行疑難排解並建議正確的 Sysprep 使用方式。


#### <a name="default-sysprep-command"></a>預設 Sysprep 命令
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>預設 Linux 取消佈建命令

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>覆寫命令
若要覆寫命令，請使用 PowerShell 或 Shell 指令碼佈建工具來建立具有確切檔名的命令檔，並將其放入正確的目錄：

* Windows：c:\DeprovisioningScript.ps1
* Linux：/tmp/DeprovisioningScript.sh

Image Builder 將會讀取這些命令，而這些命令會寫出至 AIB 記錄檔 ‘customization.log’。 請參閱[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)以了解如何收集記錄。
 
## <a name="properties-distribute"></a>屬性：distribute

Azure Image Builder 支援三種散發目標： 

- **managedImage** - 受控映像。
- **sharedImage** - 共用映像庫。
- **VHD** - 儲存體帳戶中的 VHD。

您可以使用相同的設定，將影像散佈到這兩個目標型別。

> [!NOTE]
> 預設的 AIB sysprep 命令不包含 "/mode： vm"，不過，當建立將安裝 HyperV 角色的映射時，這可能是必要的。 如果您需要新增此命令引數，則必須覆寫 sysprep 命令。

由於您可有一個以上要散發至的目標，因此 Image Builder 會針對可藉由查詢 `runOutputName` 來存取的每個散發目標維護一個狀態。  `runOutputName` 是您可在散發後查詢的物件，可供取得該散發的相關資訊。 例如，您可以查詢 VHD 的位置，或映像版本所複製到的區域，或已建立的 SIG 映像版本。 這是每個散發目標的屬性。 對每個散發目標而言，`runOutputName` 必須是唯一的。 以下是查詢共用映像庫散發的範例：

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

輸出：
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribute：managedImage

映像輸出會是受控映像資源。

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribute 屬性：
- **type** – managedImage 
- **imageId** –目的地映射的資源識別碼，應為格式：/Subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **location**- 受控映像的位置。  
- **runOutputName**– 用於識別散發的唯一名稱。  
- **artifactTags** - 選擇性使用者指定的索引鍵/值組標記。
 
 
> [!NOTE]
> 目的地資源群組必須存在。
> 如果您想要將映像散發至不同的區域，則會增加部署時間。 

### <a name="distribute-sharedimage"></a>Distribute：sharedImage 
Azure 共用映像庫是新的映像管理服務，可讓您管理映像區域複寫、版本控制及共用自訂映像。 Azure Image Builder 支援透過此服務散發，因此您可將映像散發到共用映像庫所支援的區域。 
 
共用映像庫是由下列各項所組成： 
 
- 資源庫 - 多個共用映像的容器。 一個資源庫會部署在一個區域中。
- 映像定義 - 映像的概念性群組。 
- 映像版本 - 這是用於部署 VM 或擴展集的映像類型。 映像版本可複寫到其他需要部署 VM 的區域。
 
您必須先建立資源庫和映像定義，才能散發至映像庫，請參閱[共用映像](shared-images.md)。 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

共用映像庫的 Distribute 屬性：

- **type** - sharedImage  
- **galleryImageId** –共用映射資源庫的識別碼，可以用兩種格式來指定：
    * 自動版本設定-影像產生器會為您產生單純版本號碼，這適用于當您想要從相同的範本中重建映射時：格式為： `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` 。
    * 明確版本控制-您可以傳入想要讓「映射產生器」使用的版本號碼。 格式為：`/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName**– 用於識別散發的唯一名稱。  
- **artifactTags** - 選擇性使用者指定的索引鍵/值組標記。
- **replicationRegions** - 用於複寫的區域陣列。 其中一個區域必須是映像庫部署所在的區域。 新增區域將會增加組建時間，因為在複寫完成之前，組建不會完成。
- **excludeFromLatest** （選擇性）：這可讓您將所建立的映射版本標示為 SIG 定義中的最新版本，預設值為 ' false '。
- **storageAccountType** （選擇性） AIB 支援針對要建立的映射版本指定這些類型的儲存體：
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> 如果影像範本和參考的 `image definition` 位置不相同，您會看到建立影像的額外時間。 映射產生器目前沒有 `location` 映射版本資源的參數，我們將它從其父系中取得 `image definition` 。 例如，如果映射定義在 westus 中，而您想要將映射版本複寫至 eastus，則會將 blob 複製到 westus，從這種情況下，會建立 westus 中的映射版本資源，然後再複寫到 eastus。 若要避免額外的複寫時間，請確定 `image definition` 和影像範本位於相同的位置。


### <a name="distribute-vhd"></a>Distribute：VHD  
您可以輸出至 VHD。 接著可以複製 VHD，並使用其發佈至 Azure MarketPlace，或搭配 Azure Stack 使用。  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
OS 支援：Windows 和 Linux

散發 VHD 參數：

- **type** - VHD。
- **runOutputName**– 用於識別散發的唯一名稱。  
- **tags** - 選擇性使用者指定的索引鍵/值組標記。
 
Azure Image Builder 不允許使用者指定儲存體帳戶位置，但您可查詢 `runOutputs` 的狀態來取得位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 建立 VHD 後，請儘快將其複製到不同的位置。 VHD 會儲存在映像範本提交至 Azure Image Builder 服務時所建立的暫存資源群組的儲存體帳戶中。 如果您刪除映像範本，則會遺失 VHD。 

## <a name="image-template-operations"></a>影像範本作業

### <a name="starting-an-image-build"></a>啟動映射組建
若要啟動組建，您必須在映射範本資源上叫用 ' Run '，命令的範例 `run` 如下：

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>取消映射組建
如果您執行的是您認為不正確的映射組建、等待使用者輸入，或您覺得無法成功完成，則可以取消組建。

您可以隨時取消組建。 如果發佈階段已啟動，您仍然可以取消，但您將需要清除任何可能未完成的映射。 [取消] 命令不會等待 [取消] 完成，請 `lastrunstatus.runstate` 使用這些狀態[命令](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#get-statuserror-of-the-template-submission-or-template-build-status)來監視是否有取消進度。


命令範例 `cancel` ：

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>後續步驟

[Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder) 中有不同案例的範例 .json 檔案。
