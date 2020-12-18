---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ea5d4ef26fb14e22b871bb4bfa1054cb749d38e8
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673015"
---
## <a name="prepare-an-iot-hub"></a>準備 IoT 中樞

您的 Azure 訂用帳戶中必須要有 Azure IoT 中樞，才能完成此文章中的步驟。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果您要在本機使用 Azure CLI，請先使用 `az login` 登入您的 Azure 訂用帳戶。 如果您要在 Azure Cloud Shell 中執行這些命令，則會自動登入。

如果您在本機使用 Azure CLI，`az` 版本應為 **2.8.0** 或更新版本；Azure Cloud Shell 會使用最新版本。 使用 `az --version` 命令檢查電腦上所安裝的版本。

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的執行個體：

```azurecli-interactive
az extension add --name azure-iot
```

如果您還沒有可使用的 IoT 中樞，請執行下列命令，在您的訂用帳戶中建立資源群組和免費層 IoT 中樞。 將 `<YourIoTHubName>` 取代為您選擇的中樞名稱：

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

執行下列命令，在 IoT 中樞建立裝置身分識別。 將 `<YourIoTHubName>` 和 `<YourDeviceID>` 預留位置取代為您選擇的「IoT 中樞名稱」和「裝置識別碼」。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
