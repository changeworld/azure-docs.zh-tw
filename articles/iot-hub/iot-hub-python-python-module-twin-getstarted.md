---
title: Azure IoT 中心模組識別和模組孿生 (Python)
description: 了解如何使用適用於 Python 的 IoT SDK，建立模組身分識別及更新模組對應項。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757014"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>開始使用 IoT 中心模組識別和模組孿生 (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模組標識和模組孿生](iot-hub-devguide-module-twins.md)類似於 Azure IoT 中心設備標識和設備孿生,但提供更精細的粒度。 當 Azure IoT 中心設備識別和設備孿生使後端應用程式能夠配置設備並提供設備條件的可見性時,模組標識和模組孿生為設備的單個元件提供了這些功能。 在具有多個元件(如基於作業系統的設備或韌體設備)的功能設備上,它們允許為每個元件提供隔離的配置和條件。
>

在本教學結束時,您有三個 Python 應用:

* **CreateModule**,它創建設備標識、模塊標識和相關安全密鑰來連接設備和模組用戶端。

* **更新模組Twin希望屬性**,它向IoT中心發送更新的模組孿生所需屬性。

* **接收模組Twin希望屬性補丁**,它接收您設備上的模組孿生所需屬性修補程式。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中心連接字串

在本文中,您將創建一個後端服務,該服務在標識註冊表中添加設備,然後將模組添加到該設備。 此服務需要**註冊表寫入**許可權(還包括**註冊表讀取**)。 您還可以創建一個服務,將所需屬性添加到新創建的模組的模組孿生。 此服務需要**服務連接**許可權。 儘管存在單獨授予這些許可權的默認共享訪問策略,但在本節中,您可以創建包含這兩種許可權的自定義共用訪問策略。

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>在 IoT 中樞中建立裝置身分識別與模組身分識別

在本節中,您將創建一個 Python 服務應用,在 IoT 中心中的標識註冊表中創建設備標識和模組標識。 設備或模組無法連接到 IoT 中心,除非它在標識註冊表中具有條目。 有關詳細資訊,請參閱瞭解[IoT 中心中的識別註冊表](iot-hub-devguide-identity-registry.md)。 當您執行此主控台應用程式時，它會針對裝置和模組產生唯一的識別碼和金鑰。 當裝置和模組將裝置到雲端的訊息傳送給 IoT 中樞時，裝置和模組會使用這些值來識別自己。 識別碼會區分大小寫。

1. 在指令提示符下,執行以下指令以安裝**azure-iot 集線器**套件:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 在命令提示符下,執行以下命令以安裝**msrest**套件。 您需要此包來捕獲**HTTP 操作錯誤**異常。

    ```cmd/sh
    pip install msrest
    ```

1. 使用文字編輯器,在工作目錄中創建名為**CreateModule.py**的檔案。

1. 將以下代碼添加到 Python 檔案。 將*IoTHubConnectString*替換為在[獲取 IoT 中心連接字串](#get-the-iot-hub-connection-string)中複製的連接字串。

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. 在命令提示符下,執行以下指令:

    ```cmd/sh
    python CreateModule.py
    ```

此應用程式會在 **myFirstDevice** 裝置下方建立識別碼為 **myFirstDevice** 的裝置身分識別，以及識別碼為 **myFirstModule** 的模組身分識別。 (如果標識註冊表中已存在設備或模組 ID,則代碼只需檢索現有設備或模塊資訊。應用顯示每個標識的 ID 和主鍵。

> [!NOTE]
> IoT 中樞身分識別登錄只會儲存裝置和模組身分識別，以啟用對 IoT 中樞的安全存取。 身分識別登錄會儲存裝置識別碼和金鑰，以作為安全性認證使用。 身分識別登錄也會儲存每個裝置的已啟用/已停用旗標，以便您用來停用該裝置的存取權。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 模組身分識別沒有啟用/停用旗標。 有關詳細資訊,請參閱瞭解[IoT 中心中的識別註冊表](iot-hub-devguide-identity-registry.md)。
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>使用 Python 服務 SDK 更新模組孿生

在本節中,您將創建一個 Python 服務應用,用於更新模組孿生所需屬性。

1. 在命令提示符下,執行以下命令以安裝**azure-iot 集線器**包。 如果在上一節中安裝了**azure-iot 集線器**包,則可以跳過此步驟。

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 使用文字編輯器,在工作目錄中創建名為**UpdateModuleTwinDesiredProperties.py**的檔。

1. 將以下代碼添加到 Python 檔案。 將*IoTHubConnectString*替換為在[獲取 IoT 中心連接字串](#get-the-iot-hub-connection-string)中複製的連接字串。

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>取得裝置端的更新

在本節中,您將創建一個 Python 應用,以獲得設備上的模組孿生所需屬性更新。

1. 取得模組連接字串。 在[Azure 門戶](https://portal.azure.com/)中,導覽到 IoT 中心,並在左邊窗格中選擇**IoT 裝置**。 從設備清單中選擇**myFirstDevice**並打開它。 在**模組識別**下,選擇**我的第一個模組**。 複製模組連接字串。 您需要在下面的步驟。

   ![Azure 入口網站模組詳細資料](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. 在指令提示符下,執行以下指令以安裝**azure-iot 裝置**套件:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 使用文字編輯器,在工作目錄中創建名為**ReceiveModuleTwinDesiredPropertiesPatch.py**的檔。

1. 將以下代碼添加到 Python 檔案。 將*ModuleConnectString*替換為步驟 1 中複製的模組連接字串。

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>執行應用程式

在本節中,您將運行 **「接收模組Twin希望屬性」** 設備應用,然後運行**UpdateModuleTwin 希望屬性**服務應用以更新模組所需的屬性。

1. 開啟指令提示並執行裝置應用:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![裝置應用初始輸出](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. 開啟單獨的指令提示符並執行服務應用程式:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    請注意,服務應用輸出中更新的模組孿生中將顯示 **「遙測間隔**」所需屬性:

   ![服務應用輸出](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    相同的屬性顯示在裝置應用輸出中接收的所需屬性修補程式中:

   ![裝置應用輸出顯示所需的屬性修補程式](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>後續步驟

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [開始使用裝置管理](iot-hub-node-node-device-management-get-started.md)

* [開始使用 IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)