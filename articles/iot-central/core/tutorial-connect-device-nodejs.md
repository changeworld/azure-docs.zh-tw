---
title: 教學課程 - 將一般 Node.js 用戶端應用程式連線至 Azure IoT Central | Microsoft Docs
description: 本教學課程說明如何以裝置開發人員身分，將執行 Node.js 用戶端應用程式的裝置與您的 Azure IoT Central 應用程式連線。 您可以匯入裝置功能模型並新增可讓您與連線裝置互動的檢視，來建立裝置範本
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: a8c5d9479585c0a519d0ad05a4d73f3f15b21287
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758193"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>教學課程：建立用戶端應用程式並將其連線到您的 Azure IoT Central 應用程式 (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*本文適用於解決方案建置人員和裝置開發人員。*

本教學課程說明如何以裝置開發人員身分，將 Node.js 用戶端應用程式與您的 Azure IoT Central 應用程式連線。 Node.js 應用程式會模擬環境感應器裝置的行為。 您可使用範例「裝置功能模型」  ，在 IoT Central 中建立「裝置範本」  。 您可將檢視新增至裝置範本，讓操作員能與裝置互動。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 匯入裝置功能模型來建立裝置範本。
> * 將預設和自訂檢視新增至裝置範本。
> * 發佈裝置範本，並將實際裝置新增至 IoT Central 應用程式。
> * 建立和執行 Node.js 裝置程式碼，並查看其是否與您的 IoT Central 應用程式連線。
> * 檢視裝置所傳送的模擬遙測。
> * 使用檢視管理裝置屬性。
> * 呼叫同步和非同步命令以控制裝置。

## <a name="prerequisites"></a>Prerequisites

若要完成這篇文章中的步驟，您需要下列項目︰

* 使用 [自訂應用程式]  範本建立的 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
* 已安裝 [Node.js](https://nodejs.org/) 10.0.0 版或更新版本的開發電腦。 您可以在命令列執行 `node --version` 來檢查版本。 本教學課程中的指示假設您是在 Windows 命令提示字元中執行 **node** 命令。 不過，您可以在許多其他作業系統上使用 Node.js。

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

下列步驟示範如何建立 Node.js 用戶端應用程式，以連線到您新增至應用程式的實際裝置。 此 Node.js 應用程式會模擬實際裝置的行為。

1. 在命令列環境中，瀏覽至先所建立的 `environmental-sensor` 資料夾。

1. 若要初始化您的 Node.js 專案並安裝必要的相依性，請執行下列命令 - 當您執行 `npm init` 時，接受所有預設選項：

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. 在 `environmental-sensor` 資料夾中，建立名為 **environmentalSensor.js** 的檔案。

1. 在 **environmentalSensor.js** 檔案開頭新增下列 `require` 陳述式：

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. 將下列變數宣告新增至該檔案：

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    以您先前記下的值取代 `{your Scope ID}`、`{your Device ID}` 和 `{your Primary Key}` 等預留位置。 在此範例中，我們會將 `targetTemperature` 初始化為零，您可以採用裝置的目前讀數或裝置對應項的值。

1. 若要將模擬遙測傳送至 Azure IoT Central 應用程式，請將下列函式新增至檔案：

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    遙測項目的名稱 (`temp` 和 `humid`) 必須符合裝置範本中使用的名稱。

1. 若要將裝置對應項屬性傳送至 Azure IoT 中心應用程式，請將下列函式新增至檔案：

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central 使用裝置對應項來同步處理裝置與 IoT Central 應用程式之間的屬性值。 裝置屬性值會使用裝置對應項報告屬性。 可寫入屬性同時使用裝置對應項報告和所需屬性。

1. 若要定義及處理您的裝置所回應的可寫入屬性，請新增下列程式碼：

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    當操作員在 IoT Central 應用程式中設定可寫入屬性時，應用程式會使用裝置對應項所需的屬性，將值傳送至裝置。 裝置接著會使用裝置對應項報告屬性來回應。 當 IoT Central 收到報告屬性值時，其會以 [已同步]  狀態更新屬性檢視。

    屬性的名稱 (`name` 和 `brightness`) 必須符合裝置範本中使用的名稱。

1. 新增下列程式碼，以處理從 IoT Central 應用程式傳送的命令：

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    命令的名稱 (`blink`、`turnon`、`turnoff`和 `rundiagnostics`) 必須符合裝置範本中使用的名稱。

    目前，IoT Central 不會使用裝置功能模型中定義的回應結構描述。 對於同步命令，回應承載可以是任何有效的 JSON。 針對非同步命令，裝置應會立即傳回 202 回應，然後在工作完成時，接著傳回報告屬性更新。 報告屬性更新的格式為：

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    操作員可以在命令歷程記錄中檢視回應承載。

1. 新增下列程式碼以完成對 Azure IoT Central 的連線，並連結用戶端程式碼中的函式：

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>執行 Node.js 應用程式

若要啟動裝置用戶端應用程式，請在命令列環境中執行下列命令：

```cmd/sh
node environmentalSensor.js
```

您可以看到裝置連線到您的 Azure IoT Central 應用程式，並開始傳送遙測資料：

![執行用戶端應用程式](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

您可看到裝置如何回應命令和屬性更新：

![觀察用戶端應用程式](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>後續步驟

身為裝置開發人員，您現在已了解如何使用 Node.js 建立裝置的基本概念，以下是一些建議的後續步驟：

- 若要了解如何將實際裝置連線至 IoT Central，請參閱[將 MXChip IoT DevKit 裝置連線到 Azure IoT Central 應用程式](./howto-connect-devkit.md)。
- 如需深入了解如何向 IoT Central 註冊裝置，以及 IoT Central 如何保護裝置連線，請參閱[連線至 Azure IoT Central](./concepts-get-connected.md)。

如果您想要繼續進行這套 IoT Central 教學課程並深入了解如何建置 IoT Central 解決方案，請參閱：

> [!div class="nextstepaction"]
> [建立閘道裝置範本](./tutorial-define-gateway-device-type.md)
