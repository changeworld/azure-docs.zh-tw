---
title: 將 IoT 隨插即用預覽的範例 Python 裝置程式碼連線至 Azure IoT 中樞 | Microsoft Docs
description: 使用 Python 建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352577"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>快速入門：將範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞 (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具檢視其所傳送的遙測資料。 範例應用程式是以 Python 撰寫的，並隨附於適用於 Python 的 Azure IoT 中樞裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您的開發機器上必須要有 Python 3.7。 您可以從 [python.org](https://www.python.org/) 下載多個平台的最新建議版本。您可以使用下列命令來檢查 Python 版本：  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Azure IoT 總管

若要在本快速入門的第二個部分與範例裝置互動，您可以使用 **Azure IoT 總管**工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 您也可以使用 Azure IoT 總管工具來尋找 IoT 中樞連接字串。

執行下列命令，針對您新增至中樞的裝置取得_裝置連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>設定您的環境

此套件會發佈為 PIP，以供公開預覽重新整理之用。 套件版本應該是最新版或 `2.1.4`

在您的本機 Python 環境中安裝檔案，如下所示：

```cmd/sh
pip install azure-iot-device
```

複製 Python SDK IoT 存放庫，並簽出 **master**：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>執行範例裝置

azure-iot-sdk-python\azure-iot-device\samples\pnp 資料夾包含適用於 IoT 隨插即用裝置的範例程式碼。 本快速入門會使用 pnp_thermostat.py 檔案。 此範例程式碼會實作與 IoT 隨插即用相容的裝置，並使用 Azure IoT Python 裝置用戶端程式庫。

建立名為 **IOTHUB_DEVICE_CONNECTION_STRING** 的環境變數，來儲存您先前記下的裝置連接字串。

在文字編輯器中開啟 **pnp_thermostat.py** 檔案。 請注意該檔案如何：

1. 定義唯一代表[控溫器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)的單一裝置對應項模型識別碼 (DTMI)。 使用者必須知道 DTMI，並取決於裝置實作的案例。 針對目前的範例，此模型代表控溫器，具有與監視溫度相關聯的遙測、屬性和命令。

1. 具有函式可定義命令處理常式實作。 您可以撰寫這些處理常式來定義裝置回應命令要求的方式。

1. 具有函式可定義命令回應。 您會建立命令回應函式，以將回應傳回給您的 IoT 中樞。

1. 定義輸入鍵盤接聽程式函式以讓您結束應用程式。

1. 具有 **main** 函式。 **main** 函式：

    1. 使用裝置 SDK 來建立裝置用戶端，並連線到您的 IoT 中樞。

    1. 更新屬性。 我們使用的模型 (**控溫器**) 會將 `targetTemperature` 和 `maxTempSinceLastReboot` 定義為控溫器的兩個屬性，因此這是我們會使用的屬性。 屬性會使用 `device_client` 上定義的 `patch_twin_reported_properties` 方法進行更新。

    1. 使用 **execute_command_listener** 函式開始接聽命令要求。 此函式會針對來自服務的命令設定接聽程式進行接聽。 當您設定接聽程式時，要提供 `method_name`、`user_command_handler` 和 `create_user_response_handler`。 
        - `user_command_handler` 函式會定義裝置收到命令時應執行的動作。 舉例來說，如果您的鬧鐘關閉，收到此命令的效果就是您起床。 請將此視為叫用命令的「效果」。
        - `create_user_response_handler` 函式會在命令執行成功時，建立要傳送至 IoT 中樞的回應。 舉例來說，如果您的鬧鐘關閉，您按下延遲來回應，這是對於服務的反應。 請將此視為您提供給服務的回覆。 您可以在入口網站中檢視此回應。

    1. 開始傳送遙測。 **pnp_send_telemetry** 是在 pnp_methods.py 檔案中定義。 範例程式碼會使用迴圈，每 8 秒呼叫此函式。

    1. 停用所有接聽程式和工作，並且在您按下 **Q** 或 **q** 時結束迴圈。

現在您已看過程式碼，請使用下列命令來執行範例：

```cmd/sh
python pnp_thermostat.py
```

您會看到下列輸出，這表示裝置正在將遙測資料傳送至中樞，且現在已可接收命令和屬性更新：

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [與已連線至解決方案的 IoT 隨插即用預覽版裝置互動](quickstart-service-python.md)
