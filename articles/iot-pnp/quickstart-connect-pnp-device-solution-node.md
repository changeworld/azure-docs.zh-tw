---
title: 與連線至 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置互動 | Microsoft Docs
description: 使用 Node.js 連線至已連接 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置，並與其互動。
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550667"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>快速入門：與已連線至解決方案的 IoT 隨插即用預覽版裝置互動 (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 隨插即用預覽版讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 Node.js 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您的開發電腦上必須要有 Node.js。 您可以從 [nodejs.org](https://nodejs.org) 下載多個平台的最新建議版本。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_ (請記下以供後續使用)：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>執行範例裝置

在本快速入門中，您會使用以 Node.js 撰寫的範例環境感應器，作為 IoT 隨插即用裝置。 下列指示說明如何安裝和執行裝置：

1. 在您選擇的目錄中開啟終端機視窗。 執行下列命令，將[適用於 Node.js 的 Azure IoT 範例](https://github.com/azure-samples/azure-iot-samples-node) GitHub 存放庫複製到下列位置：

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. 此終端機視窗此時會作為您的_裝置_終端機。 移至複製存放庫的資料夾，並瀏覽至 **/azure-iot-samples-node/digital-twins/Quickstarts/Device** 資料夾。 執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

1. 設定_裝置連接字串_：

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 使用下列命令來執行範例：

    ```cmd/sh
    node sample_device.js
    ```

1. 您會看到訊息，指出裝置已傳送一些資訊並回報其本身的線上狀態。 這表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。 請勿關閉此終端機，稍後您將需要用它來確認服務範例也已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在本快速入門中，您會使用 Node.js 中的範例 IoT 解決方案，與範例裝置進行互動。

1. 開啟另一個終端機視窗 (這將是您的_服務_終端機)。 移至複製存放庫的資料夾，並瀏覽至 **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 資料夾。 執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

1. 設定 _IoT 中樞連接字串_，以允許服務與其連線：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>讀取屬性

1. 當您在終端機上連接_裝置_時，您會看到下列訊息，指出裝置處於線上狀態：

    ```cmd/sh
    reported state property as online
    ```

1. 在 **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 資料夾中，開啟 **get_digital_twin.js** 檔案。 將 `<DEVICE_ID_GOES_HERE>` 預留位置取代為您的裝置識別碼，並儲存檔案。

1. 移至_服務_終端機，並使用下列命令執行範例以讀取裝置資訊：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在_服務_終端機輸出中，捲動至 `environmentalSensor` 元件。 您會看到 `state` 屬性已回報為_線上_狀態：

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>更新可寫入屬性

1. 開啟檔案 **update_digital_twin_property.js**。

1. 在檔案的開頭處，有一組以大寫預留位置定義的常數。 請將 `<DEVICE_ID_GOES_HERE>` 預留位置取代為您實際的裝置識別碼，並以下列值更新其餘常數，然後儲存檔案：

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. 移至_服務_終端機，並使用下列命令執行範例以更新屬性：

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. _服務_終端機輸出會顯示已更新的裝置資訊。 捲動至 `environmentalSensor` 元件，以查看新的亮度值 42。

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. 移至您的_裝置_終端機，您會看到裝置已接收更新：

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. 回到您的_服務_終端機並執行下列命令，以再次取得裝置資訊，確認屬性已更新。
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. 在_服務_終端機輸出的 `environmentalSensor` 元件底下，您會看到回報的更新亮度值。 注意：裝置可能需要一段時間才能完成更新。 您可以重複此步驟，直到裝置實際處理屬性更新為止。
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>叫用命令

1. 開啟檔案 **invoke_command.js**。

1. 在檔案開頭處，將 `<DEVICE_ID_GOES_HERE>` 預留位置取代為您的實際裝置識別碼。 請下列值更新其餘常數，然後儲存檔案：

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. 移至_服務_終端機。 使用下列命令來執行範例，以叫用命令：

    ```cmd/sh
    node invoke_command.js
    ```

1. _服務_終端機中的輸出應會顯示下列確認：

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. 移至_裝置_終端機，您會看到命令已認可：

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
