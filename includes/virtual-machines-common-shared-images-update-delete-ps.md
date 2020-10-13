---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 6e0612a017650f0c6e4c9f63d9a5fd097b0b92c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303970"
---
## <a name="update-resources"></a>更新資源

可以更新的內容有一些限制。 您可以更新下列專案： 

共用映像庫：
- 描述

映像定義：
- 建議的 vCPU
- 建議的記憶體
- 描述
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新版排除
- 生命週期結束日期

如果您計畫新增複本區域，請勿刪除來源受控映射。 將映射版本複寫到其他區域時需要來源受控映射。 

若要更新資源庫的描述，請使用 [AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery)。

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

此範例示範如何使用 [更新 AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) 來更新映射定義的生命循環結束日期。

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

此範例示範如何使用 [new-azgalleryimageversion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) 來排除此映射版本，使其無法當做 *最新* 的映射使用。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

此範例示範如何使用 [更新 new-azgalleryimageversion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) 來包含此映射版本，以納入 *最新* 的映射。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>清除資源

在刪除資源時，您需要以嵌套資源中的最後一個專案為開頭-映射版本。 一旦刪除版本之後，您就可以刪除映射定義。 您無法刪除資源庫，直到其下的所有資源都已刪除為止。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

