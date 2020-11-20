---
title: '從 VM (預覽版建立映射) '
description: 瞭解如何使用 Azure PowerShell 從 Azure 中現有的 VM，在共用映射庫中建立映射。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3a7ca8236307bbf8a419d2988e1a6dc1e4c40597
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964859"
---
# <a name="preview-create-an-image-from-a-vm"></a>預覽：從 VM 建立映射

如果您有想要用來建立多個相同 Vm 的現有 VM，您可以使用該 VM，以 Azure PowerShell 在共用映射庫中建立映射。 您也可以使用 [Azure CLI](image-version-vm-cli.md)從 VM 建立映射。

您可以使用 Azure PowerShell 從特製化 [和一般化](./windows/shared-image-galleries.md#generalized-and-specialized-images) vm 捕捉映射。 

映射庫中的影像有兩個元件，我們將在此範例中建立這些元件：
- **映射定義** 會攜帶映射的相關資訊，以及使用它的需求。 這包括映射是 Windows 或 Linux、特製化或一般化、版本資訊，以及最小和最大記憶體需求。 這是映像類型的定義。 
- **映射版本** 是用來在使用共用映射庫時建立 VM 的版本。 您可以視需要為環境準備多個映像版本。 當您建立 VM 時，映射版本會用來建立 VM 的新磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成這篇文章，您必須有現有的共用映射庫，以及 Azure 中現有的 VM 作為來源使用。 

如果 VM 已連接資料磁片，資料磁片大小不能超過 1 TB。

當您完成本文時，請視需要取代資源名稱。


## <a name="get-the-gallery"></a>取得資源庫

您可以依名稱列出所有資源庫和映射定義。 結果的格式為 `gallery\image definition\image version` 。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

一旦找到正確的資源庫和映射定義之後，請建立變數以供稍後使用。 此範例會取得 *myResourceGroup* 資源群組中名為 *>mygalleryrg* 的資源庫。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>取得 VM

您可以使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) 來查看資源群組中的可用 VM 清單。 一旦您知道 VM 名稱及其所在的資源群組之後，您可以 `Get-AzVM` 再次使用來取得 vm 物件，並將它儲存在變數中，以供稍後使用。 此範例會從 "myResourceGroup" 資源群組取得名為 *sourceVM* 的 VM，並將它指派給變數 *$sourceVm*。 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

使用 [new-azvm](/powershell/module/az.compute/stop-azvm)建立映射之前，最好先 stop\deallocate VM。

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>建立映像定義 

映像定義會建立映像的邏輯群組。 它們可用來管理影像的相關資訊。 映像定義名稱可以由大寫或小寫字母、數字、點、虛線和句點組成。 

進行映射定義時，請確定已有所有正確的資訊。 如果您將 VM (用於 Windows 的 Sysprep 或適用于 Linux 的 waagent-取消布建) 則您應該使用建立映射定義 `-OsState generalized` 。 如果您未將 VM 一般化，請使用建立映射定義 `-OsState specialized` 。

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](./windows/shared-image-galleries.md#image-definitions)。

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 建立映像定義。 

在此範例中，映射定義名為 *myImageDefinition*，適用于執行 Windows 的特製化 VM。 若要使用 Linux 建立映射的定義，請使用 `-OsType Linux` 。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>建立映像版本

使用 [New-new-azgalleryimageversion](/powershell/module/az.compute/new-azgalleryimageversion)建立映射版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，映像版本為 *1.0.0*，且它會被複寫到「美國中西部」和「美國中南部」資料中心。 選擇要複寫的目的地區域時，請記住，您也必須包含 *來源* 區域做為複寫目標。

若要從 VM 建立映像版本，請使用 `$vm.Id.ToString()` 作為 `-SourceImageId`。

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

將映像複寫到所有目標區域可能需要花上一點時間，因此我們已建立可追蹤進度的作業。 若要查看作業的進度，請輸入 `$job.State`。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。
>
> 您也可以在 `-StorageAccountType Premium_LRS` 建立映射版本時新增或 [區域多餘的儲存體](../storage/common/storage-redundancy.md) ，藉以將映射儲存在 Premium 儲存體中 `-StorageAccountType Standard_ZRS` 。
>

## <a name="next-steps"></a>後續步驟

一旦您確認新的映射版本可以正常運作，您就可以建立 VM。 從 [特製化映射版本](vm-specialized-image-version-powershell.md) 或 [一般化映射版本](vm-generalized-image-version-powershell.md)建立 VM。

如需有關如何提供採購方案資訊的詳細資訊，請參閱 [在建立映射時提供 Azure Marketplace 採購方案資訊](marketplace-images.md)。
