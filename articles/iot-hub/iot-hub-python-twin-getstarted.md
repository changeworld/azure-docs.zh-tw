---
title: 開始使用 Azure IoT 中樞裝置對應項 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置對應項來新增標籤，然後使用 IoT 中樞查詢。 您可以使用適用於 Python 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式，以新增標籤和執行 IoT 中樞查詢。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87875467"
---
# <a name="get-started-with-device-twins-python"></a>開始使用裝置對應項 (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程結尾，您將會有兩個 Python 主控台應用程式：

* **AddTagsAndQuery.py**，這是 Python 後端應用程式，可新增標籤和查詢裝置對應項。

* **ReportConnectivity.py**，這是會模擬裝置的 Python 應用程式，此裝置會以稍早建立的裝置身分識別連線到您的 IoT 中樞，並報告其連線狀況。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您會建立 Python 主控台應用程式，以將位置中繼資料新增至與您的 **{裝置識別碼}** 相關聯的裝置對應項。 接著，它會選取位於 Redmond 的裝置來查詢儲存在 IoT 中樞的裝置對應項，再查詢會報告行動電話連線的對應項。

1. 在您的工作目錄中，開啟命令提示字元並安裝**適用于 Python 的 Azure IoT 中樞服務 SDK**。

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. 使用文字編輯器，建立新的 **AddTagsAndQuery.py** 檔案。

3. 新增下列程式碼，從服務 SDK 匯入必要的模組：

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. 加入下列程式碼。 將 `[IoTHub Connection String]` 取代為在[取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)中複製的 IoT 中樞連接字串。 將取代 `[Device Id]` 為您在[IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)中註冊的裝置識別碼。
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. 將下列程式碼新增至 **AddTagsAndQuery.py** 檔案：

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    **IoTHubRegistryManager**物件會公開從服務與裝置 twins 進行互動所需的所有方法。 程式碼會先初始化**IoTHubRegistryManager**物件，然後更新**DEVICE_ID**的裝置對應項，最後執行兩項查詢。 第一個只會選取位於**Redmond43**工廠的裝置 twins，第二個則會調整查詢，只選取也透過行動電話通訊網路連線的裝置。

6. 在 **AddTagsAndQuery.py** 結尾處新增下列程式碼來實作 **iothub_service_sample_run** 函式：

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. 使用下列命令執行應用程式：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    如果是查詢所有位於 **Redmond43** 中的裝置，您在結果中會看到一個裝置，而如果查詢將結果限於使用行動電話網路的裝置，則您不會看到任何裝置。

    ![顯示 Redmond 中所有裝置的第一個查詢](./media/iot-hub-python-twin-getstarted/service-1.png)

在下一節，您將建立一個裝置應用程式，以報告連線資訊並變更上一節的查詢結果。

## <a name="create-the-device-app"></a>建立裝置應用程式

在本節中，您會建立 Python 主控台應用程式，以您的 **{裝置識別碼}** 連接到您的中樞，然後更新其裝置對應項的報告屬性，以包含使用行動電話通訊網路所連線的資訊。

1. 從工作目錄中的命令提示字元，安裝**適用于 Python 的 Azure IoT 中樞裝置 SDK**：

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 使用文字編輯器，建立新的 **ReportConnectivity.py** 檔案。

3. 新增下列程式碼，從裝置 SDK 匯入必要的模組：

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. 加入下列程式碼。 將 `[IoTHub Device Connection String]` 預留位置值取代為您在[IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)中所複製的裝置連接字串。

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. 將下列程式碼新增至 **ReportConnectivity.py** 檔案來實作裝置對應項功能：

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    **IoTHubModuleClient**物件會公開從裝置與裝置 twins 互動所需的所有方法。 先前的程式碼在初始化**IoTHubModuleClient**物件之後，會抓取您裝置的裝置對應項，並以連線資訊更新其報告屬性。

6. 在 **ReportConnectivity.py** 結尾處新增下列程式碼來實作 **iothub_client_sample_run** 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. 執行裝置應用程式︰

    ```cmd/sh
    python ReportConnectivity.py
    ```

    您應該會看到 [裝置對應項報告屬性已更新] 的確認。

    ![從裝置應用程式更新回報的屬性](./media/iot-hub-python-twin-getstarted/device-1.png)

8. 現在，裝置已回報其連線資訊，它應該會出現在這兩個查詢中。 請返回並再次執行查詢：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    這一次，您的 **{裝置識別碼}** 應該會出現在這兩個查詢結果中。

    ![服務應用程式上的第二個查詢](./media/iot-hub-python-twin-getstarted/service-2.png)

    在您的裝置應用程式中，您會看到已收到服務應用程式所傳送之所需屬性對應項修補程式的確認。

    ![在裝置應用程式上接收所需屬性](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫模擬裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也已經了解如何使用登錄來查詢此資訊。

使用下列資源來了解如何：

* 利用[開始使用 IoT 中樞](quickstart-send-telemetry-python.md)教學課程，傳送裝置的遙測資料。

* 使用裝置對應項的所需屬性搭配[使用所需的屬性來設定裝置](tutorial-device-twins.md)教學課程來設定裝置。

* 透過[使用直接方法](quickstart-control-device-python.md)教學課程，以互動方式 (，例如從使用者控制的應用程式開啟風扇) 。
