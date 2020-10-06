---
title: 設定 IoT 隨插即用所需的 IoT 資源 |Microsoft Docs
description: 建立 IoT 中樞和裝置布建服務實例，以搭配 IoT 隨插即用快速入門和教學課程使用。
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3b7c9b51bad45bb348f70c8b0e433404b49b5aac
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761357"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>針對 IoT 隨插即用快速入門和教學課程設定您的環境

在您可以完成任何 IoT 隨插即用快速入門和教學課程之前，您必須先在 Azure 訂用帳戶中設定 IoT 中樞和裝置布建服務 (DPS) 。 您也需要範例應用程式和 Azure IoT explorer 工具所使用之模型檔案的本機複本。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要避免在本機安裝 Azure CLI 的需求，您可以使用 Azure Cloud Shell 來設定雲端服務。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>建立資源

建立資源的 Azure 資源群組：

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

建立 IoT 中樞。 下列命令會使用名稱 `my-pnp-hub` 做為要建立的 IoT 中樞名稱範例。 選擇要用來取代的 IoT 中樞的唯一名稱 `my-pnp-hub` ：

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

建立 DPS 實例。 下列命令使用名稱做為 `my-pnp-dps` 要建立之 DPS 實例的名稱範例。 選擇要用來取代的 DPS 實例的唯一名稱 `my-pnp-dps` ：

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

若要將 DPS 實例連結至 IoT 中樞，請使用下列命令。 `my-pnp-dps`將和取代 `my-pnp-hub` 為您先前選擇的唯一名稱：

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>取得設定

某些快速入門和教學課程會使用您 IoT 中樞的連接字串。 當您設定 Azure IoT explorer 工具時，您也需要連接字串。 取出連接字串，並立即記下它。 `my-pnp-hub`以您為 IoT 中樞選擇的唯一名稱取代：

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

大部分的快速入門和教學課程都使用 DPS 設定的 *識別碼範圍* 。 取出識別碼範圍，並立即記下它。 將取代為 `my-pnp-dps` 您為 DPS 實例選擇的唯一名稱：

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

所有快速入門和教學課程都使用 DPS 裝置註冊。 使用下列命令， `my-pnp-device` 在您的 DPS 實例中建立 *個別裝置註冊* 。 取代為 `my-pnp-dps` 您為 DPS 實例選擇的唯一名稱。 請記下註冊識別碼和主要金鑰值，以在快速入門和教學課程中使用：

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>建立環境變數

建立五個環境變數來設定快速入門和教學課程中的範例，以使用裝置布建服務 (DPS) 連接到您的 IoT 中樞：

* **IOTHUB_DEVICE_SECURITY_TYPE**：值 `DPS` 。
* **IOTHUB_DEVICE_DPS_ID_SCOPE**：您先前所記下的 DPS 識別碼範圍。
* **IOTHUB_DEVICE_DPS_DEVICE_ID**：值 `my-pnp-device` 。
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**：您先前所記下的註冊主要金鑰。
* **IOTHUB_DEVICE_DPS_ENDPOINT**：值 `global.azure-devices-provisioning.net`

服務範例需要下列環境變數，以識別要連接的中樞和裝置：

* **IOTHUB_CONNECTION_STRING**：您先前所記下的 IoT 中樞連接字串。
* **IOTHUB_DEVICE_ID**： `my-pnp-device` 。

例如，在 Linux bash shell 中：

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

例如，在 Windows 命令列上：

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>下載模型檔案

快速入門和教學課程會使用溫度控制器和控溫器裝置的範例模型檔案。 若要下載範例模型檔案：

1. 在本機電腦上建立名為 *models* 的資料夾。

1. 以滑鼠右鍵按一下 [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)，並將 JSON 檔案儲存至 *models* 資料夾。

1. 以滑鼠右鍵按一下 [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)，並將 JSON 檔案儲存至 *models* 資料夾。

## <a name="install-the-azure-iot-explorer"></a>安裝 Azure IoT 檔案總管

快速入門和教學課程會使用 **Azure IoT explorer** 工具。 移至 [Azure IoT explorer 版本](https://github.com/Azure/azure-iot-explorer/releases) ，並展開最新版本的資產清單。 下載並安裝適用于您作業系統的最新版應用程式。

當您第一次執行此工具時，系統會提示您輸入 IoT 中樞連接字串。 使用您先前所記下的連接字串。

設定工具以使用您先前下載的模型檔案。 從工具的 [首頁] 中，選取 [ **IoT 隨插即用設定**]，然後按一下 [ **+ 新增 > 本機資料夾**]。 選取您先前建立的 *模型* 資料夾。 然後選取 [ **儲存** ] 以儲存設定。

若要深入瞭解，請參閱 [安裝和使用 Azure IoT explorer](howto-use-iot-explorer.md)。

## <a name="remove-the-resources"></a>移除資源

您可以針對所有 IoT 隨插即用快速入門和教學課程使用 IoT 中樞和 DPS 實例，因此您只需要完成本文中的步驟。 當您完成時，您可以使用下列命令從訂用帳戶中移除它們：

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>後續步驟

現在您已設定環境，您可以嘗試下列其中一個快速入門或教學課程，例如：

> [!div class="nextstepaction"]
> [將範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 ( # A0) ](quickstart-connect-device-node.md)
