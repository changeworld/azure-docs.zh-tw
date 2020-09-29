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
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: e398138f12c38e5235a0004679d9574dbde607db
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446886"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>使用 IoT 中樞傳送雲端到裝置訊息 (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。 [將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-node.md)快速入門會說明如何建立 IoT 中樞、在其中佈建裝置身分識別，以及撰寫模擬的裝置應用程式來傳送裝置到雲端訊息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教學課程是以[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-node.md)為基礎。 這會說明如何：

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。
* 接收裝置上的雲端到裝置訊息。
* 從解決方案後端，要求確認收到從 IoT 中樞傳送到裝置的訊息 (「意見反應」)。

您可在 [IoT 中樞開發人員指南](iot-hub-devguide-messaging.md)中，找到有關雲端到裝置訊息的詳細資訊。

在本教學課程結尾處，您會執行兩個 Node.js 主控台應用程式：

* **SimulatedDevice** 是在[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-node.md)中建立的應用程式修改版本，其可連線到 IoT 中樞，並接收雲端到裝置訊息。

* **SendCloudToDeviceMessage** 會透過 IoT 中樞，將雲端到裝置訊息傳送到模擬裝置應用程式，然後接收其傳遞通知。

> [!NOTE]
> IoT 中樞會透過 Azure IoT 裝置 SDK 為許多裝置平台和語言 (包括 C、Java、Python 及 JavaScript) 提供 SDK 支援。 如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連接到「Azure IoT 中樞」) 的逐步指示，請參閱 [Azure IoT 開發人員中樞](https://azure.microsoft.com/develop/iot)。
>

## <a name="prerequisites"></a>必要條件

* Node.js 10.0.x 版或更新版本。 [準備開發環境](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)描述如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。)

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-simulated-device-app"></a>在模擬的裝置應用程式中接收訊息

在本節中，您會修改在[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-node.md)中建立的模擬裝置應用程式，以接收來自 IoT 中樞的雲端到裝置訊息。

1. 使用文字編輯器開啟 **SimulatedDevice.js** 檔案。 此檔案位於[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-node.md)快速入門內所下載 Node.js 範例程式碼根資料夾的 **iot-hub\Quickstarts\simulated-device** 資料夾中。

2. 向裝置用戶端註冊處理常式，以接收從 IoT 中樞傳送的訊息。 在建立裝置用戶端的那一行後面新增 `client.on` 的呼叫，如下列程式碼片段所示：

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

在此範例中，裝置會叫用 **完整** 的函式，以通知 IoT 中樞它已處理訊息，而且可以安全地從裝置佇列中移除。 如果使用 MQTT 傳輸，則不需要呼叫 **complete**，並可予以省略。 這是 AMQP 和 HTTPS 的必要項。

使用 AMQP 和 HTTPS （而非 MQTT）時，裝置也可以：

* 放棄訊息，這會導致 IoT 中樞將訊息保留在裝置佇列中，以供日後使用。
* 拒絕訊息，這會將訊息從裝置佇列中永久移除。

如果發生導致裝置無法完成、放棄或拒絕訊息的情況，IoT 中樞將會在固定的超時期間之後，將訊息排在佇列中，以便再次傳遞。 基於這個理由，裝置應用程式中的訊息處理邏輯必須具有 *等冪*性，如此一來，多次接收相同的訊息就會產生相同的結果。

如需 IoT 中樞如何處理雲端到裝置訊息的詳細資訊，包括雲端到裝置訊息生命週期的詳細資訊，請參閱 [從 IoT 中樞傳送雲端到裝置的訊息](iot-hub-devguide-messages-c2d.md)。
  
> [!NOTE]
> 如果您使用 HTTPS （而不是 MQTT 或 AMQP）作為傳輸，則 **>deviceclient** 實例會不常檢查來自 IoT 中樞的訊息， (至少每隔25分鐘的時間) 。 如需有關 MQTT、AMQP 和 HTTPS 支援之間差異的詳細資訊，請參閱 [雲端到裝置的通訊指引](iot-hub-devguide-c2d-guidance.md) 和 [選擇通訊協定](iot-hub-devguide-protocols.md)。
>

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

在本文中，您會建立後端服務以透過在[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-node.md)內建立的 IoT 中樞來傳送雲端到裝置訊息。 若要傳送雲端到裝置訊息，則服務需要**服務連線**權限。 根據預設，每個 IoT 中樞都是透過授與此權限且名為**服務**的共用存取原則所建立。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

在本節中，您會建立 Node.js 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。 您需要在[將遙測從裝置傳送至中樞](quickstart-send-telemetry-node.md)快速入門內所新增裝置的裝置識別碼。 您也需要先前在[取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)內複製的 IoT 中樞連接字串。

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

5. 在 **SendCloudToDeviceMessage.js** 檔案中新增下列程式碼。 將 "{iot hub connection string}" 和 "{device id}" 預留位置值取代為先前記下的 IoT 中樞連接字串和裝置識別碼：

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

1. 在 **simulated-device** 資料夾的命令提示字元中，執行下列命令來將遙測傳送至 IoT 中樞並接聽雲端到裝置訊息：

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
   > 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)文章所建議，實作重試原則 (例如指數型輪詢)。
   >

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。
