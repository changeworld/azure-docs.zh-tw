---
title: 使用 Azure IoT 中樞 (Python) 排程作業 | Microsoft Docs
description: 如何排定 Azure IoT 中樞作業在多個裝置上叫用直接方法。 您可以使用適用於 Python 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式來執行作業。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527396"
---
# <a name="schedule-and-broadcast-jobs-python"></a>排程及廣播作業 (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT 中樞是一項完全受控的服務，可讓後端應用程式建立作業來排定和更新數百萬個裝置，並追蹤作業。  作業可用於下列動作：

* 更新所需屬性
* 更新標籤
* 叫用直接方法

就概念而言，作業會包裝上述其中一個動作，然後針對由裝置對應項 (twin) 查詢所定義的一組裝置，追蹤執行進度。  例如，後端應用程式可以在 10,000 個裝置上使用作業叫用重新啟動方法，此方法由裝置對應項查詢指定並排定在未來執行。  接著，該應用程式可以追蹤每個這些裝置接收和執行重新啟動方法的進度。

從下列文章深入了解這當中的每一項功能：

* 裝置對應項和屬性：[開始使用裝置對應項](iot-hub-python-twin-getstarted.md)和[教學課程：如何使用裝置對應項屬性](tutorial-device-twins.md)

* 直接方法[：IoT 中心開發人員指南 - 直接方法和](iot-hub-devguide-direct-methods.md)[教程：直接方法](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教學課程說明如何：

* 建立具有直接方法的 Python 模擬裝置應用程式，此直接方法會啟用 **lockDoor** 供解決方案後端呼叫。

* 建立 Python 主控台應用程式，此應用程式會使用作業在模擬裝置應用程式中呼叫 **lockDoor** 直接方法，並使用裝置作業來更新所需屬性。

在本教學課程結尾，您將會有兩個 Python 應用程式：

**simDevice.py** 會使用裝置身分識別連線到您的 IoT 中樞，並接收 **lockDoor** 直接方法。

**scheduleJobService.py** 會使用作業在模擬裝置應用程式中呼叫直接方法，並更新裝置對應項的所需屬性。

> [!NOTE]
> **適用於 Python 的 Azure IoT SDK** 不直接支援 [作業]**** 功能。 反之，本教學課程使用非同步執行緒與計時器提供替代方案。 如需進一步的更新，請參閱 [適用於 Python 的 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) 頁面上的 [服務用戶端 SDK]**** 功能清單。
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您建立 Python 主控台應用程式，回應雲端所呼叫的直接方法，可觸發模擬的 **lockDoor** 方法。

1. 在命令提示符下，運行以下命令以安裝**azure-iot 設備**包：

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 使用文字編輯器，在工作目錄中建立新的 **simDevice.py** 檔案。

3. 在 **simDevice.py** 檔案開頭新增下列 `import` 陳述式和變數。 將 `deviceConnectionString` 取代為您上方所建立裝置的連接字串：

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 新增下列函式回呼以處理 **lockDoor** 方法：

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. 新增另一個函式回呼來處理裝置對應項更新：

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. 添加以下代碼以註冊**lockDoor**方法的處理常式。 另包含 `main` 常式：

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. 儲存並關閉 **simDevice.py** 檔案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)一文中所建議，實作重試原則 (例如指數型輪詢)。
>

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

在本文中，您將創建一個後端服務，該服務在設備上調用直接方法並更新設備孿生。 該服務需要**服務連接**許可權才能在設備上調用直接方法。 該服務還需要**註冊表讀取**和**註冊表寫入**許可權來讀取和寫入標識註冊表。 沒有僅包含這些許可權的預設共用訪問策略，因此您需要創建一個許可權。

要創建授予**服務連接**、**註冊表讀取**和**註冊表寫入**許可權的共用訪問策略，並獲取此策略的連接字串，請按照以下步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中打開 IoT 中心。 到達 IoT 中心最簡單的方法是選擇**資源組**，選擇 IoT 中心所在的資源組，然後從資源清單中選擇 IoT 中心。

2. 在 IoT 中心的左側窗格中，選擇**共用訪問策略**。

3. 從策略清單上方的頂部功能表中，選擇 **"添加**"。

4. 在"**添加共用訪問策略"** 窗格中，為策略輸入描述性名稱;在"添加共用訪問策略"窗格中，為策略輸入描述性名稱。例如：*服務和註冊讀寫*。 在 **"許可權**"下，選擇 **"服務連接**"和 **"註冊表寫入**"（當您選擇 **"註冊表寫入**"時，將自動選擇**註冊表讀取**）。 然後選擇 **"創建**"。

    ![演示如何添加新的共用訪問策略](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. 返回"**共用訪問策略"** 窗格，從策略清單中選擇新策略。

6. 在 **"共用便捷鍵**"下，選擇**連接字串的**複製圖示 -- 主鍵並保存該值。

    ![顯示如何擷取連接字串](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

有關 IoT 中心共用訪問策略和許可權的詳細資訊，請參閱[存取控制和許可權](./iot-hub-devguide-security.md#access-control-and-permissions)。

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>排定用於呼叫直接方法及更新裝置對應項 (twin) 屬性的作業

在本節中，您將創建一個 Python 主控台應用，該應用程式使用直接方法在設備上啟動遠端**lockDoor，** 並更新設備孿生所需的屬性。

1. 在命令提示符下，運行以下命令以安裝**azure-iot 集線器**包：

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 使用文字編輯器，在工作目錄中建立新的 **scheduleJobService.py** 檔案。

3. 在**scheduleJobService.py**檔`import`開始時添加以下語句和變數。 將`{IoTHubConnectionString}`預留位置替換為以前在[獲取 IoT 中心連接字串](#get-the-iot-hub-connection-string)中複製的 IoT 中心連接字串。 將`{deviceId}`預留位置替換為您在[IoT 中心註冊新設備時](#register-a-new-device-in-the-iot-hub)註冊的裝置識別碼：

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. 新增下列函式，用來查詢裝置：

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. 新增下列方法來執行呼叫直接方法和裝置對應項的兩個作業：

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. 新增下列程式碼來排程作業及更新作業狀態。 另包含 `main` 常式：

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. 儲存並關閉 **scheduleJobService.py** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在工作目錄的命令提示字元中，執行下列命令以開始接聽重新啟動直接方法：

    ```cmd/sh
    python simDevice.py
    ```

2. 在工作目錄的另一個命令提示字元中，執行下列命令以觸發鎖門作業並更新對應項：
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. 您會在主控台中看到直接方法和裝置對應項更新的裝置回應。

    ![IoT 中心作業示例 1 -- 設備輸出](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT 中心作業示例 2-- 設備輸出](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用作業來排定裝置的直接方法，以及更新裝置對應項 (twin) 的屬性。

要繼續使用 IoT 中心和裝置管理模式（如遠端通過空氣固件更新）入門，請參閱[如何執行固件更新](tutorial-firmware-update.md)。