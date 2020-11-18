---
title: 使用 Azure CLI 建立 IoT 中樞 | Microsoft Docs
description: 瞭解如何使用 Azure CLI 命令來建立資源群組，然後在資源群組中建立 IoT 中樞。 同時瞭解如何移除中樞。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659908"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>使用 Azure CLI 建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文將說明如何使用 Azure CLI 建立 IoT 中樞。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

使用 Azure CLI 建立資源群組，然後新增 IoT 中樞。

1. 在建立 IoT 中樞時，必須將其建立在資源群組內。 使用現有的資源群組，或執行下列[命令來建立資源群組](/cli/azure/resource) (英文)：
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > 上一個範例會建立位於美國西部位置的資源群組。 您可以執行下列命令，以檢視可用位置清單： 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. 使用 IoT 中樞的全域唯一名稱，在資源群組中執行下列[命令建立 IoT 中樞](/cli/azure/iot/hub#az-iot-hub-create) (英文)：
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


上一個命令會在您付費使用的 S1 定價層中建立 IoT 中樞。 如需詳細資訊，請參閱 [Azure IoT 中樞價格](https://azure.microsoft.com/pricing/details/iot-hub/)。

## <a name="remove-an-iot-hub"></a>移除 IoT 中樞

您可以使用 Azure CLI 來[刪除個別資源](/cli/azure/resource) (英文，例如 IoT 中樞)，或刪除資源群組和其所有資源 (包括任何 IoT 中樞)。

若要 [刪除 IoT 中樞](/cli/azure/iot/hub#az-iot-hub-delete)，請執行下列命令：

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

若要 [刪除資源群組](/cli/azure/group#az-group-delete) 和其所有資源，請執行下列命令：

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 IoT 中樞，請參閱以下文章︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)
* [使用 Azure 入口網站管理 IoT 中樞](iot-hub-create-through-portal.md)