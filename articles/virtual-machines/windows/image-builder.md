---
title: 使用 Azure 映像產生器建立 Windows VM(預覽)
description: 使用 Azure 映像產生器創建 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869502"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>預覽:使用 Azure 映像產生器建立 Windows VM

本文將向您展示如何使用 Azure VM 映射生成器創建自訂的 Windows 映射。 本文中的範例使用[自訂器](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize)來自定義映射:
- PowerShell (文稿) - 下載並執行[PowerShell 文稿](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重新啟動 - 重新啟動 VM。
- PowerShell(內聯) - 運行特定命令。 在此範例中,它使用在 VM`mkdir c:\\buildActions`上創建一個目錄。
- 檔案 - 將檔案從 GitHub 複製到 VM 上。 此示例[將index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)`c:\buildArtifacts\index.html`複製到VM上。

還可以指定`buildTimeoutInMinutes`。 預設值為 240 分鐘,您可以增加生成時間以允許較長的運行生成。

我們將使用示例 .json 範本來配置映射。 我們使用的 .json 檔案在這裡:[你好ImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure 映射生成器當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-the-features"></a>註冊功能

要在預覽期間使用 Azure 映射生成器,需要註冊新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

檢查功能註冊的狀態。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

檢查您的註冊。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果他們不說已註冊,則運行以下內容:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>設定變數

我們將反覆使用一些資訊片段,因此我們將創建一些變數來存儲該資訊。


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

為訂閱 ID 創建變數。 您可以使用 獲取`az account show | grep id`此 功能。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>建立資源群組
此資源組用於存儲映射配置範本專案和映射。


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>設定資源群組的權限

授予圖像生成器"參與者"在資源組中創建映射的許可權。 如果沒有這一點,映射生成將失敗。 

該`--assignee`值是映射生成器服務的應用註冊 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>下載映像設定樣本範例

已建立參數化映像配置範本,供您嘗試。 下載範例 .json 檔,然後使用以前設置的變數對其進行配置。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

您可以在終端中使用文字編輯器(如`vi`)修改此範例。

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 對於源映像,必須始終[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure),不能`latest`使用 。
> 如果新增或更改映像分轉到的資源群組,則必須在資源群組上[設定權限](#set-permissions-on-the-resource-group)。
 
## <a name="create-the-image"></a>建立映像

將映像設定提交到 VM 映像產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

完成後,這會傳回成功訊息傳回到主控台,並在`Image Builder Configuration Template``$imageResourceGroup`中建立 。 如果啟用「顯示隱藏類型」,則可以在 Azure 門戶中的資源組中看到此資源。

在後台,映射生成器還將在訂閱中創建暫存資源組。 此資源組用於映射生成。 它將採用此格式:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 不能直接刪除暫存資源組。 首先刪除影像範本專案,這將導致刪除暫存資源組。

如果服務在映像設定樣本提交期間報告失敗:
-  查看這些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步驟。 
- 在重試提交之前,您需要使用以下代碼段刪除範本。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>開機映像產生
使用[az 資源調用操作](/cli/azure/resource#az-resource-invoke-action)啟動映像生成過程。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等待生成完成。 這可能需要大約 15 分鐘。

如果您遇到任何錯誤,請查看這些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步驟。


## <a name="create-the-vm"></a>建立 VM

使用生成的映射創建 VM。 將*\<密碼>* 替換為`aibuser`VM 上的密碼。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>驗證自訂

使用創建 VM 時設置的使用者名稱和密碼創建與 VM 的遠端桌面連接。 在 VM 中開啟 cmd 提示並鍵入:

```console
dir c:\
```

您應該會看到在映像自訂期間建立的這兩個目錄:
- 組建操作
- 組建工件

## <a name="clean-up"></a>清除

完成後,刪除資源。

### <a name="delete-the-image-builder-template"></a>刪除映像產生器樣本

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>移除影像資源群組

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>後續步驟

要瞭解有關本文中使用的 .json 檔案元件的更多內容,請參閱[影像產生器樣本參考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
