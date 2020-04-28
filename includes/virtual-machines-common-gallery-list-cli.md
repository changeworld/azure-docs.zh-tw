---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "66226030"
---
## <a name="using-rbac-to-share-images"></a>使用 RBAC 來共用映像

您可以使用角色型存取控制（RBAC），跨訂用帳戶共用影像。 具有映像版本 (甚至可跨訂用帳戶) 讀取權限的使用者，都能夠使用映像版本來部署虛擬機器。

如需使用 RBAC 共用資源的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 管理存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)。


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