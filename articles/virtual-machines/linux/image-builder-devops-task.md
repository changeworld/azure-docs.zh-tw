---
title: Azure Image Builder 服務 DevOps 工作
description: Azure DevOps 工作，將組建成品插入 VM 映射，讓您可以安裝和設定您的應用程式和作業系統。
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: a3016900b6265bfd56ad1a5a71f70efc01181af5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499249"
---
# <a name="azure-image-builder-service-devops-task"></a>Azure Image Builder 服務 DevOps 工作

本文說明如何使用 Azure DevOps 工作，將組建成品插入 VM 映射中，讓您可以安裝和設定您的應用程式和作業系統。

## <a name="devops-task-versions"></a>DevOps 工作版本
有兩個 Azure VM 映射產生器 (AIB) DevOps 工作：

* 「穩定」的[AIB](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)工作，這可讓我們在將更新和功能升階到「穩定」工作（大約1個星期之後）時，將其放入最新的更新和功能，讓客戶進行測試。 


* 「不穩定」的[AIB](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)工作，這讓我們能夠在將其升階為「穩定」工作之前，先放入最新的更新和功能，讓客戶進行測試。 如果沒有回報的問題，而且我們的遙測沒有顯示任何問題（大約1周），我們會將工作程式碼升階為「穩定」。 

## <a name="prerequisites"></a>先決條件

* [從 Visual Studio Marketplace 安裝穩定的 DevOps](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)工作。
* 您必須擁有 VSTS DevOps 帳戶，並已建立組建管線
* 在管線所使用的訂用帳戶中註冊並啟用映射產生器功能需求：
    * [Az PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Az CLI](../windows/image-builder.md#register-the-features)
    
* 在來源映射資源群組中建立標準 Azure 儲存體帳戶，您可以使用其他資源群組/儲存體帳戶。 儲存體帳戶會用來將組建成品從 DevOps 工作轉移至映射。

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>將工作新增至發行管線

選取 **發行管線**  >  **編輯**

在使用者代理程式上，選取 *+* 要新增的映射產生器，然後搜尋 **影像** 產生器。 選取 [新增]  。

設定下列工作屬性：

### <a name="azure-subscription"></a>Azure 訂用帳戶

從下拉式功能表中選取您想要讓映射產生器執行的訂用帳戶。 使用與您的來源映射所在的相同訂用帳戶，以及要在其中散發映射的訂用帳戶。 您必須授權映射產生器參與者對訂用帳戶或資源群組的存取權。

### <a name="resource-group"></a>資源群組

使用將儲存暫存影像範本成品的資源群組。 建立範本成品時，會建立額外的暫存映射產生器資源群組 `IT_<DestinationResourceGroup>_<TemplateName>_guid` 。 暫存資源群組會儲存影像中繼資料，例如腳本。 在工作結束時，會刪除映射範本成品和暫存映射產生器資源群組。
 
### <a name="location"></a>Location

位置是映射產生器將執行的區域。 只支援設定的 [區域](../windows/image-builder-overview.md#regions) 數量。 來源映射必須存在於此位置。 例如，如果您使用共用映射庫，則複本必須存在於該區域中。

### <a name="managed-identity-required"></a>需要 (受控識別) 
影像產生器需要受控識別，它是用來讀取來源自訂映射、連接 Azure 儲存體，以及建立自訂映射。 詳細資訊請看[這裡](./image-builder-overview.md#permissions)。

### <a name="vnet-support"></a>VNET 支援

DevOps 工作目前不支援指定現有的子網，但如果您想要利用現有的 VNET，您可以使用 ARM 範本，並將影像產生器範本內嵌在內部，請參閱 Windows 映像產生器範本範例以瞭解如何達成此目的，或使用 [AZ AIB PowerShell](../windows/image-builder-powershell.md)。

### <a name="source"></a>來源

來源映射必須是支援的映射產生器 OSs。 您可以在執行映射產生器的相同區域中，選擇現有的自訂映射：
* 受控映射-您必須傳入 resourceId，例如：
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure 共用映射庫-您必須傳入映射版本的 resourceId，例如：
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    如果您需要取得最新的共用映射庫版本，您可以先擁有 AZ PowerShell 或 AZ CLI 工作，才能取得最新版本並設定 DevOps 變數。 使用 AZ VM Image Builder DevOps 工作中的變數。 如需詳細資訊，請參閱 [範例](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)。

*  (Marketplace) 基礎映射中有常用映射的下拉式清單，這些映射一律會使用支援的 OS 最新版本。 

    如果基底映射不在清單中，您可以使用來指定確切的映射 `Publisher:Offer:Sku` 。

    基底映射版本 (選擇性) -您可以提供想要使用的映射版本，預設值為 `latest` 。

### <a name="customize"></a>自訂

#### <a name="provisioner"></a>布建程式

一開始，支援兩個自訂程式- **Shell** 和 **PowerShell**。 僅支援內嵌。 如果您想要下載腳本，您可以傳遞內嵌命令來這麼做。

針對您的作業系統，請選取 PowerShell 或 Shell。

#### <a name="windows-update-task"></a>Windows Update 工作

針對僅限 Windows，工作會在自訂結束時執行 Windows Update。 它會處理所需的重新開機。

執行下列 Windows Update 設定：
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
它會安裝不是預覽的重要和建議的 Windows 更新。

#### <a name="handling-reboots"></a>處理重新開機
DevOps 工作目前不支援重新開機 Windows 組建，如果您嘗試使用 PowerShell 程式碼重新開機，組建將會失敗。 不過，您可以使用程式碼來重新開機 Linux 組建。

#### <a name="build-path"></a>組建路徑

此工作的設計目的是要能夠將 DevOps 組建發行成品插入映射中。 若要完成此工作，您必須設定組建管線。 在發行管線的設定中，您必須加入組建構件的存放庫。

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="在發行管線中選取 [新增成品]。":::

選取 [ **組建路徑** ] 按鈕，以選擇要放在映射上的組建資料夾。 影像產生器工作會複製其中的所有檔案和目錄。 建立映射時，影像產生器會根據 OS，將檔案和目錄部署至不同的路徑。

> [!IMPORTANT]
> 新增存放庫成品時，您可能會發現目錄前面加上底線 *_*。 底線可能會導致內嵌命令發生問題。 在命令中使用適當的引號。
> 

下列範例將說明其運作方式：

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="顯示階層的目錄結構。":::


* Windows-檔案存在於 `C:\` 。 會建立名為的目錄 `buildArtifacts` ，其中包含 `webapp` 目錄。

* Linux-檔案存在於中  `/tmp` 。 `webapp`建立的目錄包含所有檔案和目錄。 您必須從這個目錄移動檔案。 否則，將會刪除它們，因為它位於臨時目錄中。

#### <a name="inline-customization-script"></a>內嵌自訂腳本

* Windows-您可以輸入以逗號分隔的 PowerShell 內嵌命令。 如果您想要在組建目錄中執行腳本，您可以使用：

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux-Linux 系統上的組建成品會放在 `/tmp` 目錄中。 不過，在許多 Linux OSs 上，重新開機時，會刪除/tmp 目錄內容。 如果您希望構件存在於映射中，您必須建立另一個目錄，並將其複製到其中。  例如：

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    如果您可以使用「/tmp」目錄，則可以使用下列程式碼來執行腳本。
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>映射組建之後的組建成品會發生什麼事？

> [!NOTE]
> 影像產生器不會自動移除組建成品，強烈建議您一律具有可移除組建成品的程式碼。
> 

* Windows-映射產生器會將檔案部署至 `c:\buildArtifacts` 目錄。 保存目錄時，您必須移除目錄。 您可以在執行的腳本中移除它。 例如：

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux-組建成品會放在目錄中 `/tmp` 。 不過，在許多 Linux OSs 上，重新開機時， `/tmp` 會刪除目錄內容。 建議您擁有可移除內容的程式碼，而不需依賴作業系統來移除內容。 例如：

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>映射組建的總長度

目前無法變更 DevOps 管線工作中的總長度。 它使用預設值240分鐘。 如果您想要增加 [buildTimeoutInMinutes](./image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-buildtimeoutinminutes)，則可以在發行管線中使用 AZ CLI 工作。 設定工作以複製範本並提交範本。 如需範例，請參閱此 [解決方案](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)，或使用 Az PowerShell。


#### <a name="storage-account"></a>儲存體帳戶

選取您在必要條件中建立的儲存體帳戶。 如果您在清單中看不到它，則影像產生器沒有其許可權。

當組建開始時，影像產生器將會建立名為的容器 `imagebuilder-vststask` 。 容器是存放庫中的組建成品儲存位置。

> [!NOTE]
> 您必須在每個組建之後，手動刪除儲存體帳戶或容器。
>

### <a name="distribute"></a>散佈

支援三種散發類型。

#### <a name="managed-image"></a>受控映像

* Id
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* 位置

#### <a name="azure-shared-image-gallery"></a>Azure 共用映射庫

共用映射庫 **必須** 已經存在。

* Id 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* 區域：區域清單，以逗號分隔。 例如，westus、eastus、centralus

#### <a name="vhd"></a>VHD 

您無法將任何值傳遞給這個，影像產生器會將 VHD 發出至 vhd 容器中的暫存映射產生器資源群組 `IT_<DestinationResourceGroup>_<TemplateName>` 。 *vhds* 當您啟動發行組建時，影像產生器會發出記錄。 完成時，它會發出 VHD URL。

### <a name="optional-settings"></a>選擇性設定

* [Vm 大小](image-builder-json.md#vmprofile) -您可以從預設的 *STANDARD_D1_V2* 覆寫 vm 大小。 您可以覆寫以減少自訂時間的總計，或因為您想要建立相依于特定 VM 大小的映射（例如 GPU/HPC 等）。

## <a name="how-it-works"></a>運作方式

當您建立發行時，此工作會在儲存體帳戶中建立名為 *imagebuilder-vststask* 的容器。 它會 zips 並上傳您的組建成品，並建立 zip 檔案的 SAS 權杖。

工作會使用傳遞給工作的屬性來建立影像產生器範本成品。 此工作會執行下列動作：
* 下載組建成品 zip 檔案和任何其他相關的腳本。 這些檔案會儲存在暫存映射產生器資源群組的儲存體帳戶中 `IT_<DestinationResourceGroup>_<TemplateName>` 。
* 建立前面加上 *t_* 和10位數單純整數的範本。 範本會儲存至您選取的資源群組。 資源群組中的組建持續時間內會有範本。 

範例輸出︰

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

當映射組建開始時，會在發行記錄中報告執行狀態：

```text
starting run template...
```

當映射組建完成時，您會看到類似下列文字的輸出：

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

影像範本和已 `IT_<DestinationResourceGroup>_<TemplateName>` 刪除。

您可以採用 ' $ (imageUri) ' VSTS 變數，並在下一個工作中使用它，或只使用值並建立 VM。

## <a name="output-devops-variables"></a>輸出 DevOps 變數

來源 marketplace 映射的 Pub/供應專案/SKU/版本：
* $ (pirPublisher) 
* $ (pirOffer) 
* $ (pirSku) 
* $ (pirVersion) 

映射 URI-分散式映射的 ResourceID：
* $ (imageUri) 

## <a name="faq"></a>常見問題集

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>我可以使用我在 DevOps 之外建立的現有映射範本嗎？

目前沒有。

### <a name="can-i-specify-the-image-template-name"></a>我可以指定映射範本名稱嗎？

否。 使用唯一的範本名稱，然後刪除。

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>影像產生器失敗。 我該如何進行疑難排解？

如果發生組建失敗，DevOps 工作就不會刪除暫存資源群組。 您可以存取包含組建自訂記錄檔的預備資源群組。

您會在 DevOps 記錄檔中看到 VM 映射產生器工作的錯誤，並查看自訂記錄檔的位置。 例如：

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="顯示失敗的 DevOps 工作錯誤範例。":::

如需疑難排解的詳細資訊，請參閱針對 [Azure 映射產生器服務進行疑難排解](image-builder-troubleshoot.md)。 

在調查失敗之後，您可以刪除暫存資源群組。 首先，刪除映射範本資源成品。 成品前面會加上 *t_* ，並可在 DevOps 工作組建記錄檔中找到：

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

映射範本資源成品位於一開始在工作中指定的資源群組中。 當您完成疑難排解時，請刪除成品。 如果使用 Azure 入口網站刪除資源群組中的 [ **顯示隱藏的類型**]，則可查看成品。


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 映射](image-builder-overview.md)建立器總覽。