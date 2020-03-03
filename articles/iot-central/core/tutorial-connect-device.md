---
title: 教學課程 - 將一般 Node.js 用戶端應用程式連線至 Azure IoT Central | Microsoft Docs
description: 本教學課程說明如何以裝置開發人員身分，將執行 Node.js 用戶端應用程式的裝置與您的 Azure IoT Central 應用程式連線。 您可以匯入裝置功能模型並新增可讓您與連線裝置互動的檢視，來建立裝置範本
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624334"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>教學課程：建立並將 Node.js 用戶端應用程式連線到您的 Azure IoT Central 應用程式 (Node.js)

本教學課程說明如何以裝置開發人員身分，將 Node.js 用戶端應用程式與您的 Azure IoT Central 應用程式連線。 Node.js 應用程式會模擬實際裝置的行為。 您可以使用範例_裝置功能模型_，讓環境感應器裝置在 IoT Central 中建立_裝置範本_。 您可以將檢視新增至裝置範本，讓您將裝置遙測資料視覺化、管理裝置屬性，以及使用命令來控制裝置。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 匯入裝置功能模型來建立裝置範本。
> * 將預設和自訂檢視新增至裝置範本。
> * 發佈裝置範本，並將實際裝置新增至 IoT Central 應用程式。
> * 建立和執行 Node.js 裝置程式碼，並查看其是否與您的 IoT Central 應用程式連線。
> * 檢視裝置所傳送的模擬遙測。
> * 使用檢視管理裝置屬性。
> * 呼叫命令來控制裝置。

## <a name="prerequisites"></a>Prerequisites

若要完成這篇文章中的步驟，您需要下列項目︰

* 使用**自訂應用程式**範本建立 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
* 已安裝 [Node.js](https://nodejs.org/) 10.0.0 版或更新版本的開發電腦。 您可以在命令列執行 `node --version` 來檢查版本。 Node.js 適用於多種作業系統。 本教學課程中的指示假設您是在 Windows 命令提示字元中執行 **node** 命令。 您可以在各種不同的作業系統上使用 Node.js。

## <a name="create-a-device-template"></a>建立裝置範本

在本機電腦上建立名為 `environmental-sensor` 的資料夾。

下載[環境感應器功能模型](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON 檔案，並儲存在 `environmental-sensor` 資料夾中。

請在文字編輯器中，將 `{YOUR_COMPANY_NAME_HERE}` 的兩個執行個體以您下載的 `EnvironmentalSensorInline.capabilitymodel.json` 檔案中的公司名稱取代。

在您的 Azure IoT Central 應用程式中，匯入 `EnvironmentalSensorInline.capabilitymodel.json` 裝置功能模型檔案，來建立稱為*環境感應器*的裝置範本：

![具有匯入裝置功能模型的裝置範本](./media/tutorial-connect-device/device-template.png)

裝置功能模型包含兩個介面：標準**裝置資訊**介面和自訂**環境感應器**介面。 **環境感應器**介面會定義下列功能：

| 類型 | 顯示名稱 | 描述 |
| ---- | ------------ | ----------- |
| 屬性 | 裝置狀態     | 裝置的狀態。 有線上/離線兩個狀態可用。 |
| 屬性 | 客戶名稱    | 目前正在操作裝置的客戶名稱。 |
| 屬性 | 亮度等級 | 裝置上的光線亮度等級。 可以指定為1 (高)、2 (中)、3 (低)。 |
| 遙測 | 溫度 | 裝置目前的溫度。 |
| 遙測 | 溼度    | 裝置目前的溼度。 |
| Command | blink          | 開始在指定的時間間隔內閃爍 LED。 |
| Command | turnon         | 開啟裝置上的 LED 燈。 |
| Command | turnoff        | 關閉上的 LED 燈。 |
| Command | rundiagnostics | 此命令會啟動診斷執行。 |

若要自訂如何在 IoT Central 應用程式中顯示**裝置狀態** 屬性，請選取裝置範本中的 [自訂]  。 展開 [裝置狀態]  項目，輸入「Online」  作為 **True 名稱**，並輸入「Offline」  作為 **False 名稱**。 接著儲存變更：

![自訂裝置範本](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>建立檢視

檢視可讓您與連線到 IoT Central 應用程式的裝置互動。 例如，您可以擁有顯示遙測資料的檢視、顯示內容的檢視，以及可讓您編輯可寫入和雲端屬性的檢視。 檢視是裝置範本的一部分。

若要將一些預設檢視新增至 **環境感應器**裝置範本，請瀏覽至您的裝置範本，選取 [檢視]  ，然後選取 [產生預設檢視]  圖格。 請確定**概觀**和**關於**的狀態為**開啟**，然後選取 [產生預設的儀表板檢視]  。 您現在已在範本中定義了兩個預設檢視。

**環境感應器** 介面包含兩個可寫入的屬性 - **客戶名稱**和**亮度等級**。 若要建立檢視，您可以使用來編輯這些屬性：

1. 選取 [檢視]  ，再選取 [編輯裝置和雲端資料]  圖格。

1. 輸入「屬性」  作為表單名稱。

1. 選取 [亮度層級]  和 [客戶名稱]  屬性。 然後選取 [新增區段]  。

1. 儲存您的變更。

![新增檢視以啟用屬性編輯](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>發佈範本

您必須先發佈範本，才能將裝置新增到使用**環境感應器**裝置範本的 IoT Central 應用程式。

在裝置範本中，選取 [發佈]  。 在顯示要發佈之變更的面板上，選取 [發佈]  。

若要檢查範本是否已可供使用，請瀏覽至 IoT Central 應用程式中的 [裝置]  頁面。 [裝置]  區段會顯示應用程式中已發佈的裝置清單：

![裝置頁面上的已發佈範本](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>新增真實裝置

在您的 Azure IoT Central 應用程式中，將實際裝置新增至您在上一節中建立的裝置範本：

1. 在 [裝置]  頁面中，選取 [環境感應器] 裝置範本  。

1. 選取 [+ 新增]  。

1. 請確定 **模擬**狀態為**關閉**。 然後選取 [建立]  。

按一下裝置名稱，然後選取 [連線]  。 記下 [裝置連線]  頁面上的裝置連線資訊，包括 [識別碼範圍]  、[裝置識別碼]  和 [主要金鑰]  。 建立裝置程式碼時，您會需要這些值：

![裝置連線資訊](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

下列步驟示範如何建立 Node.js 用戶端應用程式，以實作您新增至應用程式的實際裝置。 此 Node.js 應用程式會模擬實際裝置的行為。

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

1. 若要將遙測資料傳送至 Azure IoT Central 應用程式，請將下列函式新增至檔案：

    ```javascript
    // Send device measurements.
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

1. 若要將裝置屬性傳送至 Azure IoT 中心應用程式，請將下列函式新增至檔案：

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. 若要定義及處理您的裝置所回應的可寫入屬性，請新增下列程式碼：

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. 新增下列程式碼，以處理從 IoT Central 應用程式傳送的命令：

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. 新增下列程式碼以完成對 Azure IoT Central 的連線，並連結用戶端程式碼中的函式：

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
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

![執行用戶端應用程式](media/tutorial-connect-device/run-application.png)

身為 Azure IoT Central 應用程式的操作者，您可以：

* 在 [概觀]  頁面上，檢視裝置所傳送的遙測資料：

    ![檢視遙測](media/tutorial-connect-device/view-telemetry.png)

* 在 [屬性]  頁面上更新可寫入的屬性值：

    ![更新屬性](media/tutorial-connect-device/update-properties.png)

    ![更新屬性裝置](media/tutorial-connect-device/update-properties-device.png)

* 呼叫 [命令]  頁面中的命令：

    ![呼叫 blink 命令](media/tutorial-connect-device/call-command.png)

    ![呼叫 blink 命令裝置](media/tutorial-connect-device/call-command-device.png)

* 在 [關於]  頁面上，檢視裝置屬性：

    ![檢視屬性](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>後續步驟

若要深入了解裝置功能模型以及如何建立自己的裝置範本，請繼續參閱操作指南：

> [!div class="nextstepaction"]
> [定義新的 IoT 裝置類型](./howto-set-up-template.md)
