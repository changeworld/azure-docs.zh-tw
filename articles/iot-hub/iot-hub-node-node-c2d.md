---
title: 使用 Azure IoT 中樞傳送雲端到裝置訊息 (Node) | Microsoft Docs
description: 如何使用適用於 Node.js 的 Azure IoT SDK，將雲端到裝置訊息從 Azure IoT 中樞傳送至裝置。 您可以修改模擬裝置應用程式，以接收雲端到裝置訊息，也可以修改後端應用程式，以傳送雲端到裝置訊息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 8071ddbc5f6073598daf0a08d359ccd19ccd1e4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110798"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>使用 IoT 中心 （Node.js） 發送雲到設備的消息

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。 將[遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-node.md)快速入門顯示如何創建 IoT 中心、在其中預配設備標識以及編寫發送設備到雲消息的類比設備應用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程基於[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-node.md)。 這會說明如何：

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。
* 接收裝置上的雲端到裝置訊息。
* 從解決方案後端請求從 IoT 中心發送到設備的消息的傳遞確認 （*回饋*）。

您可以在[IoT 中心開發人員指南](iot-hub-devguide-messaging.md)中找到有關雲到設備消息的詳細資訊。

在本教學課程結尾處，您會執行兩個 Node.js 主控台應用程式：

* **類比設備**，在[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-node.md)中創建的應用程式的修改版本，該中心連接到 IoT 中心並接收雲到設備的消息。

* **SendCloudToDeviceMessage**，它通過 IoT 中心向類比設備應用發送雲到設備的消息，然後接收其傳遞確認。

> [!NOTE]
> IoT 中心通過 Azure IoT 設備 SDK 對許多設備平臺和語言（包括 C、JAVA、Python 和 JAVAscript）具有 SDK 支援。 如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連接到「Azure IoT 中樞」) 的逐步指示，請參閱 [Azure IoT 開發人員中樞](https://azure.microsoft.com/develop/iot)。
>

## <a name="prerequisites"></a>Prerequisites

* Node.js 版本 10.0.x 或更高版本。 [準備您的開發環境](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。)

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-simulated-device-app"></a>在模擬的裝置應用程式中接收訊息

在本節中，您將修改在[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-node.md)以接收來自 IoT 中心的雲到設備消息時創建的類比設備應用。

1. 使用文字編輯器，打開**類比設備.js**檔。 此檔位於 Node.js 示例資料夾中的**iot-hub_Quickstarts_類比設備**資料夾中，您在[從設備向 IoT 中心快速啟動發送遙測](quickstart-send-telemetry-node.md)資料中下載了該代碼。

2. 使用設備用戶端註冊處理常式以接收從 IoT 中心發送的消息。 將調用添加到`client.on`創建設備用戶端的行之後，如以下程式碼片段：

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    在此示例中，設備調用**完整**函數來通知 IoT 中心它已處理消息。 如果您使用的是 MQTT 傳輸，則不需要調用**完成**，並且可以省略。 HTTPS 和 AMQP 需要它。
  
   > [!NOTE]
   > 如果您使用 HTTPS 而不是使用 MQTT 或 AMQP 作為傳輸，**DeviceClient** 執行個體將不會經常 (頻率低於每隔 25 分鐘) 檢查「IoT 中樞」是否有訊息。 如需有關 MQTT、AMQP 和 HTTPS 支援之間的差異以及「IoT 中樞」節流的詳細資訊，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide-messaging.md)。
   >

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

在本文中，您將創建一個後端服務，通過在[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-node.md)中創建的 IoT 中心發送雲到設備消息。 要發送雲到設備的消息，您的服務需要**服務連接**許可權。 預設情況下，每個 IoT 中心都使用授予此許可權的名為**服務的**共用訪問策略創建。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

在本節中，您會建立 Node.js 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。 您需要在["將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-node.md)快速啟動"中添加的設備的裝置識別碼。 您還需要以前在[獲取 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)。

1. 建立新的名為 **sendcloudtodevicemessage**的空資料夾。 在 **sendcloudtodevicemessage** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```shell
    npm init
    ```

2. 在命令提示字元中，於 **sendcloudtodevicemessage** 資料夾中執行下列命令以安裝 **azure-iothub** 套件：

    ```shell
    npm install azure-iothub --save
    ```

3. 使用文字編輯器，在 **sendcloudtodevicemessage** 資料夾中建立 **SendCloudToDeviceMessage.js** 檔案。

4. 在 **SendCloudToDeviceMessage.js** 檔案開頭新增下列 `require` 陳述式：

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. 在 **SendCloudToDeviceMessage.js** 檔案中新增下列程式碼。 將"[iot 中心連接字串]"和"[設備 id]"預留位置值替換為您之前注意到的 IoT 中心連接字串和裝置識別碼：

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 加入下列函式來將作業結果列印至主控台︰

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 加入下列函式來將傳遞意見反應訊息列印至主控台︰

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 加入下列程式碼，當裝置收到雲端到裝置訊息時，會將訊息傳送至您的裝置，並處理意見反應訊息︰

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. 儲存並關閉 **SendCloudToDeviceMessage.js** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在**類比設備**資料夾中的命令提示符下，運行以下命令以向 IoT 中心發送遙測資料並偵聽雲到設備的消息：

    ```shell
    node SimulatedDevice.js
    ```

    ![執行模擬裝置應用程式](./media/iot-hub-node-node-c2d/receivec2d.png)

2. 在命令提示字元中，於 **sendcloudtodevicemessage** 資料夾中執行下列命令，以傳送雲端到裝置訊息並等候通知的意見反應︰

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![執行應用程式以傳送雲端到裝置命令](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > 為簡單起見，本教程不實現任何重試策略。 在生產環境程式碼中，您應該如[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)文章所建議，實作重試原則 (例如指數型輪詢)。
   >

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。
