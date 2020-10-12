---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions
ms.openlocfilehash: adc621f932462422202d9f16fd539f5ecc7c3d8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336886"
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

> [!NOTE]
> IoT 隨插即用目前只能在美國中部、北歐和日本東部區域中建立的 IoT 中樞上使用。 基本層 IoT 中樞並未包含 IoT 隨插即用支援。

執行下列命令，在 IoT 中樞建立裝置身分識別。 將 `<YourIoTHubName>` 和 `<YourDeviceID>` 預留位置取代為您選擇的「IoT 中樞名稱」和「裝置識別碼」。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
