---
title: 將 Azure 映射產生器與 Linux VM 的圖像庫一起使用（預覽）
description: 使用 Azure 映射產生器和共用映射庫創建 Linux VM 映射。
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944996"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>預覽：創建 Linux 映射並將其分發到共用圖像庫 

本文介紹如何使用 Azure 映射產生器和 Azure CLI 在[共用映射庫中](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)創建映射版本，然後全域分發映射。 您還可以使用[Azure PowerShell](../windows/image-builder-gallery.md)執行此操作。


我們將使用示例 .json 範本來配置映射。 我們使用的 .json 檔在這裡：[你好圖片範本FORSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

要將映射分發到共用圖像庫，範本使用[共用圖像](image-builder-json.md#distribute-sharedimage)作為範本`distribute`部分的值。

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

## <a name="set-variables-and-permissions"></a>設置變數和許可權 

我們將反復使用一些資訊片段，因此我們將創建一些變數來存儲該資訊。

對於預覽，圖像產生器將僅支援在與源託管映射相同的資源組中創建自訂映射。 將此示例中的資源組名稱更新為與源託管映射相同的資源組。

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

為訂閱 ID 創建變數。 您可以使用 獲取此`az account show | grep id`功能。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

建立資源群組。

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


授予 Azure 映射產生器在資源組中創建資源的許可權。 該`--assignee`值是映射產生器服務的應用註冊 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>創建圖像定義和庫

要將圖像產生器與共享圖像庫一起使用，您需要具有現有的圖像庫和圖像定義。 圖像產生器不會為您創建圖像庫和圖像定義。

如果還沒有要使用的庫和圖像定義，則從創建它們開始。 首先，創建一個圖像庫。

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

然後，創建圖像定義。

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


## <a name="download-and-configure-the-json"></a>下載並配置 .json

下載 .json 範本，並將其與變數一起配置。

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

## <a name="create-the-image-version"></a>創建映射版本

下一部分將在庫中創建圖像版本。 

將映射配置提交到 Azure 映射產生器服務。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

啟動映射生成。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

創建映射並將其複製到兩個區域可能需要一段時間。 等待此部件完成，然後再繼續創建 VM。


## <a name="create-the-vm"></a>建立 VM

從 Azure 映射產生器創建的圖像版本創建 VM。

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

一旦建立 SSH 連接，您應該會看到圖像是使用*當天消息*自訂的！

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>清除資源

如果要現在嘗試重新自訂映射版本以創建同一映射的新版本，請跳過後續步驟，然後繼續[使用 Azure 映射產生器創建另一個映射版本](image-builder-gallery-update-image-version.md)。


這將刪除創建的映射以及所有其他資源檔。 在刪除資源之前，請確保已完成此部署。

刪除圖像庫資源時，需要刪除所有圖像版本，然後才能刪除用於創建它們的圖像定義。 要刪除庫，首先需要刪除庫中的所有圖像定義。

刪除映射產生器範本。

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

獲取由映射產生器創建的圖像版本，這始終從`0.`開始，然後刪除映射版本

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


刪除圖像定義。

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

刪除庫。

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

刪除資源群組。

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>後續步驟

瞭解有關[Azure 共用映射庫](shared-image-galleries.md)的更多。