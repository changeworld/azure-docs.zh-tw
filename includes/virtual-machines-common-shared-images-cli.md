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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814768"
---
## <a name="before-you-begin"></a>開始之前

若要完成本文中的範例，您必須具有一般化虛擬機器的現有受控映像。 有關詳細資訊，請參閱[教程：使用 Azure CLI 創建 Azure VM 的自訂映射](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)。 如果託管映射包含資料磁片，則資料磁片大小不能超過 1 TB。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]**** 即可。 您還可以通過 訪問[https://shell.azure.com/bash](https://shell.azure.com/bash)在單獨的瀏覽器選項卡中啟動雲外殼。 選擇 **"複製"** 以複製代碼塊，將其粘貼到雲外殼中，然後按 Enter 以運行它。

如果希望在本地安裝和使用 CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 庫名稱不能包含破折號。   資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 建立映像資源庫。 下列範例會在 *myGalleryRG* 中建立名為 *myshare* 的資源庫。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>建立映像定義

圖像定義為圖像創建邏輯分組。 它們用於管理有關在其中創建的映射版本的資訊。 圖像定義名稱可以由大寫字母或小寫字母、數位、點、破折號和句點組成。 有關可以為圖像定義指定的值的詳細資訊，請參閱[圖像定義](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

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

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*主要版本*。*次要版本*。*補丁*。

在此示例中，我們的映射版本為*1.0.0，* 我們將在*美國中西部*區域創建 2 個副本，*在美國中南部*區域創建 1 個副本，在美國*東部 2*區域使用 1 個副本。


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
> 您需要等待映射版本完全完成正在構建和複製，然後才能使用相同的託管映射創建另一個映射版本。
>
> 您還可以通過在創建映射版本時添加添加`--storage-account-type standard_zrs`將所有映射版本副本存儲在[區域冗余存儲](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中。
>

## <a name="share-the-gallery"></a>共用庫

我們建議您在庫級別與其他使用者共用。 要獲取庫的物件識別碼，請使用[az sig 顯示](/cli/azure/sig#az-sig-show)。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用物件識別碼 作為作用域，以及電子郵件地址和 az[角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)，以授予使用者對共用映射庫的存取權限。

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


