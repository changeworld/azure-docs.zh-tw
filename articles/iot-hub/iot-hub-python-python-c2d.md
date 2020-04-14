---
title: 使用 Azure IoT 中樞傳送雲端到裝置訊息 (Python) | Microsoft Docs
description: 如何使用適用於 Python 的 Azure IoT SDK，將雲端到裝置訊息從 Azure IoT 中樞傳送到裝置。 您可以修改模擬裝置應用程式，以接收雲端到裝置訊息，也可以修改後端應用程式，以傳送雲端到裝置訊息。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.openlocfilehash: 0c3b35eeed85dd3a1c44dea6ec46203eb812e1e8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257811"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>使用 IoT 中樞傳送雲端到裝置訊息 (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。 將[遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)快速入門顯示如何創建 IoT 中心、在其中預配設備標識以及編寫發送設備到雲端訊息的模擬設備應用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

這個教學基於[將遙測資料從裝置傳送到 IoT 中心](quickstart-send-telemetry-python.md)。 這會說明如何：

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。

* 接收裝置上的雲端到裝置訊息。

您可以在[IoT 中心開發人員指南](iot-hub-devguide-messaging.md)中找到有關雲端到設備訊息的詳細資訊。

在本教學課程結尾，您會執行兩個 Python 主控台應用程式：

* **SimulatedDevice.py**,在[將遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)中創建的應用程式的修改版本,該中心連接到 IoT 中心並接收雲到設備的消息。

* **SendCloudToDeviceMessage.py**,通過IoT中心向類比設備應用發送雲到設備的消息。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定,該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-simulated-device-app"></a>在模擬的裝置應用程式中接收訊息

在本節中，您會建立一個 Python 主控台應用程式，來模擬裝置並接收來自 IoT 中樞的雲端到裝置訊息。

1. 從工作目錄中的指令提示,安裝 Python 的**Azure IoT 中心裝置 SDK**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 使用文字編輯器,創建名為**SimulatedDevice.py**的檔。

1. 在 **SimulatedDevice.py** 檔案開頭新增下列 `import` 陳述式和變數：

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. 將下列程式碼新增至 **SimulatedDevice.py** 檔案。 將`{deviceConnectionString}`佔位符值取代為您在[「將遙測資料從裝置傳送到 IoT 中心](quickstart-send-telemetry-python.md)快速啟動」中建立的裝置的裝置連接字串:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. 新增下列函式，將接收的訊息列印至主控台：

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. 新增下列程式碼，將用戶端初始化，並等候以接收雲端到裝置訊息：

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
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. 新增下列 main 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. 儲存並關閉 **SimulatedDevice.py** 檔案。

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中心連接字串

在本文中,您將創建一個後端服務,透過在[將遙測資料從裝置發送到 IoT 中心](quickstart-send-telemetry-python.md)中建立的 IoT 中心發送雲端到裝置訊息。 要發送雲到設備的消息,您的服務需要**服務連接**許可權。 默認情況下,每個 IoT 中心都使用授予此許可權的名為**服務的**共享存取策略創建。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

在本節中，您會建立一個 Python 主控台應用程式，將雲端到裝置訊息傳送到模擬裝置應用程式。 您需要在[「將遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)快速啟動」中添加的裝置的設備 ID。 您必須在[取得 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)。

1. 在工作目錄中,打開命令提示符並安裝 Python 的**Azure IoT 中心服務 SDK。**

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. 使用文字編輯器,建立名為**SendCloudToDeviceMessage.py**的檔案。

1. 在 **SendCloudToDeviceMessage.py** 檔案開頭新增下列 `import` 陳述式和變數：

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. 將下列程式碼新增至 **SendCloudToDeviceMessage.py** 檔案。 將`{iot hub connection string}`與`{device id}`占位符值取代為您之前指出的 IoT 中心連接字串和裝置 ID:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. 新增以下代碼以傳送訊息:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. 新增下列 main 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. 儲存並關閉 **SendCloudToDeviceMessage.py** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在工作目錄中的命令提示符處,執行以下指令以偵聽雲到設備的消息:

    ```shell
    python SimulatedDevice.py
    ```

    ![執行模擬裝置應用程式](./media/iot-hub-python-python-c2d/device-1.png)

1. 在工作目錄開啟新的指令提示,並執行以下指令以傳送雲到裝置的消息:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![執行應用程式以傳送雲端到裝置命令](./media/iot-hub-python-python-c2d/service.png)

1. 請注意設備收到的消息。

    ![已接收的訊息](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。
