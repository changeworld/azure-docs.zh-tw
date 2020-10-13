---
title: 從 Azure CLI 管理 IoT Central |Microsoft Docs
description: 本文說明如何使用 CLI 建立和管理您的 IoT Central 應用程式。 您可以使用 CLI 來查看、修改和移除應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: bd87f15ff63edf1da447faf986cad2f9591610dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502959"
---
# <a name="manage-iot-central-from-azure-cli"></a>從 Azure CLI 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用[Azure CLI](/cli/azure/)來管理您的應用程式，而不需在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您想要在本機電腦上執行 Azure CLI，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。 當您在本機執行 Azure CLI 時，請先使用 **az login 命令登** 入 Azure，然後再嘗試執行本文中的命令。

> [!TIP]
> 如果您需要在不同的 Azure 訂用帳戶中執行 CLI 命令，請參閱 [變更有效訂](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)用帳戶。

## <a name="install-the-extension"></a>安裝延伸模組

本文中的命令是 **azure iot** CLI 擴充功能的一部分。 執行下列命令以安裝延伸模組：

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>建立應用程式

使用 [az iot central 應用程式 create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) 命令，在您的 Azure 訂用帳戶中建立 IoT Central 應用程式。 例如：

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

這些命令會先在美國東部區域建立應用程式的資源群組。 下表說明搭配 **az iot central 應用程式 create** 命令使用的參數：

| 參數         | 描述 |
| ----------------- | ----------- |
| resource-group    | 包含應用程式的資源群組。 此資源群組必須已經存在於您的訂用帳戶中。 |
| location          | 根據預設，此命令會使用來自資源群組的位置。 目前，您可以在**澳大利亞**、**亞太地區**、**歐洲**、**美國** **、英國和****日本**地區建立 IoT Central 應用程式。 |
| NAME              | 應用程式在 Azure 入口網站中的名稱。 |
| 子域         | 應用程式 URL 中的子網域。 在範例中，應用程式 URL 是 `https://mysubdomain.azureiotcentral.com`。 |
| sku               | 目前，您可以使用 **ST1** 或 **ST2**。 請參閱 [Azure IoT Central 價格](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| template          | 要使用的應用程式範本。 如需詳細資訊，請參閱下列表格。 |
| display-name      | 在 UI 中顯示的應用程式名稱。 |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>檢視您的應用程式

使用 [az iot central 應用程式清單](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) 命令，列出您的 IoT Central 應用程式並查看中繼資料。

## <a name="modify-an-application"></a>修改應用程式

使用 [az iot central 應用程式更新](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) 命令來更新 IoT Central 應用程式的中繼資料。 例如，若要變更應用程式的顯示名稱：

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>移除應用程式

使用 [az iot central 應用程式刪除](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) 命令來刪除 IoT Central 應用程式。 例如：

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何從 Azure CLI 管理 Azure IoT Central 的應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)
