---
title: 將受控映射遷移至共用映射資源庫
description: 瞭解如何使用 Azure PowerShell 將受控映射遷移至共用映射庫中的映射版本。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c119ebc8d87c00608fc515099711463f47b8ed1a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87901805"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>從受控映射遷移至共用映射庫映射

如果您有想要遷移到共用映射資源庫的現有受控映射，您可以直接從受控映射建立共用映射庫映射。 測試新的映射之後，您就可以刪除來源受控映射。 您也可以使用[Azure CLI](image-version-managed-image-cli.md)，從受控映射遷移到共用映射資源庫。

映射庫中的映射有兩個元件，我們將在此範例中建立：
- **映射定義**會攜帶影像的相關資訊，以及使用它的需求。 這包括映射是 Windows 或 Linux、特製化還是一般化、版本資訊，以及最小和最大記憶體需求。 這是映像類型的定義。 
- 使用共用映射資源庫時，**映射版本**是用來建立 VM 的。 您可以視需要為環境準備多個映像版本。 當您建立 VM 時，映射版本會用來為 VM 建立新的磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成本文，您必須具有現有的受控映射。 如果受控映射包含資料磁片，資料磁片大小不能超過 1 TB。

逐步完成本文之後，請視需要取代資源群組和 VM 名稱。

## <a name="get-the-gallery"></a>取得資源庫

您可以依名稱列出所有資源庫和映射定義。 結果的格式為 `gallery\image definition\image version` 。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

找到正確的資源庫之後，請建立變數以供稍後使用。 這個範例會取得*myResourceGroup*資源群組中名為*myGallery*的圖庫。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>建立映像定義 

映像定義會建立映像的邏輯群組。 它們可用來管理影像的相關資訊。 映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 

建立映射定義時，請確定具有所有正確的資訊。 由於受控映射一律會一般化，因此您應該設定 `-OsState generalized` 。 

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](./windows/shared-image-galleries.md#image-definitions)。

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 建立映像定義。 在此範例中，映射定義名為*myImageDefinition*，而適用于一般化 Windows OS。 若要使用 Linux OS 來建立映射的定義，請使用 `-OsType Linux` 。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>取得受控映像

您可以使用 [Get-AzImage](/powershell/module/az.compute/get-azimage) 來查看資源群組中的可用映像清單。 當您知道映像名稱和其所位於的資源群組之後，您可以再次使用 `Get-AzImage` 來取得該映像物件，並將它儲存在變數中以供日後使用。 此範例會從 "myResourceGroup" 資源群組取得名為 *myImage* 的映象，然後將它指派至 *$managedImage* 變數。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>建立映像版本

使用[new-azgalleryimageversion](/powershell/module/az.compute/new-azgalleryimageversion)從受控映射建立映射版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，映像版本為 *1.0.0*，且它會被複寫到「美國中西部」** 和「美國中南部」** 資料中心。 選擇要複寫的目的地區域時，請記住，您也必須將*來源*區域納入做為複寫的目標。 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

將映像複寫到所有目標區域可能需要花上一點時間，因此我們已建立可追蹤進度的作業。 若要查看進度，請輸入 `$job.State` 。

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。 
>
> 建立映像版本時，您也可以藉由新增 `-StorageAccountType Premium_LRS`，將映像儲存在「進階」儲存體，或新增 `-StorageAccountType Standard_ZRS`，將映像儲存在[區域備援儲存體](../storage/common/storage-redundancy.md)。
>

## <a name="delete-the-managed-image"></a>刪除受控映射

一旦您確認新的映射版本能夠正常運作，就可以刪除受控映射。

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>後續步驟

一旦確認複寫已完成，您就可以從[一般化映射](vm-generalized-image-version-powershell.md)建立 VM。

如需有關如何提供採購方案資訊的詳細資訊，請參閱[在建立映射時提供 Azure Marketplace 購買方案資訊](marketplace-images.md)。
