---
title: PowerShell-從共用映射庫中的快照集或 VHD 建立映射
description: 瞭解如何使用 PowerShell 從共用映射庫中的快照集或 VHD 建立映射。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 315c635ba0864dc1565fd7ba5ccc450223d87ac9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494712"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>使用 PowerShell 在共用映射資源庫中從 VHD 或快照集建立映射

如果您有想要遷移到共用映射資源庫的現有快照集或 VHD，您可以直接從 VHD 或快照集建立共用映射庫映射。 測試新的映射之後，您就可以刪除來源 VHD 或快照集。 您也可以使用[Azure CLI](image-version-snapshot-cli.md)，從共用映射庫中的 VHD 或快照集建立映射。

映射庫中的映射有兩個元件，我們將在此範例中建立：
- **映射定義**會攜帶影像的相關資訊，以及使用它的需求。 這包括映射是 Windows 或 Linux、特製化還是一般化、版本資訊，以及最小和最大記憶體需求。 這是映像類型的定義。 
- 使用共用映射資源庫時，**映射版本**是用來建立 VM 的。 您可以視需要為環境準備多個映像版本。 當您建立 VM 時，映射版本會用來為 VM 建立新的磁片。 映像版本可以使用多次。


## <a name="before-you-begin"></a>開始之前

若要完成本文，您必須擁有快照集或 VHD。 

如果您想要包含資料磁片，資料磁片大小不能超過 1 TB。

當您完成本文時，請視需要取代資源名稱。


## <a name="get-the-snapshot-or-vhd"></a>取得快照集或 VHD

您可以使用[new-azsnapshot](/powershell/module/az.compute/get-azsnapshot)查看資源群組中可用的快照集清單。 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

一旦知道快照集名稱及其所在的資源群組，您就可以 `Get-AzSnapshot` 再次使用來取得快照集物件，並將它儲存在變數中以供稍後使用。 這個範例會從 "myResourceGroup" 資源群組取得名為*mySnapshot*的快照集，並將它指派給 *$source*的變數。 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

您也可以使用 VHD，而不是快照集。 若要取得 VHD，請使用[new-azdisk](/powershell/module/az.compute/get-azdisk)。 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

然後取得 VHD，並將它指派給 `$source` 變數。

```azurepowershell-interactive
$source = Get-AzDisk `
   -SnapshotName mySnapshot
   -ResourceGroupName myResourceGroup
```

您可以使用相同的 Cmdlet 來取得您想要包含在映射中的任何資料磁片。 將它們指派給變數，然後在您建立映射版本之後使用這些變數。


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

建立映射定義時，請確定具有所有正確的資訊。 在此範例中，我們假設快照集或 VHD 來自于使用中的 VM，而且尚未一般化。 如果 VHD 或快照集是由一般化 OS 所建立（執行 Windows 或[waagent](https://github.com/Azure/WALinuxAgent)的 Sysprep 或 `-deprovision` Linux 之後）， `-deprovision+user` 請將變更 `-OsState` 為 `generalized` 。 

若要深入了解您可以為映像定義指定哪些值，請參閱[映像定義](./windows/shared-image-galleries.md#image-definitions)。

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 建立映像定義。 在此範例中，映射定義的名稱為*myImageDefinition*，而適用于特製化 Windows OS。 若要使用 Linux OS 來建立映射的定義，請使用 `-OsType Linux` 。 

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

### <a name="purchase-plan-information"></a>購買方案資訊

在某些情況下，從以 Azure Marketplace 映射為基礎的映射建立 VM 時，您需要在中傳遞購買計畫資訊。 在這些情況下，建議您在映射定義中包含購買方案資訊。 在此情況下，請參閱[在建立映射時提供 Azure Marketplace 購買方案資訊](marketplace-images.md)。


## <a name="create-an-image-version"></a>建立映像版本

使用[new-azgalleryimageversion](/powershell/module/az.compute/new-azgalleryimageversion)，從快照集建立映射版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

如果您想要讓映射包含資料磁片（除了 OS 磁片），請新增 `-DataDiskImage` 參數，並將它設定為數據磁片快照集或 VHD 的識別碼。

在此範例中，映像版本為 *1.0.0*，且它會被複寫到「美國中西部」** 和「美國中南部」** 資料中心。 選擇要複寫的目的地區域時，請記住，您也必須將*來源*區域納入做為複寫的目標。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

將映像複寫到所有目標區域可能需要花上一點時間，因此我們已建立可追蹤進度的作業。 若要查看作業的進度，請輸入 `$job.State`。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 您必須等候映射版本完全完成建立和複寫，才能使用相同的快照集來建立另一個映射版本。 
>
> 您也可以在建立映射版本時新增，以將映射版本儲存在[區域的多餘儲存體](../storage/common/storage-redundancy.md)中 `-StorageAccountType Standard_ZRS` 。
>

## <a name="delete-the-source"></a>刪除來源

一旦您確認新的映射版本能夠正常運作，您可以使用[new-azsnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot)或[remove-new-azdisk](/powershell/module/az.compute/remove-azdisk)刪除映射的來源。


## <a name="next-steps"></a>後續步驟

一旦您確認複寫已完成，您就可以從[特製化映射](vm-specialized-image-version-powershell.md)建立 VM。

如需有關如何提供採購方案資訊的詳細資訊，請參閱[在建立映射時提供 Azure Marketplace 購買方案資訊](marketplace-images.md)。
