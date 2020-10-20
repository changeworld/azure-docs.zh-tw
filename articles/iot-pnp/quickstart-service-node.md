---
title: 與連線至 Azure IoT 解決方案的 IoT 隨插即用裝置互動 (Node.js) | Microsoft Docs
description: 使用 Node.js 連線至已連接 Azure IoT 解決方案的 IoT 隨插即用裝置，並與其互動。
author: elhorton
ms.author: elhorton
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: a6ade8d44e6c751f45849743c66d0a34075943b4
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946122"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-nodejs"></a>快速入門：與已連線至解決方案的 IoT 隨插即用裝置互動 (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 隨插即用讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 Node.js 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要完成本快速入門，您的開發電腦上必須要有 Node.js。 您可以從 [nodejs.org](https://nodejs.org) 下載多個平台的最新建議版本。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用範例程式碼複製 SDK 存放庫

從 [Node SDK 存放庫](https://github.com/Azure/azure-iot-sdk-node)複製範例。 在您選擇的資料夾中開啟終端機視窗。 執行下列命令，以複製[適用於 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存放庫：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>執行範例裝置

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要深入了解範例設定，請參閱[範例讀我檔案](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)。

在本快速入門中，您可使用以 Node.js 撰寫的範例控溫器裝置，作為 IoT 隨插即用裝置。 若要執行範例裝置：

1. 開啟終端機視窗，並瀏覽至本機資料夾，此資料夾中包含從 GitHub 複製的適用於 Node.js 存放庫的 Microsoft Azure IoT SDK。

1. 此終端機視窗會作為您的**裝置**終端機。 移至複製存放庫的資料夾，並瀏覽至 /azure-iot-sdk-node/device/samples/pnp 資料夾。 執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

1. 使用下列命令來執行範例控溫器裝置：

    ```cmd/sh
    node simple_thermostat.js
    ```

1. 您會看到訊息，指出裝置已傳送一些資訊並回報其本身的線上狀態。 這些訊息表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。 請勿關閉此終端機，稍後您將需要用來確認服務範例也已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在 [設定 IoT 隨插即用的環境快速入門和教學課程](set-up-environment.md)，您建立了兩個環境變數，以設定連線至 IoT 中樞和裝置的範例：

* **IOTHUB_CONNECTION_STRING**：您先前記下的 IoT 中樞連接字串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在本快速入門中，您會使用 Node.js 中的範例 IoT 解決方案，與您剛剛設定的範例裝置進行互動。

1. 開啟另一個終端機視窗，作為您的**服務**終端機。

1. 在複製的 Node SDK 存放庫中，瀏覽至 */azure-iot-sdk-node/service/samples/javascript* 資料夾。 執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>讀取屬性

1. 當您在**裝置**終端機中執行範例控溫器裝置時，您會看到下列訊息，指出裝置處於線上狀態：

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. 移至**服務**終端機，並使用下列命令執行範例以讀取裝置資訊：

    ```cmd/sh
    node twin.js
    ```

1. 在**服務**終端機輸出中，請注意裝置對應項的回應。 您會看到已回報裝置的模型識別碼和相關聯的屬性：

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. 下列程式碼片段顯示 twin.js 中的程式碼，可取出裝置分身的模型識別碼：

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

在此案例中，會輸出 `Model Id: dtmi:com:example:Thermostat;1`。

> [!NOTE]
> 這些服務範例會使用 **IoT 中樞服務用戶端**中的 **Registry** 類別。 若要深入了解 API (包括數位對應項 API)，請參閱[服務開發人員指南](concepts-developer-guide-service.md)。

### <a name="update-a-writable-property"></a>更新可寫入屬性

1. 在程式碼編輯器中開啟檔案 twin.js。

1. 請參閱範例程式碼，其中會顯示兩種更新裝置對應項的方式。 若要使用第一種方式，請修改 `twinPatch` 變數，如下所示：

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature` 屬性會在控溫器裝置型號中定義為可寫入的屬性。

1. 在**服務**終端機，使用下列命令執行範例以更新屬性：

    ```cmd/sh
    node twin.js
    ```

1. 在您的**裝置**終端機中，您會看到裝置已接收更新：

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. 在您的 **服務**終端機中，執行下列命令以確認屬性已更新：

    ```cmd/sh
    node twin.js
    ```

1. 在**服務**終端機輸出中，您會在 ¬reported` 屬性區段中看到回報的更新目標溫度。 裝置可能需要一段時間才能完成更新。 重複此步驟，直到裝置處理屬性更新為止：

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>叫用命令

1. 開啟檔案 device_method.js 並檢閱程式碼。

1. 移至**服務**終端機。 使用下列命令來執行範例，以叫用命令：

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. **服務**終端機中的輸出應該會顯示下列確認：

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. 在**裝置**終端機，您會看到命令已認可：

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](concepts-developer-guide-device-csharp.md)
