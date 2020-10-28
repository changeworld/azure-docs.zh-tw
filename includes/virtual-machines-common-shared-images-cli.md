---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 58f38f5fe5544f01636d5fd2b2393025e5cb01ab
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755892"
---
## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 

資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 資源庫名稱不能包含連字號。   資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 建立映像資源庫。 下列範例會在「美國東部」建立名為 myGalleryRG 的資源群組，以及名為 myGallery 的資源庫。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>共用資源庫

您可以使用角色型存取控制 (RBAC) 來跨訂用帳戶共用映像。 您可以在資源庫、映射定義或映射版本層級共用映射。 具有映像版本 (甚至可跨訂用帳戶) 讀取權限的使用者，都能夠使用映像版本來部署 VM。

我們建議您在資源庫層級上與其他使用者共用。 若要取得資源庫的物件識別碼，請使用 [az sig show](/cli/azure/sig#az-sig-show)。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用物件識別碼作為範圍，以及使用電子郵件地址和 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 對使用者授與共用映像庫的存取權。 將 `<email-address>` 和 `<gallery iD>` 取代為您的個人資訊。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

如需使用 RBAC 共用資源的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 管理存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)。
