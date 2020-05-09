---
title: 使用 Azure CLI，從受控映射遷移至映射版本
description: 瞭解如何使用 Azure CLI，從受控映射遷移至共用映射庫中的映射版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 6f49ece874ea52227e6531193fc53b3bea525702
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796599"
---
# <a name="migrate-from-a-managed-image-to-an-image-version-using-the-azure-cli"></a>使用 Azure CLI，從受控映射遷移至映射版本
如果您有想要遷移到共用映射資源庫的現有受控映射，您可以直接從受控映射建立共用映射庫映射。 測試新的映射之後，您就可以刪除來源受控映射。 您也可以使用[PowerShell](image-version-managed-image-powershell.md)，從受控映射遷移至共用映射資源庫。

映射庫中的映射有兩個元件，我們將在此範例中建立：
- **映射定義**會攜帶影像的相關資訊，以及使用它的需求。 這包括映射是 Windows 或 Linux、特製化還是一般化、版本資訊，以及最小和最大記憶體需求。 這是映像類型的定義。 
- 使用共用映射資源庫時，**映射版本**是用來建立 VM 的。 您可以視需要為環境準備多個映像版本。 當您建立 VM 時，映射版本會用來為 VM 建立新的磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成本文，您必須擁有現有的[共用映射資源庫](shared-images-cli.md)。 

若要完成本文中的範例，您必須具有一般化虛擬機器的現有受控映像。 如需詳細資訊，請參閱[Capture a 受控映射](./linux/capture-image.md)。 如果受控映射包含資料磁片，資料磁片大小不能超過 1 TB。

逐步完成本文之後，請視需要取代資源群組和 VM 名稱。



## <a name="create-an-image-definition"></a>建立映像定義

由於受控映射一律是一般化的映射，因此您將使用`--os-state generalized`來為一般化映射建立映射定義。

影像定義名稱可以由大寫或小寫字母、數位、點、虛線和句號組成。 

如需您可以為映射定義指定之值的詳細資訊，請參閱[影像定義](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用[az sig image 定義 create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立映射定義。

在此範例中，映射定義名為*myImageDefinition*，而適用于[通用](./linux/shared-image-galleries.md#generalized-and-specialized-images)Linux OS 映射。 若要使用 Windows 作業系統來建立映射的定義，請`--os-type Windows`使用。 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>建立映射版本

使用[az image 圖庫 create-image 版本](/cli/azure/sig/image-version#az-sig-image-version-create)來建立版本。 您必須傳入受控映像的識別碼，以作為建立映像版本的基準。 您可以使用[az image list](/cli/azure/image?view#az-image-list)來取得影像的識別碼。 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式： *MajorVersion*。*MinorVersion*。*修補程式*。

在此範例中，我們的映射版本為*1.0.0* ，而我們將在*美國中南部*區域建立1個複本，並在*美國東部 2*區域中使用區域多餘的儲存體來建立1個複本。 選擇要複寫的目的地區域時，請記住，您也必須將*來源*區域納入做為複寫的目標。

在`--managed-image`參數中傳遞受控映射的識別碼。


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> 您必須等候映射版本完全完成建立和複寫，才能使用相同的受控映射來建立另一個映射版本。
>
> 您也可以在建立映射版本時新增`--storage-account-type standard_zrs` ，以將所有映射版本複本儲存在[區域冗余儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中。
>

## <a name="next-steps"></a>後續步驟

從[一般化映射版本](vm-generalized-image-version-cli.md)建立 VM。