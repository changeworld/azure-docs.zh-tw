---
title: 使用 Azure 映射產生器建立 Windows VM （預覽）
description: 使用 Azure 映射產生器來建立 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869502"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>預覽：使用 Azure 映射產生器建立 Windows VM

本文說明如何使用 Azure VM 映射產生器來建立自訂的 Windows 映像。 本文[中的範例會使用自](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize)定義映射：
- PowerShell （ScriptUri）-下載並執行[powershell 腳本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重新開機-重新開機 VM。
- PowerShell （內嵌）-執行特定的命令。 在此範例中，它會使用`mkdir c:\\buildActions`在 VM 上建立目錄。
- File-從 GitHub 將檔案複製到 VM。 這個範例會[index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)將 index.md `c:\buildArtifacts\index.html`複製到 VM 上的。

您也可以指定`buildTimeoutInMinutes`。 預設值是240分鐘，您可以增加組建時間以允許較長的執行中組建。

我們將使用範例. json 範本來設定映射。 我們所使用的. json 檔案位於這裡： [helloImageTemplateWin。](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json) 


> [!IMPORTANT]
> Azure 映射產生器目前為公開預覽版。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-the-features"></a>註冊功能

若要在預覽期間使用 Azure 映射產生器，您必須註冊新功能。

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

如果沒有顯示 [已註冊]，請執行下列動作：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>設定變數

我們會重複使用一些資訊，因此我們將建立一些變數來儲存該資訊。


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

建立訂用帳戶識別碼的變數。 您可以使用`az account show | grep id`來取得。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>建立資源群組
此資源群組用來儲存映射設定範本成品和映射。


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>設定資源群組的許可權

提供映射產生器的「參與者」許可權，以在資源群組中建立映射。 如果沒有這麼做，映射組建將會失敗。 

`--assignee`值是映射產生器服務的應用程式註冊識別碼。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>下載映射設定範本範例

已為您建立參數化映射設定範本，以供您嘗試。 下載範例 json 檔案，並使用您先前設定的變數來設定它。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

您可以在終端機中使用像`vi`是的文字編輯器來修改此範例。

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 針對來源映射，您必須一律[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)，而不能使用`latest`。
> 如果您新增或變更將映射散發至其中的資源群組，您必須在資源群組上[設定許可權](#set-permissions-on-the-resource-group)。
 
## <a name="create-the-image"></a>建立映像

將映射設定提交至 VM 映射產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

完成時，這會將成功訊息傳回至主控台，並`Image Builder Configuration Template`在中建立。 `$imageResourceGroup` 如果您啟用「顯示隱藏的類型」，您可以在 [Azure 入口網站] 的資源群組中看到此資源。

在背景中，映射產生器也會在您的訂用帳戶中建立預備資源群組。 此資源群組用於映射組建。 它的格式如下：`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 您不能直接刪除暫存資源群組。 首先，刪除映射範本成品，這會導致暫存資源群組遭到刪除。

如果服務在映射設定範本提交期間報告失敗：
-  請參閱這些[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步驟。 
- 您必須先使用下列程式碼片段刪除範本，然後再重試提交。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>啟動映射組建
使用[az resource invoke-action](/cli/azure/resource#az-resource-invoke-action)啟動映射建立程式。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等候組建完成。 這可能需要大約15分鐘的時間。

如果您遇到任何錯誤，請參閱這些[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步驟。


## <a name="create-the-vm"></a>建立 VM

使用您建立的映射建立 VM。 將* \<password>* 取代為您在 VM 上`aibuser`的密碼。

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

使用您在建立 VM 時所設定的使用者名稱和密碼，建立 VM 的遠端桌面連線。 在 VM 中，開啟命令提示字元，然後輸入：

```console
dir c:\
```

您應該會看到這兩個在映射自訂期間建立的目錄：
- buildActions
- buildArtifacts

## <a name="clean-up"></a>清除

當您完成時，請刪除資源。

### <a name="delete-the-image-builder-template"></a>刪除映射產生器範本

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>刪除映射資源群組

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中所使用之 json 檔案的元件，請參閱影像產生器[範本參考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
