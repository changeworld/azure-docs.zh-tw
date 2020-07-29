---
title: 開始使用 Azure IoT 中樞裝置管理 (Node) | Microsoft Docs
description: 如何使用 IoT 中樞裝置管理來起始遠端裝置重新開機。 您可以使用適用於 Node.js 的 Azure IoT SDK，實作模擬裝置應用程式 (包含直接方法) 和服務應用程式 (叫用直接方法)。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt
ms.openlocfilehash: a7814970d4ffb051ab54c965d27394f925d79e59
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732336"
---
# <a name="get-started-with-device-management-nodejs"></a>開始使用裝置管理 (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教學課程說明如何：

* 使用 [Azure 入口網站](https://portal.azure.com)來建立 IoT 中樞，且在 IoT 中樞建立裝置身分識別。

* 建立模擬裝置應用程式，其包含可將該裝置重新開機的直接方法。 直接方法是從雲端叫用。

* 建立 Node.js 主控台應用程式，可透過您的 IoT 中樞在模擬的裝置應用程式中呼叫重新啟動直接方法。

在本教學課程結尾處，您會有兩個 Node.js 主控台應用程式：

* **dmpatterns_getstarted_device.js**，它會以先前建立的裝置識別連線到您的 IoT 中樞，接收重新啟動直接方法，模擬實體重新啟動，並報告上一次重新啟動的時間。

* **dmpatterns_getstarted_service.js**，它會在模擬裝置應用程式上呼叫直接方法，顯示回應，並顯示更新的報告屬性。

## <a name="prerequisites"></a>必要條件

* Node.js 10.0.x 版或更新版本。 [準備開發環境](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)描述如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您可：

* 建立 Node.js 主控台應用程式，以回應雲端所呼叫的直接方法

* 觸發模擬裝置重新啟動

* 使用報告屬性來啟用裝置對應項查詢，以識別裝置及其上次重新啟動時間

1. 建立稱為 **manageddevice** 的空資料夾。  在 **manageddevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。  接受所有預設值：

    ```cmd/sh
    npm init
    ```

2. 在命令提示字元中，於 **manageddevice** 資料夾中執行下列命令來安裝 **azure-iot-device** 裝置 SDK 套件和 **azure-iot-device-mqtt** 套件：

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. 使用文字編輯器，在 **manageddevice** 資料夾中建立 **dmpatterns_getstarted_device.js** 檔案。

4. 在 **dmpatterns_getstarted_device.js** 檔案開頭新增下列 'require' 陳述式：

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. 新增 **connectionString** 變數，並用它來建立**用戶端**執行個體。  將 `{yourdeviceconnectionstring}` 預留位置值取代為先前於[在 IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)內複製的裝置連接字串。  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. 新增下列函式以在裝置上實作直接方法

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. 開啟您 IoT 中樞的連線，並啟動直接方法接聽程式︰

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. 儲存並關閉 **dmpatterns_getstarted_device.js**檔案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)一文中所建議，實作重試原則 (例如指數型輪詢)。

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端重新啟動

在本節中，您會建立 Node.js 主控台應用程式，此應用程式會使用直接方法起始遠端重新開機。 應用程式使用裝置對應項查詢來探索該裝置的上次重新開機時間。

1. 建立名為 **triggerrebootondevice** 的空白資料夾。 在命令提示字元中，於 **triggerrebootondevice** 資料夾中使用下列命令來建立 package.json 檔案。 接受所有預設值：

    ```cmd/sh
    npm init
    ```

2. 在 **triggerrebootondevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 裝置 SDK 套件以及 **azure-iot-device-mqtt** 套件：

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. 使用文字編輯器，在 **triggerrebootondevice** 資料夾中建立 **dmpatterns_getstarted_service.js** 檔案。

4. 在 **dmpatterns_getstarted_service.js** 檔案開頭新增下列 'require' 陳述式：

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. 新增下列變數宣告，並將 `{iothubconnectionstring}` 預留位置值取代為先前在[取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)內複製的 IoT 中樞連接字串：

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. 新增下列函式來叫用裝置方法，以重新啟動目標裝置︰

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. 新增下列函式來查詢裝置並取得上次重新啟動時間︰

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. 新增下列函式來呼叫可觸發重新啟動直接方法，並查詢上次重新啟動時間的函式︰

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. 儲存並關閉 **dmpatterns_getstarted_service.js** 檔案。

## <a name="run-the-apps"></a>執行應用程式

您現在可開始執行應用程式。

1. 在 **manageddevice** 資料夾中，於命令提示字元中執行下列命令以開始接聽重新啟動直接方法。

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. 在命令提示字元中，於 **triggerrebootondevice** 資料夾執行下列命令以觸發裝置對應項的遠端重新啟動，以及查詢裝置對應項來尋找上次重新啟動時間。

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. 您會在主控台中看到重新開機直接方法的裝置回應和重新開機狀態。

   下列顯示服務所傳送重新開機直接方法的裝置回應：

   ![manageddevice 應用程式輸出](./media/iot-hub-node-node-device-management-get-started/device.png)

   下列顯示服務觸發重新開機，並輪詢裝置對應項以取得上次重新開機時間：

   ![triggerrebootondevice 應用程式輸出](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
