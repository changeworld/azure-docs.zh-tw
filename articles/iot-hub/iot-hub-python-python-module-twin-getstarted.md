---
title: 'Azure IoT 中樞模組身分識別和模組對應項 (Python) '
description: 了解如何使用適用於 Python 的 IoT SDK，建立模組身分識別及更新模組對應項。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92139397"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>開始使用 IoT 中樞模組身分識別和模組對應項 (Python) 

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模組身分識別和模組 twins](iot-hub-devguide-module-twins.md) 類似于 Azure IoT 中樞裝置身分識別和裝置 twins，但可提供更細微的細微性。 雖然 Azure IoT 中樞裝置身分識別和裝置 twins 可讓後端應用程式設定裝置，並提供裝置條件的可見度，模組身分識別和模組 twins 會為裝置的個別元件提供這些功能。 在具有多個元件的可用裝置上，例如以作業系統為基礎的裝置或固件裝置，它們允許針對每個元件進行隔離的設定和條件。
>

在本教學課程結尾處，您會有三個 Python 應用程式：

* **CreateModule**，它會建立裝置身分識別、模組身分識別和相關聯的安全性金鑰，以連接您的裝置和模組用戶端。

* **UpdateModuleTwinDesiredProperties**，會將已更新的模組對應項所需屬性傳送至您的 IoT 中樞。

* **ReceiveModuleTwinDesiredPropertiesPatch**，它會在您的裝置上接收模組對應項所需的屬性修補程式。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

在本文中，您會建立後端服務，以在身分識別登錄中新增裝置，然後將模組新增至該裝置。 這種服務需要登錄 **寫入** 許可權 (也包括登錄 **讀取**) 。 您也會建立服務，將所需屬性新增至新建立模組的模組對應項。 此服務需要 **service connect** 許可權。 雖然有預設的共用存取原則會個別授與這些許可權，但在本節中，您會建立包含這兩個許可權的自訂共用存取原則。

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>在 IoT 中樞中建立裝置身分識別與模組身分識別

在本節中，您會建立 Python 服務應用程式，以在 IoT 中樞的身分識別登錄中建立裝置身分識別和模組身分識別。 裝置或模組無法連線到 IoT 中樞，除非它在身分識別登錄中有專案。 如需詳細資訊，請參閱 [瞭解 IoT 中樞內的身分識別登錄](iot-hub-devguide-identity-registry.md)。 當您執行此主控台應用程式時，它會針對裝置和模組產生唯一的識別碼和金鑰。 當裝置和模組將裝置到雲端的訊息傳送給 IoT 中樞時，裝置和模組會使用這些值來識別自己。 識別碼會區分大小寫。

1. 在命令提示字元中，執行下列命令以安裝 **azure-iot-hub** 套件：

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 在命令提示字元中執行下列命令，以安裝 **msrest** 套件。 您需要此套件來攔截 **HTTPOperationError** 例外狀況。

    ```cmd/sh
    pip install msrest
    ```

1. 使用文字編輯器，在您的工作目錄中建立名為 **CreateModule.py** 的檔案。

1. 將下列程式碼新增至您的 Python 檔案。 將 *YourIoTHubConnectionString* 取代為您在「 [取得 IoT 中樞」連接字串](#get-the-iot-hub-connection-string)中複製的連接字串。

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

1. 在命令提示字元中，執行下列命令：

    ```cmd/sh
    python CreateModule.py
    ```

此應用程式會在 **myFirstDevice** 裝置下方建立識別碼為 **myFirstDevice** 的裝置身分識別，以及識別碼為 **myFirstModule** 的模組身分識別。  (如果身分識別登錄中已經有裝置或模組識別碼，則程式碼只會抓取現有的裝置或模組資訊。 ) 應用程式會顯示每個身分識別的識別碼和主要金鑰。

> [!NOTE]
> IoT 中樞身分識別登錄只會儲存裝置和模組身分識別，以啟用對 IoT 中樞的安全存取。 身分識別登錄會儲存裝置識別碼和金鑰，以作為安全性認證使用。 身分識別登錄也會儲存每個裝置的已啟用/已停用旗標，以便您用來停用該裝置的存取權。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 模組身分識別沒有啟用/停用旗標。 如需詳細資訊，請參閱 [瞭解 IoT 中樞內的身分識別登錄](iot-hub-devguide-identity-registry.md)。
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>使用 Python 服務 SDK 更新模組對應項

在本節中，您會建立 Python 服務應用程式，以更新模組對應項所需屬性。

1. 在命令提示字元中執行下列命令，以安裝 **azure iot 中樞** 套件。 如果您在上一節中安裝了 **azure iot 中樞** 套件，則可以略過此步驟。

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 使用文字編輯器，在您的工作目錄中建立名為 **UpdateModuleTwinDesiredProperties.py** 的檔案。

1. 將下列程式碼新增至您的 Python 檔案。 將 *YourIoTHubConnectionString* 取代為您在「 [取得 IoT 中樞」連接字串](#get-the-iot-hub-connection-string)中複製的連接字串。

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

在本節中，您會建立 Python 應用程式，以在您的裝置上取得模組對應項所需的屬性更新。

1. 取得您的模組連接字串。 在 [Azure 入口網站](https://portal.azure.com/)中，流覽至您的 iot 中樞，然後在左窗格中選取 [ **iot 裝置** ]。 從裝置清單中選取 [ **myFirstDevice** ]，然後開啟它。 在 [ **模組**身分識別] 下，選取 [ **>myfirstdevice**]。 複製模組連接字串。 您必須在下列步驟中使用它。

   ![Azure 入口網站模組詳細資料](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. 在命令提示字元中，執行下列命令以安裝 **azure-iot-device** 套件：

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 使用文字編輯器，在您的工作目錄中建立名為 **ReceiveModuleTwinDesiredPropertiesPatch.py** 的檔案。

1. 將下列程式碼新增至您的 Python 檔案。 將 *YourModuleConnectionString* 取代為您在步驟1中複製的模組連接字串。

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

在本節中，您會執行 **ReceiveModuleTwinDesiredPropertiesPatch** 裝置應用程式，然後執行 **UpdateModuleTwinDesiredProperties** 服務應用程式來更新您的模組所需屬性。

1. 開啟命令提示字元並執行裝置應用程式：

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![裝置應用程式初始輸出](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. 開啟個別的命令提示字元，並執行服務應用程式：

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    請注意， **>desired.config.telemetryinterval** 所需屬性會出現在服務應用程式輸出中已更新的模組對應項中：

   ![服務應用程式輸出](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    相同的屬性會出現在您的裝置應用程式輸出所收到的所需屬性修補程式中：

   ![裝置應用程式輸出顯示所需的屬性修補程式](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>後續步驟

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [開始使用裝置管理](iot-hub-node-node-device-management-get-started.md)

* [開始使用 IoT Edge](../iot-edge/quickstart-linux.md)