---
title: 使用 PowerShell 從另一個資源庫複製映射
description: 使用 Azure PowerShell 從另一個資源庫複製映射。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 48f0a247ed023583c8489994439a790944b90fdc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082591"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>使用 PowerShell 從另一個資源庫複製映射

如果您的組織中有多個資源庫，您可以從儲存在其他資源庫中的影像建立映射。 例如，您可能有開發和測試資源庫，可用於建立和測試新的映射。 當他們準備好在生產環境中使用時，您可以使用此範例將它們複製到生產資源庫。 您也可以使用[Azure CLI](image-version-another-gallery-cli.md)，從另一個資源庫中的映射建立映射。


## <a name="before-you-begin"></a>開始之前

若要完成本文，您必須擁有現有的來源資源庫、映射定義和映射版本。 您也應該有目的地資源庫。 

來源映射版本必須複寫到目的地資源庫所在的區域。 

我們將在您的目的地資源庫中建立新的映射定義和映射版本。


當您完成本文時，請視需要取代資源名稱。


## <a name="get-the-source-image"></a>取得來源映射 

您將需要來源映射定義的資訊，以便在目的地資源庫中建立複本。

使用[get-azresource](/powershell/module/az.resources/get-azresource) Cmdlet，列出現有資源庫、映射定義和映射版本的相關資訊。

結果的格式為 `gallery\image definition\image version` 。

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

一旦擁有所需的所有資訊，您就可以使用[new-azgalleryimageversion](/powershell/module/az.compute/get-azgalleryimageversion)取得來源映射版本的識別碼。 在此範例中，我們會取得 `1.0.0` `myImageDefinition` `myGallery` 資源群組中來源庫內的定義映射版本 `myResourceGroup` 。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>建立映射定義 

您需要建立符合來源映射定義的新映射定義。 您可以使用[AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition)來查看重新建立映射定義所需的所有資訊。

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


輸出會如下所示：

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

使用[AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) Cmdlet 和上述輸出中的資訊，在您的目的地資源庫中建立新的映射定義。


在此範例中，映射定義在名為*myDestinationGallery*的資源庫中名為*myDestinationImgDef* 。


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>建立映像版本

使用[new-azgalleryimageversion](/powershell/module/az.compute/new-azgalleryimageversion)建立映射版本。 您必須在參數中傳入來源映射的識別碼，以在 `--managed-image` 目的地資源庫中建立映射版本。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此範例中，[*美國西部*] 位置的 [ *myDestinationRG* ] 資源群組中的目的地資源庫名為*myDestinationGallery*。 我們的映射版本為*1.0.0* ，我們將在*美國中南部*區域建立1個複本，並在*美國西部*區域中建立2個複本。 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
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
> 建立映像版本時，您也可以藉由新增 `-StorageAccountType Premium_LRS`，將映像儲存在「進階」儲存體，或新增 `-StorageAccountType Standard_ZRS`，將映像儲存在[區域備援儲存體](../storage/common/storage-redundancy.md)。
>


## <a name="next-steps"></a>後續步驟

從[一般化](vm-generalized-image-version-powershell.md)或[特製](vm-specialized-image-version-powershell.md)化映射版本建立 VM。

[Azure 映射產生器（預覽）](./linux/image-builder-overview.md)可協助自動建立映射版本，您甚至可以使用它來更新和[建立現有映射版本的新映射版本](./linux/image-builder-gallery-update-image-version.md)。 

如需有關如何提供採購方案資訊的詳細資訊，請參閱[在建立映射時提供 Azure Marketplace 購買方案資訊](marketplace-images.md)。
