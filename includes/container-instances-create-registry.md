---
title: 包含檔案
description: 包含檔案
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2b1d9b7f9ff07a3e0c7745191decc3e82181553e
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708014"
---
## <a name="create-azure-container-registry"></a>建立 Azure Container Registry

建立容器登錄之前，您需要「資源群組」** 才能部署它。 資源群組是在其中部署及管理所有 Azure 資源的邏輯集合。

使用 [az group create][az-group-create] 命令來建立資源群組。 在下列範例中，會在 eastus 區域中建立名為 myResourceGroup 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

建立資源群組後，請使用 [az acr create][az-acr-create] 命令建立 Azure Container Registry。 容器登錄名稱在 Azure 內必須是唯一的，且必須包含 5-50 個英數字元。 以登錄的唯一名稱取代 `<acrName>`：

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

以下是名為 mycontainerregistry082 的新 Azure Container Registry 輸出：

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

本教學課程的其餘部分將 `<acrName>` 視為您在此步驟中所選容器登錄名稱的預留位置。

## <a name="log-in-to-container-registry"></a>登入容器登錄

您必須先登入 Azure Container Registry 執行個體，才能將映像推送給它。 使用 [az acr login][az-acr-login] 命令來完成此作業。 您必須提供在建立容器登錄時提供的唯一名稱。

```azurecli
az acr login --name <acrName>
```

例如：

```azurecli
az acr login --name mycontainerregistry082
```

完成後，此命令會傳回 `Login Succeeded`：

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create