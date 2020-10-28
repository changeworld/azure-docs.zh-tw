---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6432c125c4fedd962faa28a4c84c7494300b0472
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92756002"
---
## <a name="list-information"></a>清單資訊

使用 [az sig list](/cli/azure/sig#az-sig-list) 來取得可用映像資源庫的位置、狀態和其他資訊。

```azurecli-interactive 
az sig list -o table
```

使用 [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) 來列出資源庫中的映像定義，包括 OS 類型和狀態的相關資訊。

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

使用 [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list)，列出資源庫中共用的映像版本。

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

使用 [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show) 來取得映像版本的識別碼。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
