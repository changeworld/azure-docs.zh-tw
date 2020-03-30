---
title: 使用 Azure IoT 中樞傳送雲端到裝置訊息 (Python) | Microsoft Docs
description: 如何使用適用於 Python 的 Azure IoT SDK，將雲端到裝置訊息從 Azure IoT 中樞傳送到裝置。 您可以修改模擬裝置應用程式，以接收雲端到裝置訊息，也可以修改後端應用程式，以傳送雲端到裝置訊息。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110428"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>使用 IoT 中樞傳送雲端到裝置訊息 (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。 將[遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)快速入門顯示如何創建 IoT 中心、在其中預配設備標識以及編寫發送設備到雲消息的類比設備應用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程基於[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)。 這會說明如何：

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。

* 接收裝置上的雲端到裝置訊息。

* 從解決方案後端請求從 IoT 中心發送到設備的消息的傳遞確認 （*回饋*）。

您可以在[IoT 中心開發人員指南](iot-hub-devguide-messaging.md)中找到有關雲到設備消息的詳細資訊。

在本教學課程結尾，您會執行兩個 Python 主控台應用程式：

* **SimulatedDevice.py**，在[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)中創建的應用程式的修改版本，該中心連接到 IoT 中心並接收雲到設備的消息。

* **SendCloudToDeviceMessage.py**，它通過 IoT 中心向類比設備應用發送雲到設備的消息，然後接收其傳遞確認。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-simulated-device-app"></a>在模擬的裝置應用程式中接收訊息

在本節中，您會建立一個 Python 主控台應用程式，來模擬裝置並接收來自 IoT 中樞的雲端到裝置訊息。

1. 使用文字編輯器，建立 **SimulatedDevice.py** 檔案。

2. 在 **SimulatedDevice.py** 檔案開頭新增下列 `import` 陳述式和變數：

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. 將下列程式碼新增至 **SimulatedDevice.py** 檔案。 將"[設備連接String]"預留位置值替換為您在"[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)快速啟動"中創建的設備的設備連接字串：

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 新增下列函式，將接收的訊息列印至主控台：

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. 新增下列程式碼，將用戶端初始化，並等候以接收雲端到裝置訊息：

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. 新增下列 main 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. 儲存並關閉 **SimulatedDevice.py** 檔案。

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

在本文中，您將創建一個後端服務，通過在[將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)中創建的 IoT 中心發送雲到設備消息。 要發送雲到設備的消息，您的服務需要**服務連接**許可權。 預設情況下，每個 IoT 中心都使用授予此許可權的名為**服務的**共用訪問策略創建。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

在本節中，您會建立一個 Python 主控台應用程式，將雲端到裝置訊息傳送到模擬裝置應用程式。 您需要在["將遙測資料從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)快速啟動"中添加的設備的裝置識別碼。 您還需要以前在[獲取 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)。

1. 使用文字編輯器，建立 **SendCloudToDeviceMessage.py** 檔案。

2. 在 **SendCloudToDeviceMessage.py** 檔案開頭新增下列 `import` 陳述式和變數：

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. 將下列程式碼新增至 **SendCloudToDeviceMessage.py** 檔案。 將"[iot 中心連接字串]"和"[設備 id]"預留位置值替換為您之前注意到的 IoT 中心連接字串和裝置識別碼：

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. 新增下列函式，將意見反應訊息列印至主控台：

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. 加入下列程式碼，當裝置收到雲端到裝置訊息時，會將訊息傳送至您的裝置，並處理意見反應訊息︰

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. 新增下列 main 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. 儲存並關閉 **SendCloudToDeviceMessage.py** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 開啟命令提示字元並安裝**適用於 Python 的 Azure IoT 中樞裝置 SDK**。

    ```shell
    pip install azure-iothub-device-client
    ```

2. 在命令提示字元中，執行下列命令，以接聽雲端到裝置訊息：

    ```shell
    python SimulatedDevice.py
    ```

    ![執行模擬裝置應用程式](./media/iot-hub-python-python-c2d/simulated-device.png)

3. 開啟新的命令提示字元並安裝**適用於 Python 的 Azure IoT 裝中樞服務 SDK**。

    ```shell
    pip install azure-iothub-service-client
    ```

4. 在命令提示字元中，執行下列命令，以傳送雲端到裝置訊息並等候訊息意見反應：

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![執行應用程式以傳送雲端到裝置命令](./media/iot-hub-python-python-c2d/send-command.png)

5. 請注意裝置所接收的訊息。

    ![已接收的訊息](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。
