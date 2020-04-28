---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "66814768"
---
## <a name="before-you-begin"></a>開始之前

若要完成本文中的範例，您必須具有一般化虛擬機器的現有受控映像。 如需詳細資訊，請參閱[教學課程：使用 Azure CLI 建立 AZURE VM 的自訂映射](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)。 如果受控映射包含資料磁片，資料磁片大小不能超過 1 TB。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您想要在本機安裝和使用 CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 圖庫名稱不能包含連字號。   資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 建立映像資源庫。 下列範例會在 *myGalleryRG* 中建立名為 *myshare* 的資源庫。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>建立映像定義

映射定義會建立影像的邏輯群組。 它們可用來管理在其中建立之映射版本的相關資訊。 影像定義名稱可以由大寫或小寫字母、數位、點、虛線和句號組成。 如需您可以為映射定義指定之值的詳細資訊，請參閱[影像定義](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立初始映像定義。

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>建立映像版本 

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 建立所需的映像版本。 您必須傳入受控映像的識別碼，以作為建立映像版本的基準。 您可以使用 [az image list](/cli/azure/image?view#az-image-list) 來取得資源群組中映像的相關資訊。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式： *MajorVersion*。*MinorVersion*。*修補程式*。

在此範例中，我們的映射版本為*1.0.0* ，而我們將在*美國中西部*區域中建立2個複本，在*美國中南部*區域使用1個複本，而在*美國東部 2*區域中建立1個複本，並使用區域多餘的儲存體。


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> 您必須等候映射版本完全完成建立和複寫，才能使用相同的受控映射來建立另一個映射版本。
>
> 您也可以在建立映射版本時新增`--storage-account-type standard_zrs` ，以將所有映射版本複本儲存在[區域冗余儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中。
>

## <a name="share-the-gallery"></a>共用資源庫

我們建議您在資源庫層級與其他使用者共用。 若要取得資源庫的物件識別碼，請使用[az sig show](/cli/azure/sig#az-sig-show)。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用物件識別碼作為範圍，以及電子郵件地址和[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create) ，以授與使用者共用映射資源庫的存取權。

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


