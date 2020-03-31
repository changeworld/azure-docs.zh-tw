---
title: 包含檔案
description: 包含檔案
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112301"
---
### <a name="create-a-user-assigned-identity"></a>建立使用者指派的身分識別

使用[az 標識創建][az-identity-create]命令在訂閱中創建名為*myACRTasksId*的標識。 可以使用以前用於創建容器註冊表或其他資源組的資源組。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

要在以下步驟中配置使用者分配的標識，請使用[az 標識顯示][az-identity-show]命令在變數中存儲標識的資源識別碼、主體 ID 和用戶端 ID。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show