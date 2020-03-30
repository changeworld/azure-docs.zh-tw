---
title: 從 Azure CLI 管理 IoT 中心 |微軟文檔
description: 本文介紹如何使用 CLI 創建和管理 IoT 中央應用程式。 您可以使用 CLI 查看、修改和刪除應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365518"
---
# <a name="manage-iot-central-from-azure-cli"></a>從 Azure CLI 管理 IoT 中心

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用[Azure CLI](/cli/azure/)來管理應用程式，而不是在[Azure IoT 中央應用程式管理器](https://aka.ms/iotcentral)網站上創建和管理 IoT 中央應用程式。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果願意在本地電腦上運行 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 在本地運行 Azure CLI 時，在嘗試本文中的命令之前，請使用**az 登錄**命令登錄到 Azure。

> [!TIP]
> 如果需要在不同的 Azure 訂閱中運行 CLI 命令，請參閱[更改活動訂閱](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)。

## <a name="install-the-extension"></a>安裝延伸模組

本文中的命令是**azure iot** CLI 擴展的一部分。 運行以下命令以安裝擴展：

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>建立應用程式

使用[az iotcentral 應用創建](/cli/azure/iotcentral/app#az-iotcentral-app-create)命令在 Azure 訂閱中創建 IoT 中心應用程式。 例如：

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

這些命令首先在美國東部區域為應用程式創建資源組。 下表描述了與**az iotcentral 應用創建**命令一起使用的參數：

| 參數         | 描述 |
| ----------------- | ----------- |
| resource-group    | 包含應用程式的資源群組。 此資源群組必須已經存在於您的訂用帳戶中。 |
| location          | 預設情況下，此命令使用資源組中的位置。 目前，您可以在**澳大利亞**、**亞太地區**、**歐洲**、**美國**、**英國****和日本**地區創建 IoT 中心應用程式。 |
| NAME              | 應用程式在 Azure 入口網站中的名稱。 |
| 子域         | 應用程式 URL 中的子網域。 在範例中，應用程式 URL 是 `https://mysubdomain.azureiotcentral.com`。 |
| sku               | 目前，您可以使用**ST1**或**ST2**。 請參閱 [Azure IoT Central 價格](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| template          | 要使用的應用程式範本。 如需詳細資訊，請參閱下列表格。 |
| display-name      | 在 UI 中顯示的應用程式名稱。 |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>檢視您的應用程式

使用[az iotcentral 應用清單](/cli/azure/iotcentral/app#az-iotcentral-app-list)命令列出 IoT 中心應用程式並查看中繼資料。

## <a name="modify-an-application"></a>修改應用程式

使用[az iotcentral 應用更新](/cli/azure/iotcentral/app#az-iotcentral-app-update)命令更新 IoT 中央應用程式的中繼資料。 例如，若要變更應用程式的顯示名稱：

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>移除應用程式

使用[az iotcentral 應用刪除](/cli/azure/iotcentral/app#az-iotcentral-app-delete)命令刪除 IoT 中心應用程式。 例如：

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何從 Azure CLI 管理 Azure IoT 中央應用程式，下面是建議的下一步：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)
