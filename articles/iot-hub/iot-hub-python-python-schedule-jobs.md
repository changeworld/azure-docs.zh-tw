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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
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

* 直接方法： [IoT 中樞開發人員指南-直接方法](iot-hub-devguide-direct-methods.md)和[教學課程：直接方法](quickstart-control-device-python.md)

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

## <a name="prerequisites"></a>先決條件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您建立 Python 主控台應用程式，回應雲端所呼叫的直接方法，可觸發模擬的 **lockDoor** 方法。

1. 在命令提示字元中，執行下列命令來安裝**azure iot 裝置**套件：

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

6. 新增下列程式碼以註冊**lockDoor**方法的處理常式。 另包含 `main` 常式：

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

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

在本文中，您會建立後端服務，以在裝置上叫用直接方法，並更新裝置對應項。 服務需要**服務 connect**許可權，才能在裝置上呼叫直接方法。 服務也需要登錄**讀取**和登錄**寫入**許可權，才能讀取和寫入身分識別登錄。 沒有僅包含這些許可權的預設共用存取原則，因此您需要建立一個。

若要建立共用存取原則，以授與**服務**連線、登錄**讀取**和登錄**寫入**許可權，以及取得此原則的連接字串，請遵循下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com)中開啟您的 IoT 中樞。 若要進入 IoT 中樞，最簡單的方法是選取 [**資源群組**]，選取您的 iot 中樞所在的資源群組，然後從資源清單中選取您的 iot 中樞。

2. 在 IoT 中樞的左側窗格中，選取 [**共用存取原則**]。

3. 從原則清單上方的頂端功能表中，選取 [**新增**]。

4. 在 [**新增共用存取原則**] 窗格中，輸入原則的描述性名稱;例如： *serviceAndRegistryReadWrite*。 在 **[許可權** **]** 底下，選取 [服務連線及登錄**寫入**] （當您選取 [登錄**寫入**] 時，會自動選取 [登錄**讀取**]） 然後選取 [建立]  。

    ![示範如何新增共用存取原則](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. 回到 [**共用存取原則**] 窗格，從原則清單中選取您的新原則。

6. 在 [**共用存取金鑰**] 底下，選取 [**連接字串-主要金鑰**] 的複製圖示，然後儲存值。

    ![顯示如何擷取連接字串](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

如需 IoT 中樞共用存取原則和許可權的詳細資訊，請參閱[存取控制和許可權](./iot-hub-devguide-security.md#access-control-and-permissions)。

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>排定用於呼叫直接方法及更新裝置對應項 (twin) 屬性的作業

在本節中，您會建立 Python 主控台應用程式，以使用直接方法在裝置上起始遠端**lockDoor** ，也會更新裝置對應項所需的屬性。

1. 在命令提示字元中，執行下列命令來安裝**azure iot 中樞**套件：

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 使用文字編輯器，在工作目錄中建立新的 **scheduleJobService.py** 檔案。

3. 在 scheduleJobService.py 檔案`import`的開頭新增下列語句和變數。 **scheduleJobService.py** 將`{IoTHubConnectionString}`預留位置取代為您先前在[取得 iot 中樞連接字串](#get-the-iot-hub-connection-string)中所複製的 iot 中樞連接字串。 以您`{deviceId}`在在[IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)中註冊的裝置識別碼取代預留位置：

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

    ![IoT 中樞作業範例 1--裝置輸出](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT 中樞作業範例 2--裝置輸出](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用作業來排定裝置的直接方法，以及更新裝置對應項 (twin) 的屬性。

若要繼續開始使用 IoT 中樞和裝置管理模式（例如遠端透過空中固件更新），請參閱[如何執行固件更新](tutorial-firmware-update.md)。