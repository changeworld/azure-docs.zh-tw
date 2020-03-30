---
title: 使用 Azure 映射產生器創建 Windows VM（預覽）
description: 使用 Azure 映射產生器創建 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: e82d82dac833f7455e3d83d7e11c0c57c4eea816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238793"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>預覽：使用 Azure 映射產生器創建 Windows VM

本文將向您展示如何使用 Azure VM 映射產生器創建自訂的 Windows 映像。 本文中的示例使用[自訂器](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize)來自訂映射：
- PowerShell （腳本） - 下載並運行[PowerShell 腳本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重新開機 - 重新開機 VM。
- PowerShell（內聯） - 運行特定命令。 在此示例中，它使用 在 VM 上創建`mkdir c:\\buildActions`一個目錄。
- 檔 - 將檔從 GitHub 複製到 VM 上。 此示例[將index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)複製到`c:\buildArtifacts\index.html`VM 上。

還可以指定 。 `buildTimeoutInMinutes` 預設值為 240 分鐘，您可以增加生成時間以允許較長的運行生成。

我們將使用示例 .json 範本來配置映射。 我們使用的 .json 檔在這裡：[你好ImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure 映射產生器當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-the-features"></a>註冊功能

要在預覽期間使用 Azure 映射產生器，需要註冊新功能。

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

如果他們不說已註冊，則運行以下內容：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>設定變數

我們將反復使用一些資訊片段，因此我們將創建一些變數來存儲該資訊。


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

為訂閱 ID 創建變數。 您可以使用 獲取此`az account show | grep id`功能。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>建立資源群組
此資源組用於存儲映射配置範本專案和映射。


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>設置資源組的許可權

授予圖像產生器"參與者"在資源組中創建映射的許可權。 如果沒有這一點，映射生成將失敗。 

該`--assignee`值是映射產生器服務的應用註冊 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>下載映射配置範本示例

已創建參數化映射配置範本，供您嘗試。 下載示例 .json 檔，然後使用以前設置的變數對其進行配置。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

您可以在終端中使用文字編輯器（如`vi`）修改此示例。

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 對於源映射，必須始終[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)，不能使用`latest`。
> 如果添加或更改映射分發到的資源組，則必須在資源組上[設置許可權](#set-permissions-on-the-resource-group)。
 
## <a name="create-the-image"></a>建立映像

將映射配置提交到 VM 映射產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

完成後，這將返回成功消息返回到主控台，並在 中創建 。 `Image Builder Configuration Template` `$imageResourceGroup` 如果啟用"顯示隱藏類型"，則可以在 Azure 門戶中的資源組中看到此資源。

在後臺，映射產生器還將在訂閱中創建暫存資源組。 此資源組用於映射生成。 它將採用此格式：`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 不能直接刪除暫存資源組。 首先刪除圖像範本專案，這將導致刪除暫存資源組。

如果服務在映射配置範本提交期間報告失敗：
-  查看這些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步驟。 
- 在重試提交之前，您需要使用以下程式碼片段刪除範本。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>啟動映射生成
使用[az 資源叫用作業](/cli/azure/resource#az-resource-invoke-action)啟動映射生成過程。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等待生成完成。 這可能需要大約 15 分鐘。

如果您遇到任何錯誤，請查看這些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步驟。


## <a name="create-the-vm"></a>建立 VM

使用生成的映射創建 VM。 將*\<密碼>* 替換為 VM`aibuser`上的密碼。

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

使用創建 VM 時設置的使用者名和密碼創建與 VM 的遠端桌面連線。 在 VM 中，打開 cmd 提示並鍵入：

```console
dir c:\
```

您應該會看到在映射自訂期間創建的這兩個目錄：
- 生成操作
- 生成工件

## <a name="clean-up"></a>清除

完成後，刪除資源。

### <a name="delete-the-image-builder-template"></a>刪除映射產生器範本

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>刪除圖像資源組

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>後續步驟

要瞭解有關本文中使用的 .json 檔元件的更多內容，請參閱[圖像產生器範本引用](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
