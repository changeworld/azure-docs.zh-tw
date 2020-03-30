---
title: 創建 Azure 映射產生器範本（預覽）
description: 瞭解如何創建要與 Azure 映射產生器一起使用的範本。
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246784"
---
# <a name="preview-create-an-azure-image-builder-template"></a>預覽：創建 Azure 映射產生器範本 

Azure 映射產生器使用 .json 檔將資訊傳遞到映射產生器服務。 在本文中，我們將介紹 json 檔的各個部分，以便您可以構建自己的檔。 要查看完整的 .json 檔的示例，請參閱[Azure 映射產生器 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)。

這是基本的範本格式：

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>類型和 API 版本

`type`是 資源類型，必須為`"Microsoft.VirtualMachineImages/imageTemplates"`。 隨著`apiVersion`API 的變化，將隨時間而變化，但應為`"2019-05-01-preview"`預覽。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

位置是將創建自訂映射的區域。 對於圖像產生器預覽，支援以下區域：

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
預設情況下，映射產生器將使用"Standard_D1_v2"生成 VM，您可以重寫這一點，例如，如果要為 GPU VM 自訂映射，則需要 GPU VM 大小。 這是選擇性的。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

預設情況下，圖像產生器不會更改圖像的大小，它將使用源圖像中的大小。 您可以增加 OS 磁片（Win 和 Linux）的大小，這是可選的，值為 0 表示保留與源映射相同的大小。 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
如果不指定任何 VNET 屬性，則映射產生器將創建自己的 VNET、公共 IP 和 NSG。 公共 IP 用於服務與生成 VM 通信，但是，如果您不希望公共 IP 或希望映射產生器有權訪問現有的 VNET 資源，例如佈建服務器（DSC、Chef、Puppet、Ansible）、檔共用等。，然後您可以指定 VNET。 有關詳細資訊，請查看[網路文檔](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)，這是可選的。

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tags

這些是鍵/值對，您可以為生成的圖像指定。

## <a name="depends-on-optional"></a>取決於（可選）

此可選部分可用於確保依賴項在繼續之前完成。 

```json
    "dependsOn": [],
```

有關詳細資訊，請參閱[定義資源依賴項](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)。

## <a name="identity"></a>身分識別
預設情況下，映射產生器支援使用腳本或從多個位置（如 GitHub 和 Azure 存儲）複製檔。 要使用這些，它們必須是可公開訪問的。

您還可以使用由您定義的 Azure 使用者分配的託管標識，允許映射產生器訪問 Azure 存儲，只要該標識已被授予 Azure 存儲帳戶上的最低"存儲 Blob 資料讀取器"。 這意味著您不需要使存儲 blob 在外部訪問，或設置 SAS 權杖。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

有關完整示例，請參閱[使用 Azure 使用者分配的託管標識訪問 Azure 存儲 中的檔](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

圖像產生器對使用者分配標識的支援：• 僅支援單個標識 • 不支援自訂功能變數名稱

要瞭解更多資訊，請參閱[Azure 資源的託管標識是什麼？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
有關部署此功能的詳細資訊，請參閱使用 Azure [CLI 在 Azure VM 上配置 Azure 資源的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)。

## <a name="properties-source"></a>屬性：源

該`source`部分包含有關圖像產生器將使用的源映射的資訊。

API 需要一個"SourceType"來定義映射生成源，目前有三種類型：
- PlatformImage - 指示源映射是應用商店映射。
- 託管映射 - 從常規託管映射開始時，請使用此選項。
- 共用圖像版本 - 當您在共用圖像庫中使用圖像版本作為源時，將使用此版本。

### <a name="iso-source"></a>ISO 源
我們正在從映射產生器中棄用此功能，因為現在有[RHEL 自帶訂閱映射](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)，請查看下面的時間表：
    * 2020 年 3 月 31 日 - 資源供應商現在將接受具有 RHEL ISO 源的圖像範本。
    * 2020 年 4 月 30 日 - 包含 RHEL ISO 源的圖像範本將不再處理。

### <a name="platformimage-source"></a>平臺圖像源 
Azure 映射產生器支援 Windows 伺服器和用戶端以及 Linux Azure 應用商店映射，請參閱[此處](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support)查看完整清單。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


此處的屬性與使用 AZ CLI 創建 VM 的屬性相同，請運行以下屬性以獲取屬性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

您可以在版本中使用"最新"，在進行映射生成時評估版本，而不是提交範本時。 如果將此功能與共享圖像庫目標一起使用，則可以避免重新提交範本，並每隔一段時間重新運行圖像生成，以便從最新圖像重新創建圖像。

### <a name="managedimage-source"></a>託管圖像源

將源映射集為通用 VHD 或 VM 的現有託管映射。 源託管映射必須為受支援的作業系統，並且與 Azure 映射產生器範本位於同一區域。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`應為託管映射的 ResourceID。 用於`az image list`列出可用圖像。


### <a name="sharedimageversion-source"></a>共用圖像版本源
在共用映射庫中設置現有映射版本的源映射。 映射版本必須為受支援的作業系統，並且映射必須複製到與 Azure 映射產生器範本相同的區域。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`應為映射版本的 ResourceId。 使用[az sig 圖像版本清單](/cli/azure/sig/image-version#az-sig-image-version-list)列出圖像版本。

## <a name="properties-buildtimeoutinminutes"></a>屬性：生成時間分鐘

預設情況下，映射產生器將運行 240 分鐘。 之後，無論映射生成是否完成，它將超時並停止。 如果達到超時，您將看到類似于此的錯誤：

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

如果不指定 buildTimeoutIn分鐘值，或將其設置為 0，則將使用預設值。 您可以增加或減小該值，最高可達 960 分鐘（16 小時）。 對於 Windows，我們不建議將此設置在 60 分鐘以下。 如果您發現您正在達到超時，請查看[日誌](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)，以查看自訂步驟是否正在等待使用者輸入之類的內容。 

如果您發現需要更多時間完成自訂，則設置到您認為需要的內容，並稍加開銷。 但是，不要設置過高，因為您可能需要等待超時，才能看到錯誤。 


## <a name="properties-customize"></a>屬性：自訂

圖像產生器支援多個"自訂器"。 自訂程式是用於自訂映射的函數，例如運行腳本或重新開機伺服器。 

使用`customize`時 ： 
- 您可以使用多個自訂程式，但它們必須具有唯`name`一的 。
- 自訂程式按範本中指定的循序執行。
- 如果一個自訂程式失敗，則整個自訂群組件將失敗並報告錯誤。
- 強烈建議您在範本中使用腳本之前徹底測試腳本。 在您自己的 VM 上調試腳本將更容易。
- 不要將敏感性資料放入腳本中。 
- 腳本位置需要公開訪問，除非您正在使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

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

 
自訂部分是一個陣列。 Azure 映射產生器將按順序運行自訂程式。 任何自訂程式中的任何失敗都將使生成過程失敗。 
 
 
### <a name="shell-customizer"></a>外殼自訂程式

shell 自訂程式支援運行 shell 腳本，這些腳本必須是可公開訪問的，以便 IB 訪問它們。

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

作業系統支援：Linux 
 
自訂屬性：

- **類型**= 外殼 
- **名稱**- 用於跟蹤自訂的名稱 
- **腳本 Uri** - URI 到檔的位置 
- **內聯**- shell 命令陣列，用逗號分隔。
- **sha256Checksum** - 檔沙256校驗和的值，您從本地生成此，然後圖像產生器將檢查和驗證。
    * 要生成 sha256Checksum，請使用 Mac/Linux 上運行的終端：`sha256sum <fileName>`


要使用超級使用者許可權運行命令，必須用`sudo`預固定命令。

> [!NOTE]
> 使用 RHEL ISO 源運行 shell 自訂程式時，您需要確保在進行任何自訂之前，第一個自訂外殼處理與紅帽授權伺服器註冊。 自訂完成後，腳本應取消向授權伺服器註冊。

### <a name="windows-restart-customizer"></a>Windows 重新開機自訂程式 
重新開機自訂程式允許您重新開機 Windows VM 並等待它重新連線，這允許您安裝需要重新開機的軟體。  

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

作業系統支援：視窗
 
自訂屬性：
- **類型**： 視窗重新開機
- **重新開機命令**- 命令執行重新開機（可選）。 預設值為 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **重新開機檢查命令**= 命令，以檢查重新開機是否成功（可選）。 
- **重新開機超時**- 重新開機超時指定為大小和單位的字串。 例如，（5`5m`分鐘）或`2h`（2 小時）。 預設值為："5 米"

### <a name="linux-restart"></a>Linux 重啟  
沒有 Linux 重新開機自訂程式，但是，如果您正在安裝驅動程式或需要重新開機的元件，則可以使用 Shell 自訂程式安裝驅動程式並調用重新開機，則生成 VM 有 20 分鐘的 SSH 超時。

### <a name="powershell-customizer"></a>PowerShell 定制器 
shell 自訂程式支援運行 PowerShell 腳本和內聯命令，IB 必須公開訪問這些腳本才能訪問它們。

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

作業系統支援：Windows 和 Linux

自訂屬性：

- **類型**= 電源外殼。
- **腳本 Uri** - URI 到 PowerShell 指令檔的位置。 
- **內聯**= 要運行的內聯命令，用逗號分隔。
- **有效結束代碼**– 可從腳本/內聯命令返回的可選有效代碼，這將避免腳本/內聯命令的報告失敗。
- **運行提升**– 可選、布林，支援使用提升許可權運行命令和腳本。
- **sha256Checksum** - 檔沙256校驗和的值，您從本地生成此，然後圖像產生器將檢查和驗證。
    * 要生成 sha256Checksum，請使用 Windows[獲取雜湊](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)上的 PowerShell


### <a name="file-customizer"></a>檔自訂器

檔自訂程式允許映射產生器從 GitHub 或 Azure 存儲下載檔案。 如果映射生成管道依賴于生成專案，則可以將檔自訂程式設置為從生成共用下載，並將專案移動到映射中。  

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

作業系統支援：Linux 和 Windows 

檔自訂器屬性：

- **sourceUri** - 可訪問的存儲終結點，可以是 GitHub 或 Azure 存儲。 您只能下載一個檔，而不能下載整個目錄。 如果需要下載目錄，請使用壓縮檔，然後使用 Shell 或 PowerShell 自訂程式解壓縮它。 
- **目標**= 這是完整的目標路徑和檔案名。 任何引用的路徑和子目錄都必須存在，使用命令程式或 PowerShell 自訂程式提前設置這些路徑和子目錄。 您可以使用腳本自訂程式創建路徑。 

Windows 目錄和 Linux 路徑支援此功能，但有一些差異： 
- Linux作業系統 – 映射產生器可以寫入的唯一路徑是 /tmp。
- Windows = 沒有路徑限制，但路徑必須存在。
 
 
如果嘗試下載檔案或將其放入指定的目錄中時出錯，則自訂步驟將失敗，這將在自訂.log 中。

> [!NOTE]
> 檔自訂程式僅適用于小型檔下載，< 20MB。 對於較大的檔下載，請使用腳本或內聯命令，使用代碼下載檔案，如 Linux`wget`或`curl`，Windows。 `Invoke-WebRequest`

檔自訂程式中的檔可以使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)從 Azure 存儲下載。

### <a name="windows-update-customizer"></a>視窗更新自訂程式
此自訂程式基於社區[Windows 更新預配](https://packer.io/docs/provisioners/community-supported.html)器（由 Packer 社區維護的開源專案）構建。 Microsoft 使用映射產生器服務測試和驗證預配程式，並將支援調查其問題，並致力於解決問題，但 Microsoft 並不正式支援開源專案。 有關 Windows 更新預配程式的詳細文檔和説明，請參閱專案存儲庫。
 
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
作業系統支援：視窗

自訂屬性：
- **類型**= Windows 更新。
- **搜索標準**- 可選，定義安裝的更新類型（推薦、重要等）、"僅流覽"=0 和"已安裝"=0（推薦）為預設值。
- **篩選器**= 可選，允許您指定篩選器以包括或排除更新。
- **更新限制**= 可選，定義可以安裝多少更新，預設 1000。
 
 

### <a name="generalize"></a>一般化 
預設情況下，Azure 映射產生器還將在每個映射自訂階段結束時運行"取消預配"代碼，以"通用"映射。 通用化是設置映射以便重用映射以創建多個 VM 的過程。 對於 Windows VM，Azure 映射產生器使用 Sysprep。 對於 Linux，Azure 映射產生器運行"waagent-de預配"。 

命令映射產生器使用者進行一化和通用化可能並不適合每種情況，因此 Azure 映射產生器將允許您根據需要自訂此命令。 

如果要遷移現有自訂項，並且使用不同的 Sysprep/waagent 命令，則可以使用映射產生器泛型命令，如果 VM 創建失敗，請使用您自己的 Sysprep 或 waagent 命令。

如果 Azure 映射產生器成功創建 Windows 自訂映射，並且從該映射創建 VM，則發現 VM 創建失敗或未成功完成，則需要查看 Windows Server Sysprep 文檔，或使用Windows 伺服器 Sysprep 客戶服務支援小組，他們可以對正確的 Sysprep 使用方式進行故障排除並提供建議。


#### <a name="default-sysprep-command"></a>預設 Sysprep 命令
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>預設 Linux 取消預配命令

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>重寫命令
要重寫這些命令，請使用 PowerShell 或 Shell 腳本預配器創建具有確切檔案名的命令檔，並將它們放在正確的目錄中：

* 視窗：c：\取消預配腳本.ps1
* Linux： /tmp/取消預配腳本.sh

映射產生器將讀取這些命令，這些命令被寫入 AIB 日誌，"自訂.log"。 有關如何收集日誌[的疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)。
 
## <a name="properties-distribute"></a>屬性：分發

Azure 映射產生器支援三個分發目標： 

- **託管映射**- 託管映射。
- **共用圖像**- 共用圖像庫。
- **VHD** - 存儲帳戶中的 VHD。

您可以將映射分發到同一配置中的兩個目標型別，請參閱[示例](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)。

由於可以有多個目標要分發到 ，因此 Image Builder 會維護可通過查詢 訪問的每個分發目標的狀態`runOutputName`。  `runOutputName`是可以查詢發佈後分發的物件，以獲取有關該分佈的資訊。 例如，您可以查詢 VHD 的位置、映射版本複製到的區域或創建 SIG 圖像版本的區域。 這是每個分發目標的屬性。 必須`runOutputName`對每個分發目標是唯一的。 下面是一個示例，這是查詢共用圖像庫分發：

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
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

### <a name="distribute-managedimage"></a>分發：託管圖像

映射輸出將是託管映射資源。

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
 
分發屬性：
- **類型**= 託管圖像 
- **映射 Id** = 靶心圖表像的資源識別碼，預期格式：/\<訂閱/訂閱 id>/資源組/\<目標資源組名稱>/提供程式/Microsoft.\<計算/圖像/圖像名稱>
- **位置**- 託管映射的位置。  
- **運行輸出名稱**= 標識分佈的唯一名稱。  
- **專案標記**- 可選使用者指定鍵值對標記。
 
 
> [!NOTE]
> 目標資源組必須存在。
> 如果希望將映射分發到其他區域，它將增加部署時間。 

### <a name="distribute-sharedimage"></a>分發：共用圖像 
Azure 共用映射庫是一種新的映射管理服務，允許管理映射區域複製、版本控制和共用自訂映射。 Azure 映射產生器支援使用此服務進行分發，因此可以將映射分發到共用映射庫支援的區域。 
 
共用圖像庫由： 
 
- 庫 - 用於多個共用圖像的容器。 庫部署在一個區域中。
- 圖像定義 - 圖像的概念分組。 
- 映射版本 - 這是用於部署 VM 或縮放集的圖像類型。 圖像版本可以複製到需要部署 VM 的其他地區。
 
在可以分發到圖像庫之前，必須創建庫和圖像定義，請參閱[共用圖像](shared-images.md)。 

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

為共用圖像庫分發屬性：

- **類型**- 共用圖像  
- **庫 ImageId** = 共用圖像庫的 ID。 格式為\<：/訂閱/訂閱 id>/資源組\</資源組名稱>/供應商/Microsoft.計算/庫圖\</共用 ImageGallery 名稱>/圖像/\<圖像庫名稱>。
- **運行輸出名稱**= 標識分佈的唯一名稱。  
- **專案標記**- 可選使用者指定鍵值對標記。
- **複製區域**- 用於複製的區域陣列。 其中一個區域必須是部署庫的區域。
 
> [!NOTE]
> 可以在其他區域中使用 Azure 映射產生器到庫，但 Azure 映射產生器服務將需要在資料中心之間傳輸映射，這需要更長的時間。 圖像產生器將根據單調整數自動對圖像進行版本控制，您當前無法指定它。 

### <a name="distribute-vhd"></a>分發： VHD  
您可以輸出到 VHD。 然後，可以複製 VHD，並使用它發佈到 Azure 市場廣場，或使用 Azure 堆疊。  

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
 
作業系統支援：Windows 和 Linux

分發 VHD 參數：

- **類型**- VHD。
- **運行輸出名稱**= 標識分佈的唯一名稱。  
- **標記**- 可選使用者指定鍵值對標記。
 
Azure 映射產生器不允許使用者指定存儲帳戶位置，但您可以查詢 的狀態`runOutputs`以獲取該位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 創建 VHD 後，請儘快將其複製到其他位置。 VHD 存儲在將映射範本提交到 Azure 映射產生器服務時創建的臨時資源組中的存儲帳戶中。 如果刪除圖像範本，則將丟失 VHD。 
 
## <a name="next-steps"></a>後續步驟

[Azure 映射產生器 GitHub](https://github.com/danielsollondon/azvmimagebuilder)中對於不同方案的示例 .json 檔。
 
