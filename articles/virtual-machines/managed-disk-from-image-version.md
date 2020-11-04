---
title: 從映射版本建立受控磁片
description: 從共用映射庫中的映射版本建立受控磁片。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 5873f28fed492f9ef906a9d7c1364d8ae07033a7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336056"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>從映射版本建立受控磁片

如有需要，您可以從映射版本將作業系統或單一資料磁片從存放在共用映射庫中的映射版本匯出為受控磁片。


## <a name="cli"></a>CLI

使用 [az sig image-version 清單](/cli/azure/sig/image-version.md#az_sig_image_version_list)來列出資源庫中的映射版本。 在此範例中，我們會在 *>mygalleryrg* 映射庫中尋找屬於 *myImageDefinition* 映射定義一部分的所有映射版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

將 `source` 變數設定為映射版本的識別碼，然後使用 [az disk create](/cli/azure/disk.md#az_disk_create) 來建立受控磁片。 

在此範例中，我們會匯出映射版本的 OS 磁片，以在 *EastUS* 區域中建立名為 *myManagedOSDisk* 的受控磁片（位於名為 *myResourceGroup* 的資源群組中）。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



如果您想要從映射版本匯出資料磁片，請新增 `--gallery-image-reference-lun` 以指定要匯出之資料磁片的 LUN 位置。 

在此範例中，我們會匯出位於映射版本之 LUN 0 的資料磁片，以在名為 *myResourceGroup* 的資源群組中建立名為 *myManagedDataDisk* 的受控磁片（位於 *EastUS* 區域）。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

使用 [get-azresource](/powershell/module/az.resources/get-azresource)列出資源庫中的映射版本。 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

一旦擁有您需要的所有資訊之後，您就可以使用 [new-azgalleryimageversion](/powershell/module/az.compute/get-azgalleryimageversion) 取得您想要使用的來源映射版本，並將它指派給變數。 在此範例中，我們會取得 `1.0.0` `myImageDefinition` `myGallery` 資源群組中來源資源庫中定義的映射版本 `myResourceGroup` 。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

將變數設定 `source` 為映射版本的識別碼之後，請使用 [new-azdiskconfig](/powershell/module/az.compute/new-azdiskconfig) 來建立磁片設定，並使用 [>get-azdisk](/powershell/module/az.compute/new-azdisk) 來建立磁片。 

在此範例中，我們會匯出映射版本的 OS 磁片，以在 *EastUS* 區域中建立名為 *myManagedOSDisk* 的受控磁片（位於名為 *myResourceGroup* 的資源群組中）。 

建立磁片設定。
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

建立磁片。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

如果您想要匯出映射版本的資料磁片，請將 LUN 識別碼新增至磁片設定，以指定要匯出之資料磁片的 LUN 位置。 

在此範例中，我們會匯出位於映射版本之 LUN 0 的資料磁片，以在名為 *myResourceGroup* 的資源群組中建立名為 *myManagedDataDisk* 的受控磁片（位於 *EastUS* 區域）。 

建立磁片設定。
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

建立磁片。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>後續步驟

您也可以使用 [Azure CLI](image-version-managed-image-cli.md) 或 [PowerShell](image-version-managed-image-powershell.md)，從受控磁片建立映射版本。


