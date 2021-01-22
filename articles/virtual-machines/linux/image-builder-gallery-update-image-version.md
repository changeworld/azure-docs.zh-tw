---
title: '使用 Azure 映射產生器 (預覽版，從現有的映射版本建立新的 VM 映射版本) '
description: 在 Linux 中使用 Azure 映射建立器，從現有的映射版本建立新的 VM 映射版本。
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: 13cdb444046fd8d3138e1c33ed65495e9bfe5e98
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685053"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>預覽：在 Linux 中使用 Azure 映射建立器，從現有的映射版本建立新的 VM 映射版本

本文說明如何在 [共用映射庫](../shared-image-galleries.md)中取得現有的映射版本、更新它，並將其發佈為映射庫的新映射版本。

我們將會使用樣本 .json 範本來設定映像。 我們正在使用的 json 檔案如下： [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json)。 


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

如果您使用 [建立映射並散發至共用映射庫](image-builder-gallery.md) 來建立共用映射庫，您已經建立了一些需要的變數。 如果沒有，請設定要用於此範例的一些變數。


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

為訂用帳戶識別碼建立變數。 您可以使用 `az account show | grep id` 取得此項目。

```console
subscriptionID=<Subscription ID>
```

取得您想要更新的映射版本。

```azurecli
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
您可以在這裡開啟 json 檔案，以查看我們即將使用的範例： [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) ，以及影像產生器 [範本參考](image-builder-json.md)。 


下載 json 範例，並使用您的變數進行設定。 

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>建立映像

將映射設定提交至 VM 映射產生器服務。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

啟動映像建置。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

等到映射已建立並複寫後，再繼續進行下一個步驟。


## <a name="create-the-vm"></a>建立 VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

使用 VM 的公用 IP 位址建立與 VM 的 SSH 連線。

```console
ssh azureuser@<pubIp>
```

當您建立 SSH 連線時，您應該會看到映射已自訂為「一天訊息」。

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

輸入 `exit` 以關閉 SSH 連接。

您也可以列出資源庫中現在可用的映射版本。

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>下一步

若要深入瞭解本文中所使用之 json 檔案的元件，請參閱影像產生器 [範本參考](../linux/image-builder-json.md)。