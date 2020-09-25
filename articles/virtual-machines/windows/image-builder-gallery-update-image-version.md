---
title: '使用 Azure 映射產生器 (預覽版，從現有的映射版本建立新的映射版本) '
description: 使用 Windows 中的 Azure 映射產生器，從現有的映射版本建立新的 VM 映射版本。
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 7515e0a39d1cf0da74d2a23457443e96716b4275
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335947"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>預覽：在 Windows 中使用 Azure 映射建立器，從現有的映射版本建立新的 VM 映射版本

本文說明如何在 [共用映射庫](shared-image-galleries.md)中取得現有的映射版本、更新它，並將其發佈為映射庫的新映射版本。

我們將會使用樣本 .json 範本來設定映像。 我們正在使用的 json 檔案如下： [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json)。 

> [!IMPORTANT]
> Azure Image Builder 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>註冊各項功能
若要在預覽期間使用 Azure Image Builder，您必須註冊新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

檢查功能註冊的狀態。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

檢查註冊。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

如果沒有顯示已註冊，請執行下列動作：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>設定變數和授權

如果您使用 [建立映射並散發至共用映射庫](image-builder-gallery.md) 來建立共用映射庫，您已經建立了我們所需的變數。 如果沒有，請設定要用於此範例的一些變數。

如果是預覽狀態，映像建立器僅針對在與來源受控映像相同的資源群組中建立自訂映像提供支援。 將此範例中的資源群組名稱更新為與來源受控映像相同。

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

為訂用帳戶識別碼建立變數。 您可以使用 `az account show | grep id` 取得此項目。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

取得您想要更新的映射版本。

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>建立使用者指派的身分識別，並在資源群組上設定權限
由於您已在先前的範例中設定使用者身分識別，因此您只需要取得其資源識別碼，就會將此識別碼附加至範本。

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

如果您已經有自己的共用映射庫，而且未遵循上述範例，您將需要指派影像產生器的許可權以存取資源群組，使其可以存取資源庫。 請參閱 [建立映射並散發至共用映射庫](image-builder-gallery.md) 範例中的步驟。


## <a name="modify-helloimage-example"></a>修改 helloImage 範例
您可以在這裡開啟 json 檔案，以查看我們即將使用的範例： [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) ，以及影像產生器 [範本參考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 


下載 json 範例，並使用您的變數進行設定。 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>建立映像

將映射設定提交至 VM 映射產生器服務。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

啟動映像建置。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

等到映射已建立並複寫後，再繼續進行下一個步驟。


## <a name="create-the-vm"></a>建立 VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>確認自訂
使用您在建立 VM 時所設定的使用者名稱與密碼，建立與該 VM 的遠端桌面連線。 在 VM 中，開啟命令提示字元並輸入：

```console
dir c:\
```

您現在應該會看到兩個目錄：
- `buildActions` 這是在第一個映射版本中建立的。
- `buildActions2` 這是在更新第一個映射版本時所建立，以建立第二個映射版本。


## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中所使用之 json 檔案的元件，請參閱影像產生器 [範本參考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
