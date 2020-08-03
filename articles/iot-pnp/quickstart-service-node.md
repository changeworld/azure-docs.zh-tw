---
title: 與連線至 Azure IoT 解決方案 (Node.js) 的 IoT 隨插即用預覽版裝置互動 | Microsoft Docs
description: 使用 Node.js 連線至已連接 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置，並與其互動。
author: elhorton
ms.author: elhorton
ms.date: 07/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9ddca58e166baa1e94fcc3edcfbbc64abd578049
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352565"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>快速入門：與已連線至解決方案的 IoT 隨插即用預覽版裝置互動 (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 隨插即用預覽版讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 Node.js 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您的開發電腦上必須要有 Node.js。 您可以從 [nodejs.org](https://nodejs.org) 下載多個平台的最新建議版本。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

執行下列命令，[透過 IoT 隨插即用支援 安裝 Node 服務 SDK](https://www.npmjs.com/package/azure-iot-digitaltwins-service)：

```cmd/sh
npm i azure-iot-digitaltwins-service
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

執行下列命令，針對您新增至中樞的裝置取得_裝置連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>執行範例裝置

在本快速入門中，您可以使用以 Node.js 撰寫的範例控溫器裝置，作為 IoT 隨插即用裝置。 若要執行範例裝置：

1. 在您選擇的資料夾中開啟終端機視窗。 執行下列命令，將[適用於 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存放庫複製到下列位置：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. 此終端機視窗會作為您的**裝置**終端機。 移至複製存放庫的資料夾，並瀏覽至 /azure-iot-sdk-node/device/samples/pnp 資料夾。 執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

1. 設定_裝置連接字串_：

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 使用下列命令來執行範例控溫器裝置：

    ```cmd/sh
    node simple_thermostat.js
    ```

1. 您會看到訊息，指出裝置已傳送一些資訊並回報其本身的線上狀態。 這些訊息表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。 請勿關閉此終端機，稍後您將需要用來確認服務範例也已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在本快速入門中，您會使用 Node.js 中的範例 IoT 解決方案，與您剛剛設定的範例裝置進行互動。

1. 開啟另一個終端機視窗，作為您的**服務**終端機。 服務 SDK 處於預覽狀態，因此您必須從 [Node SDK 的預覽分支](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh)複製範例：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b public-preview-pnp
    ```

1. 移至這個複製存放庫分支的資料夾，並瀏覽至 /azure-iot-samples-node/digital-twins/samples/service/javascript 資料夾。 執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

1. 設定裝置識別碼的環境變數和 _IoT 中樞連接字串_：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>讀取屬性

1. 當您在**裝置**終端機中執行範例控溫器裝置時，您會看到下列訊息，指出裝置處於線上狀態：

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. 移至**服務**終端機，並使用下列命令執行範例以讀取裝置資訊：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在**服務**終端機輸出中，請注意數位分身的回應。 您會看到已回報裝置的模型識別碼和相關聯的屬性：

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. 下列程式碼片段顯示 get_digital_twin.js 中的程式碼，可取出裝置分身的模型識別碼：

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

在此案例中，會輸出 `Model Id: dtmi:com:example:Thermostat;1`。

### <a name="update-a-writable-property"></a>更新可寫入屬性

1. 在程式碼編輯器中開啟檔案 update_digital_twin_property.js。

1. 檢閱範例程式碼。 您可以了解如何建立 JSON 修補程式，以更新裝置的數位分身。 在此範例中，程式碼會以值 42 取代控溫器的溫度：

    ```javascript
    const patch = [{
        op: 'add',
        path: 'targetTemperature',
        value: '42'
      }]
    ```

1. 在**服務**終端機，使用下列命令執行範例以更新屬性：

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. **服務**終端機輸出會顯示已更新的裝置資訊。 捲動至 `thermostat1` 元件，以查看值為 42 的新 `targetTemperature`：

    ```json
    "modelId": "dtmi:com:example:Thermostat;1",
        "version": 12,
        "properties": {
            "desired": {
                "targetTemperature": "42",
                "$metadata": {
                    "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                    "$lastUpdatedVersion": 5,
                    "targetTemperature": {
                        "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                        "$lastUpdatedVersion": 5
                    }
                },
                "$version": 5
            },
            "reported": {
                "serialNumber": "123abc",
                "maxTempSinceLastReboot": 32.279942997143785,
                "targetTemperature": {
                    "value": "42",
                    "ac": 200,
                    "ad": "Successfully executed patch for targetTemperature",
                    "av": 2
                },
    ```

1. 在您的**裝置**終端機中，您會看到裝置已接收更新：

    ```cmd/sh
    Received an update for targetTemperature: 42
    updated the property
    ```

1. 在您的 **服務**終端機中，執行下列命令以確認屬性已更新：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在**服務**終端機輸出中，在 `thermostat1` 元件下的數位分身回應中，您會看到回報的更新目標溫度。 裝置可能需要一段時間才能完成更新。 重複此步驟，直到裝置處理屬性更新為止：

    ```json
    "$model": "dtmi:com:example:Thermostat;1",
    "targetTemperature": {
      "desiredValue": 42,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch for targetTemperature",
      "lastUpdateTime": "2020-07-09T13:55:30.5062641Z"
    }
    ```

### <a name="invoke-a-command"></a>叫用命令

1. 開啟檔案 invoke_command.js 並且檢閱程式碼。

1. 移至**服務**終端機。 使用下列命令來執行範例，以叫用命令：

    ```cmd/sh
    node invoke_command.js
    ```

1. **服務**終端機中的輸出應該會顯示下列確認：

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. 在**裝置**終端機，您會看到命令已認可：

    ```cmd/sh
    MaxMinReport [object Object]
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用預覽版模型開發人員指南](concepts-developer-guide.md)
