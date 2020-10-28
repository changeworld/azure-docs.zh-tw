---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: c4dcc0d578df5dbd693f4f2a05f3e531bb509838
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755771"
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

使用 ([az sig update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update)更新資源庫的描述。 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


使用 [az sig 映射定義更新](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)來更新映射定義的描述。

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

使用 [az sig image-version update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)更新映射版本，以新增要複寫的區域。 此變更需要一些時間，因為映射會複寫到新的區域。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

此範例示範如何使用 [az sig 映射版本更新](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) 來排除此映射版本，使其無法當做 *最新* 的映射使用。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

此範例示範如何使用 [az sig 映射版本更新](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) 來納入將此映射版本視為 *最新* 映射。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>刪除資源

您必須先刪除映射版本，以反轉順序刪除資源。 在刪除所有映像版本之後，就可以刪除映像定義。 在刪除所有映像定義之後，就可以刪除映像庫。 

使用 [az sig 映射-版本刪除](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)來刪除映射版本。

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

使用 [az sig 映射定義刪除](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)來刪除映射定義。

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


使用 [az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)刪除映射庫。

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
