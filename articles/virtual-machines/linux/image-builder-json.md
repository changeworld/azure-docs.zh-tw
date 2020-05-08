---
title: 建立 Azure 映射構建者範本（預覽）
description: 瞭解如何建立範本以與 Azure 映射產生器搭配使用。
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: c13ace67f18b619d5ad86106ecb648db722be9fa
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792440"
---
# <a name="preview-create-an-azure-image-builder-template"></a>預覽：建立 Azure 映射產生器範本 

Azure 映射產生器會使用 json 檔案，將資訊傳遞至映射產生器服務。 在本文中，我們將探討 json 檔案的各個區段，讓您可以自行建立。 若要查看完整的 json 檔案範例，請參閱[Azure 映射](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)產生器 GitHub。

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

`type`是資源類型，必須是`"Microsoft.VirtualMachineImages/imageTemplates"`。 當`apiVersion` API 變更時，會隨著時間改變，但應`"2019-05-01-preview"`為預覽狀態。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

位置是將建立自訂映射的區域。 針對影像產生器預覽，支援下欄區域：

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
根據預設，映射產生器會使用「Standard_D1_v2」組建 VM，您可以覆寫此範例，例如，如果您想要自訂 GPU VM 的映射，您需要 GPU VM 大小。 這是選擇性的。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

根據預設，映射產生器不會變更影像的大小，它會使用來源映射的大小。 您可以增加 OS 磁片（Win 和 Linux）的大小，這是選擇性的，值為0表示保留與來源映射相同的大小。 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
如果您未指定任何 VNET 屬性，則「映射產生器」會建立自己的 VNET、公用 IP 和 NSG。 公用 IP 用於服務與組建 VM 通訊，不過，如果您不想要公用 IP，或想要讓「映射產生器」能夠存取您現有的 VNET 資源（例如設定伺服器（DSC、Chef、Puppet、Ansible）、檔案共用等），則您可以指定 VNET。 如需詳細資訊，請參閱[網路檔](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)，這是選擇性的。

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tags

這些是您可以為產生的影像指定的索引鍵/值組。

## <a name="depends-on-optional"></a>取決於（選擇性）

這個選擇性區段可以用來確保相依性已完成，然後再繼續進行。 

```json
    "dependsOn": [],
```

如需詳細資訊，請參閱[定義資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)相依性。

## <a name="identity"></a>身分識別
根據預設，映射產生器支援使用腳本，或從多個位置複製檔案，例如 GitHub 和 Azure 儲存體。 若要使用這些功能，必須可公開存取。

您也可以使用由您定義的 Azure 使用者指派受控識別，以允許映射產生器存取 Azure 儲存體，只要已在 Azure 儲存體帳戶上授與「儲存體 Blob 資料讀取者」最少的身分識別即可。 這表示您不需要讓儲存體 blob 可供外部存取，或設定 SAS 權杖。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

如需完整範例，請參閱[使用 Azure 使用者指派的受控識別來存取 Azure 儲存體中](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)的檔案。

映射產生器支援使用者指派的身分識別：•僅支援單一身分識別•不支援自訂功能變數名稱

若要深入瞭解，請參閱[什麼是適用于 Azure 資源的受控識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
如需部署這項功能的詳細資訊，請參閱[使用 Azure CLI 在 AZURE VM 上設定 azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)識別。

## <a name="properties-source"></a>屬性：來源

`source`區段包含「映射產生器」將使用之來源影像的相關資訊。

API 需要定義映射組建來源的 ' SourceType '，目前有三種類型：
- PlatformImage-指出來源映射是 Marketplace 映射。
- ManagedImage-從一般受控映射啟動時使用此元件。
- SharedImageVersion-當您使用共用映射庫中的映射版本做為來源時，會使用此功能。

### <a name="iso-source"></a>ISO 來源
我們正從映射產生器淘汰這種功能，因為現在有[RHEL 自備訂](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)用帳戶映射，請參閱下列時程表：
    * 31 2020 年3月31日-資源提供者現在將不再接受 RHEL ISO 來源的映射範本。
    * 2020年4月30日-包含 RHEL ISO 來源的映射範本將不會再處理。

### <a name="platformimage-source"></a>PlatformImage 來源 
Azure 映射產生器支援 Windows Server 和用戶端，以及 Linux Azure Marketplace 映射，如需完整清單，請參閱[這裡](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support)。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


這裡的屬性與用來建立 VM 的相同，使用 AZ CLI 執行下列內容以取得屬性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

您可以使用版本中的「最新」，版本會在映射組建進行時進行評估，而不是在提交範本時進行評估。 如果您將此功能與共享映射庫目的地搭配使用，您可以避免重新提交範本，並以間隔重新執行映射組建，以便從最新的映射重新建立映射。

### <a name="managedimage-source"></a>ManagedImage 來源

將來源映射設定為一般化 VHD 或 VM 的現有受控映射。 來源受控映射必須是受支援的 OS，且位於與您的 Azure 映射產生器範本相同的區域中。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`應該是受控映射的 ResourceId。 用`az image list`來列出可用的映射。


### <a name="sharedimageversion-source"></a>SharedImageVersion 來源
將來源映射設定為共用映射庫中的現有映射版本。 映射版本必須是受支援的 OS，且映射必須複寫到與您的 Azure 映射產生器範本相同的區域。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`應該是映射版本的 ResourceId。 使用[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list)列出映射版本。

## <a name="properties-buildtimeoutinminutes"></a>屬性： buildTimeoutInMinutes

根據預設，映射產生器將會執行240分鐘。 之後，不論映射組建是否已完成，它都會超時並停止。 如果遇到超時時間，您會看到類似下面的錯誤：

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

如果您未指定 buildTimeoutInMinutes 值，或將它設定為0，則會使用預設值。 您可以增加或減少此值，最多可達960mins （16hrs）。 對於 Windows，我們不建議將此設定為低於60分鐘。 如果您發現您達到的是超時時間，請檢查[記錄](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)檔，查看自訂步驟是否正在等待類似使用者輸入的專案。 

如果您發現您需要更多時間才能完成自訂，請將此專案設定為您想要的需求，但有一些額外負荷。 但請不要將它設得太高，因為您可能必須等候它超時，才能看到錯誤。 


## <a name="properties-customize"></a>屬性：自訂

影像產生器支援多個「自建立者」。 自訂程式是用來自訂映射的函式，例如執行腳本或重新開機伺服器。 

使用`customize`時： 
- 您可以使用多個自建立者，但它們必須`name`具有唯一的。
- 自建者會依照範本中指定的循序執行。
- 如果一個自訂程式失敗，則整個自訂群組件將會失敗，並回報錯誤。
- 強烈建議您先徹底測試腳本，再于範本中使用。 在您自己的 VM 上進行腳本的偵錯工具會變得更容易。
- 請勿將機密資料放在腳本中。 
- 除非您使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)，否則腳本位置必須可公開存取。

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

 
[自訂] 區段是陣列。 Azure 映射產生器將依序按循序執行自建者。 任何自訂程式中的任何失敗都將導致建立程式失敗。 
 
 
### <a name="shell-customizer"></a>Shell 自訂者

Shell 自訂程式支援執行 shell 腳本，必須可公開存取，才能存取它們。

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

OS 支援： Linux 
 
自訂屬性：

- **類型**– Shell 
- **名稱**-追蹤自訂的名稱 
- **scriptUri** -檔案位置的 URI 
- shell 命令的**內嵌**陣列，以逗號分隔。
- **sha256Checksum** -檔案的 sha256 總和檢查碼值，您會在本機產生此檔案，而映射產生器將會進行總和檢查碼和驗證。
    * 若要使用 Mac/Linux 上的終端機來產生 sha256Checksum，請執行：`sha256sum <fileName>`


對於以超級使用者權限執行的命令，其前面必須加上`sudo`。

> [!NOTE]
> 以 RHEL ISO 來源執行 shell 自訂程式時，您必須確定第一個自訂命令介面會在進行任何自訂之前，先處理 Red Hat 權利伺服器的註冊。 完成自訂之後，腳本應該會向權利伺服器取消註冊。

### <a name="windows-restart-customizer"></a>Windows 重新開機自訂器 
重新開機自訂程式可讓您重新開機 Windows VM，並等候它重新上線，這可讓您安裝需要重新開機的軟體。  

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

OS 支援： Windows
 
自訂屬性：
- **類型**： WindowsRestart
- **restartCommand** -用來執行重新開機的命令（選擇性）。 預設值為 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **restartCheckCommand** –用來檢查重新開機是否成功的命令（選擇性）。 
- **restartTimeout** -以大小和單位的字串指定的重新開機超時。 例如， `5m` （5分鐘）或`2h` （2小時）。 預設值為： ' 5m '

### <a name="linux-restart"></a>Linux 重新開機  
沒有 Linux 重新開機自訂器; 不過，如果您要安裝驅動程式或需要重新開機的元件，您可以安裝它們，並使用 Shell 自訂程式叫用重新開機，組建 VM 會有20min 的 SSH 超時。

### <a name="powershell-customizer"></a>PowerShell 自訂者 
Shell 自訂程式支援執行 PowerShell 腳本和內嵌命令，必須可公開存取腳本，才能存取它們。

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

OS 支援： Windows 和 Linux

自訂屬性：

- **類型**– PowerShell。
- **scriptUri** -PowerShell 腳本檔案位置的 URI。 
- **inline** –要執行的內嵌命令，並以逗號分隔。
- **validExitCodes** –選擇性的有效程式碼，可從腳本/內嵌命令傳回，這可避免腳本/內嵌命令的回報失敗。
- **runElevated** –選擇性的布林值，支援以較高的許可權執行命令和腳本。
- **sha256Checksum** -檔案的 sha256 總和檢查碼值，您會在本機產生此檔案，而映射產生器將會進行總和檢查碼和驗證。
    * 若要產生 sha256Checksum，請在 Windows 上使用 PowerShell[取得雜湊](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>檔案自訂者

檔案自訂者可讓映射產生器從 GitHub 或 Azure 儲存體下載檔案。 如果您的映射組建管線依賴組建成品，您可以接著將檔案自訂者設定為從組建共用下載，並將成品移至映射。  

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

OS 支援： Linux 和 Windows 

檔案自訂者屬性：

- **sourceUri** -可存取的儲存體端點，這可以是 GitHub 或 Azure 儲存體。 您只能下載一個檔案，而不是整個目錄。 如果您需要下載目錄，請使用壓縮檔案，然後使用 Shell 或 PowerShell 自設程式將它解壓縮。 
- **目的地**–這是完整的目的地路徑和檔案名。 任何參考的路徑和子目錄都必須存在，請使用 Shell 或 PowerShell 自組成者，預先設定這些。 您可以使用腳本自編寫者來建立路徑。 

這受到 Windows 目錄和 Linux 路徑的支援，但有一些差異： 
- Linux OS –映射產生器唯一可以寫入的路徑是/tmp。
- Windows –沒有路徑限制，但路徑必須存在。
 
 
如果嘗試下載檔案時發生錯誤，或將它放在指定的目錄中，則自訂步驟將會失敗，而這會在自訂記錄檔中。

> [!NOTE]
> 檔案自訂者僅適用于小型檔案下載，< 20MB。 對於較大的檔案下載，請使用腳本或內嵌命令，並使用程式碼來下載檔案， `wget`例如`curl`Linux 或、 `Invoke-WebRequest`Windows。

您可以使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)從 Azure 儲存體下載檔案自訂檔中的檔案。

### <a name="windows-update-customizer"></a>Windows Update 自訂者
此自訂程式建置於 [布建程式 for Packer] 的 [[社區 Windows Update](https://packer.io/docs/provisioners/community-supported.html) ] 上，這是 Packer 社區所維護的開放原始碼專案。 Microsoft 會使用影像產生器服務來測試及驗證布建程式，並支援調查其問題，並解決問題，但 Microsoft 不會正式支援開放原始碼專案。 如需與 Windows Update 布建程式的詳細檔，請參閱專案存放庫。
 
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
OS 支援： Windows

自訂屬性：
- **輸入**– windowsupdate.log。
- **searchCriteria** -選擇性，定義要安裝的更新類型（建議、重要等）、BrowseOnly = 0 和 IsInstalled = 0 （建議）是預設值。
- **篩選**–選擇性，可讓您指定要包含或排除更新的篩選準則。
- **updateLimit** –選擇性，定義可以安裝的更新數目，預設值為1000。
 
 

### <a name="generalize"></a>一般化 
根據預設，Azure 映射產生器也會在每個映射自訂階段結束時執行「解除布建」程式碼，以「一般化」映射。 一般化是設定映射的程式，因此可以重複使用它來建立多個 Vm。 對於 Windows Vm，Azure 映射產生器會使用 Sysprep。 針對 Linux，Azure 映射產生器會執行 ' waagent-取消布建 '。 

要一般化的命令映射產生器使用者可能不適合每種情況，因此 Azure 映射產生器可讓您自訂此命令（如有需要）。 

如果您要遷移現有的自訂，而且您使用不同的 Sysprep/waagent 命令，您可以使用映射產生器的一般命令，如果 VM 建立失敗，請使用您自己的 Sysprep 或 waagent 命令。

如果 Azure 映射產生器已成功建立 Windows 自訂映射，並從中建立 VM，然後發現 VM 建立失敗或未順利完成，您將需要參閱 windows Server Sysprep 檔，或向 Windows Server Sysprep 客戶服務支援小組提出支援要求，他們可以進行疑難排解，並建議正確的 Sysprep 使用方式。


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
#### <a name="default-linux-deprovision-command"></a>預設的 Linux 取消布建命令

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>覆寫命令
若要覆寫命令，請使用 PowerShell 或 Shell 腳本 provisioners 來建立具有確切檔案名的命令檔，並將它們放在正確的目錄中：

* Windows： c:\DeprovisioningScript.ps1
* Linux：/tmp/DeprovisioningScript.sh

影像產生器將會讀取這些命令，這些命令會寫出至 AIB 記錄檔「自訂記錄」。 請參閱[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)如何收集記錄檔。
 
## <a name="properties-distribute"></a>屬性：散發

Azure 映射產生器支援三種散發目標： 

- **managedImage**管理的映射。
- **sharedImage** -共用映射資源庫。
- **Vhd** -儲存體帳戶中的 vhd。

您可以使用相同的設定將映射散發到這兩個目標型別，請參閱[範例](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)。

由於您可以將一個以上的目標散發至，因此，「影像產生器」會針對每個可透過查詢來存取的`runOutputName`散發目標維護狀態。  `runOutputName`是一個物件，您可以查詢發佈後的散發，以取得該散發的相關資訊。 例如，您可以查詢 VHD 的位置或複製映射版本的區域，或建立的 SIG 映射版本。 這是每個散發目標的屬性。 對於`runOutputName`每個散發目標而言，必須是唯一的。 以下是範例，這是查詢共用映射庫發佈：

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

### <a name="distribute-managedimage"></a>散發： managedImage

影像輸出將會是受控映射資源。

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
 
散發屬性：
- **類型**– managedImage 
- **imageId** –目的地映射的資源識別碼，應為格式：/subscriptions/\<subscriptionId>/Resourcegroups/\<destinationResourceGroupName>/providers/microsoft.compute/images/\<imageName>
- **位置**-受控映射的位置。  
- **runOutputName** –用來識別散發的唯一名稱。  
- **artifactTags** -選擇性的使用者指定的機碼值組標記。
 
 
> [!NOTE]
> 目的地資源群組必須存在。
> 如果您想要將映射散發至不同的區域，它會增加部署時間。 

### <a name="distribute-sharedimage"></a>散發： sharedImage 
Azure 共用映射資源庫是新的映射管理服務，可讓您管理映射區域複寫、版本控制和共用自訂映射。 Azure 映射產生器支援使用此服務散發，因此您可以將映射發佈到共用映射資源庫所支援的區域。 
 
共用映射資源庫是由下列各項所組成： 
 
- 圖庫-多個共用映射的容器。 資源庫會部署在一個區域中。
- 映射定義-影像的概念群組。 
- 映射版本-這是用來部署 VM 或擴展集的映射類型。 映射版本可以複寫到需要部署 Vm 的其他區域。
 
在您可以散發至映射庫之前，您必須先建立資源庫和映射定義，請參閱[共用映射](shared-images.md)。 

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

發佈共用映射資源庫的屬性：

- **類型**-sharedImage  
- **galleryImageId** –共用映射資源庫的識別碼。 格式\<為：/subscriptions/subscriptionId>/resourcegroups/\<resourceGroupName>/Providers/microsoft.compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>。
- **runOutputName** –用來識別散發的唯一名稱。  
- **artifactTags** -選擇性的使用者指定的機碼值組標記。
- **replicationRegions** -用於複寫的區域陣列。 其中一個區域必須是資源庫部署所在的區域。
 
> [!NOTE]
> 您可以將不同區域中的 Azure 映射產生器用於資源庫，但 Azure 映射產生器服務必須在資料中心之間傳輸映射，這會花費較長的時間。 影像產生器會根據單純整數自動將映射版本，您目前無法加以指定。 

### <a name="distribute-vhd"></a>散發： VHD  
您可以輸出至 VHD。 然後您可以複製 VHD，並使用它發佈至 Azure MarketPlace，或搭配 Azure Stack 使用。  

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
 
OS 支援： Windows 和 Linux

散發 VHD 參數：

- **類型**-VHD。
- **runOutputName** –用來識別散發的唯一名稱。  
- 卷**標-選擇性**的使用者指定的機碼值組標記。
 
Azure 映射產生器不允許使用者指定儲存體帳戶位置，但是您可以查詢的狀態`runOutputs`以取得位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 建立 VHD 之後，請儘快將它複製到不同的位置。 VHD 會儲存在映射範本提交至 Azure 映射產生器服務時所建立的暫存資源群組中的儲存體帳戶中。 如果您刪除映射範本，則會遺失 VHD。 
 
## <a name="next-steps"></a>後續步驟

[Azure 映射](https://github.com/danielsollondon/azvmimagebuilder)產生器 GitHub 中有適用于不同案例的範例. json 檔案。
 
