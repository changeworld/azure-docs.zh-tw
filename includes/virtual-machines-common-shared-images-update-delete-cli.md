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
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173991"
---
## <a name="update-resources"></a>更新資源

可以更新的內容有一些限制。 可以更新以下專案： 

共用映像庫：
- 描述

映像定義：
- 建議的 vCPU
- 推薦記憶體
- 描述
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新版排除
- 生命週期結束日期

如果計畫添加副本區域，請不要刪除源託管映射。 需要源託管映射才能將映射版本複製到其他區域。 

使用[（az sig 更新](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update)） 更新庫的說明。 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


使用[az sig 圖像定義更新](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)圖像定義的說明。

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

更新圖像版本以添加要複製的區域，使用[az sig 映射版本更新](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)。 當圖像複製到新區域時，此更改將需要一段時間。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>刪除資源

您必須通過首先刪除映射版本，以相反的順序刪除資源。 在刪除所有映像版本之後，就可以刪除映像定義。 在刪除所有映像定義之後，就可以刪除映像庫。 

使用[az sig 圖像版本刪除圖像版本](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)。

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

使用[az sig 圖像定義刪除圖像定義](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)。

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


使用[az sig 刪除刪除](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)圖像庫。

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
