---
title: 從映射版本建立受控磁片
description: 從共用映射庫中的映射版本建立受控磁片。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049684"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>從映射版本建立受控磁片

如有需要，您可以從儲存在共用映射庫中的映射版本建立受控磁片。


## <a name="cli"></a>CLI

將 `source` 變數設定為映射版本的識別碼，然後使用 [az disk create](/cli/azure/disk.md#az_disk_create) 來建立受控磁片。 


您可以使用 [az sig 映射版本清單](/cli/azure/sig/image-version.md#az_sig_image_version_list)來查看清單映射版本。 在此範例中，我們會在 *>mygalleryrg*映射庫中尋找屬於*myImageDefinition*映射定義一部分的所有映射版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


在此範例中，我們會在名為*myResourceGroup*的資源群組中，于*EastUS*區域中建立名為*myManagedDisk*的受控磁片。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

如果磁片是資料磁片，請新增 `--gallery-image-reference-lun` 以指定 LUN。

## <a name="powershell"></a>PowerShell

您可以使用 [get-azresource](/powershell/module/az.resources/get-azresource)列出所有的映射版本。 

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

設定磁片資訊的一些變數。

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

使用來源映射版本識別碼建立磁片設定，然後建立磁片。 若為 `-GalleryImageReference` ，只有在來源是資料磁片時，才需要 LUN。

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

建立磁片。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>後續步驟

您也可以使用 [Azure CLI](image-version-managed-image-cli.md) 或 [PowerShell](image-version-managed-image-powershell.md)，從受控磁片建立映射版本。


