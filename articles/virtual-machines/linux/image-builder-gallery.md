---
title: 使用 Azure 映射產生器搭配適用于 Linux Vm 的映射庫（預覽）
description: 使用 Azure 映射產生器和共用映射資源庫建立 Linux VM 映射。
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944996"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>預覽：建立 Linux 映射並將其發佈至共用映射資源庫 

本文說明如何使用 Azure 映射產生器和 Azure CLI，在[共用映射資源庫](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)中建立映射版本，然後全域散發映射。 您也可以使用[Azure PowerShell](../windows/image-builder-gallery.md)來執行此動作。


我們將使用範例. json 範本來設定映射。 我們所使用的. json 檔案位於這裡： [helloImageTemplateforSIG。](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json) 

若要將映射發佈到共用映射資源庫，範本會使用[sharedImage](image-builder-json.md#distribute-sharedimage)作為範本`distribute`區段的值。

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

## <a name="set-variables-and-permissions"></a>設定變數和許可權 

我們會重複使用一些資訊，因此我們將建立一些變數來儲存該資訊。

針對預覽，映射產生器僅支援在與來源受控映射相同的資源群組中建立自訂映射。 將此範例中的資源組名更新為與來源受控映射相同的資源群組。

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

建立訂用帳戶識別碼的變數。 您可以使用`az account show | grep id`來取得。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

建立資源群組。

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


授與 Azure 映射產生器許可權，以在該資源群組中建立資源。 `--assignee`值是映射產生器服務的應用程式註冊識別碼。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>建立映射定義和圖庫

若要將影像產生器與共享映射庫搭配使用，您需要有現有的映射庫和映射定義。 影像產生器不會為您建立映射庫和映射定義。

如果您還沒有資源庫和映射定義可供使用，請先加以建立。 首先，建立映射庫。

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

然後，建立映射定義。

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>下載並設定 json

下載 json 範本，並以您的變數加以設定。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>建立映射版本

下一個部分將會在資源庫中建立映射版本。 

將映射設定提交至 Azure 映射產生器服務。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

啟動映射組建。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

建立映射並將它複寫到這兩個區域可能需要一段時間。 等到此部分完成後，再繼續建立 VM。


## <a name="create-the-vm"></a>建立 VM

從 Azure 映射產生器所建立的映射版本建立 VM。

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

透過 SSH 連線到 VM。

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

您應該會在建立 SSH 連線後，看到該映射已自訂一*天的訊息*！

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>清除資源

如果您想要立即嘗試重新自訂映射版本，以建立相同映射的新版本，請略過後續步驟，並繼續[使用 Azure 映射產生器來建立另一個映射版本](image-builder-gallery-update-image-version.md)。


這會刪除已建立的映射，以及其他所有資源檔。 請先確定您已完成此部署，再刪除資源。

刪除映射庫資源時，您必須先刪除所有映射版本，才能刪除用來建立它們的映射定義。 若要刪除資源庫，您必須先刪除資源庫中的所有映射定義。

刪除映射產生器範本。

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

取得映射產生器所建立的映射版本，這一律以`0.`開頭，然後刪除映射版本

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


刪除映射定義。

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

刪除資源庫。

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

刪除資源群組。

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>後續步驟

深入瞭解[Azure 共用映射資源庫](shared-image-galleries.md)。