---
title: 教學課程 - 將一般 Python 用戶端應用程式連線至 Azure IoT Central | Microsoft Docs
description: 本教學課程說明如何以裝置開發人員身分，將執行 Python 用戶端應用程式的裝置與您的 Azure IoT Central 應用程式連線。 您可以匯入裝置功能模型並新增可讓您與連線裝置互動的檢視，來建立裝置範本
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 1be7087e99ca2e4dc605a8d1c6b9821567aecfef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968128"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>教學課程：建立用戶端應用程式並將其連線到您的 Azure IoT Central 應用程式 (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*本文適用於解決方案建置人員和裝置開發人員。*

本教學課程說明如何以裝置開發人員身分，將 Python 用戶端應用程式與您的 Azure IoT Central 應用程式連線。 Python 應用程式會模擬環境感應器裝置的行為。 您可使用範例「裝置功能模型」，在 IoT Central 中建立「裝置範本」。 您可將檢視新增至裝置範本，讓操作員能與裝置互動。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 匯入裝置功能模型來建立裝置範本。
> * 將預設和自訂檢視新增至裝置範本。
> * 發佈裝置範本，並將實際裝置新增至 IoT Central 應用程式。
> * 建立和執行 Python 裝置程式碼，並查看其是否與您的 IoT Central 應用程式連線。
> * 檢視裝置所傳送的模擬遙測。
> * 使用檢視管理裝置屬性。
> * 呼叫同步和非同步命令以控制裝置。

## <a name="prerequisites"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目︰

* 使用 [自訂應用程式] 範本建立的 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。 應用程式必須已在 2020/7/14 或之後建立。
* 已安裝 [Python](https://www.python.org/) 3.7 版或更新版本的開發電腦。 您可以在命令列執行 `python3 --version` 來檢查版本。 Python 適用於多種作業系統。 本教學課程中的指示假設您是在 Windows 命令提示字元中執行 **python3** 命令。

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>建立 Python 應用程式

下列步驟示範如何建立 Python 用戶端應用程式，以連線到您新增至應用程式的實際裝置。 Python 應用程式會模擬實際裝置的行為。

1. 在命令列環境中，瀏覽至先所建立的 `environmental-sensor` 資料夾。

1. 若要安裝必要的程式庫，請執行下列命令：

    ```cmd
    pip install azure-iot-device
    ```

1. 在 `environmental-sensor` 資料夾中，建立名為 **environmental_sensor.py** 的檔案。

1. 在 **environmental_sensor.py** 檔案開頭新增下列 `import` 陳述式：

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. 將下列非同步 `main` 函式和變數宣告新增至該檔案：

    ```python
    async def main():
        # In a production environment, don't store
        # connection information in the code.
        provisioning_host = 'global.azure-devices-provisioning.net'
        id_scope = '{your Scope ID}'
        registration_id = '{your Device ID}'
        symmetric_key = '{your Primary Key}'
  
        delay = 2
  
        # All the remaining code is nested within this main function

    if __name__ == '__main__':
        asyncio.run(main())
    ```

    以您先前記下的值取代 `{your Scope ID}`、`{your Device ID}` 和 `{your Primary Key}` 等預留位置。 在實際的應用程式中，請勿在應用程式中將此資訊進行硬式編碼。

    下列所有函式定義和程式碼都會嵌套在 `main` 函式內。

1. 在 `main` 函式中新增下列兩個函式，以註冊裝置並將其連線到您的 IoT Central 應用程式。 註冊會使用 Azure 裝置佈建服務：

    ```python
        async def register_device():
            provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
                provisioning_host=provisioning_host,
                registration_id=registration_id,
                id_scope=id_scope,
                symmetric_key=symmetric_key,
            )

            registration_result = await provisioning_device_client.register()

            print(f'Registration result: {registration_result.status}')

            return registration_result
  
        async def connect_device():
            device_client = None
            try:
                registration_result = await register_device()
                if registration_result.status == 'assigned':
                    device_client = IoTHubDeviceClient.create_from_symmetric_key(
                        symmetric_key=symmetric_key,
                        hostname=registration_result.registration_state.assigned_hub,
                        device_id=registration_result.registration_state.device_id
                    )
                    # Connect the client.
                    await device_client.connect()
                    print('Device connected successfully')
            finally:
                return device_client
    ```

1. 在 `main` 函式內新增下列函式新增，以將遙測資料傳送至 IoT Central 應用程式：

    ```python
        async def send_telemetry():
            print(f'Sending telemetry from the provisioned device every {delay} seconds')
            while True:
                temp = random.randrange(1, 75)
                humid = random.randrange(30, 99)
                payload = json.dumps(
                    {
                        'temp': temp,
                        'humid': humid
                    })
                msg = Message(payload)
                await device_client.send_message(msg, )
                print(f'Sent message: {msg}')
                await asyncio.sleep(delay)
    ```

    遙測項目的名稱 (`temp` 和 `humid`) 必須符合裝置範本中使用的名稱。

1. 在 `main` 函式內新增下列函式，以處理從 IoT Central 應用程式呼叫的命令：

    ```python
        async def blink_command(request):
            print('Received synchronous call to blink')
            response = MethodResponse.create_from_method_request(
                request,
                status = 200,
                payload = {'description': f'Blinking LED every {request.payload} seconds'}
            )
            await device_client.send_method_response(response)  # send response
            print(f'Blinking LED every {request.payload} seconds')

        async def diagnostics_command(request):
            print('Starting asynchronous diagnostics run...')
            response = MethodResponse.create_from_method_request(
                request,
                status = 202
            )
            await device_client.send_method_response(response)  # send response
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Sending property update to confirm command completion')
            await device_client.patch_twin_reported_properties(
                {
                    'rundiagnostics':
                    {
                        'value': f'Diagnostics run complete at {datetime.datetime.today()}.'
                    }
                })

        async def turnon_command(request):
            print('Turning on the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        async def turnoff_command(request):
            print('Turning off the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        commands = {
            'blink': blink_command,
            'rundiagnostics': diagnostics_command,
            'turnon': turnon_command,
            'turnoff': turnoff_command,
        }

        # Define behavior for handling commands
        async def command_listener():
            while True:
                method_request = await device_client.receive_method_request()  # Wait for commands
                await commands[method_request.name](method_request)
    ```

    命令的名稱 (`blink`、`turnon`、`turnoff`和 `rundiagnostics`) 必須符合裝置範本中使用的名稱。

    目前，IoT Central 不會使用裝置功能模型中定義的回應結構描述。 對於同步命令，回應承載可以是任何有效的 JSON。 針對非同步命令，裝置應會立即傳回 202 回應，然後在工作完成時，接著傳回報告屬性更新。 報告屬性更新的格式為：

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    操作員可以在命令歷程記錄中檢視回應承載。

1. 在 `main` 函式內新增下列函式，以處理從 IoT Central 應用程式傳送的屬性更新。 裝置為了回應[可寫入屬性更新](concepts-telemetry-properties-commands.md#writeable-property-types)而傳送的訊息必須包含 `av` 和 `ac` 欄位。 `ad` 欄位是選擇性的：

    ```python
        async def name_setting(value, version):
            await asyncio.sleep(1)
            print(f'Setting name value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'name' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        async def brightness_setting(value, version):
            await asyncio.sleep(5)
            print(f'Setting brightness value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'brightness' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        settings = {
            'name': name_setting,
            'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
            while True:
                patch = await device_client.receive_twin_desired_properties_patch() # blocking
                to_update = patch.keys() & settings.keys()
                await asyncio.gather(
                    *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
                )
    ```

    當操作員在 IoT Central 應用程式中設定可寫入屬性時，應用程式會使用裝置對應項所需的屬性，將值傳送至裝置。 裝置接著會使用裝置對應項報告屬性來回應。 當 IoT Central 收到報告屬性值時，其會以 [已同步] 狀態更新屬性檢視。

    屬性的名稱 (`name` 和 `brightness`) 必須符合裝置範本中使用的名稱。

1. 在 `main` 函式內新增下列函式，以控制應用程式：

    ```python
        # Define behavior for halting the application
        def stdin_listener():
            while True:
                selection = input('Press Q to quit\n')
                if selection == 'Q' or selection == 'q':
                    print('Quitting...')
                    break
  
        device_client = await connect_device()
  
        if device_client is not None and device_client.connected:
            print('Send reported properties on startup')
            await device_client.patch_twin_reported_properties(
                {
                    'state': 'true',
                    'processorArchitecture': 'ARM',
                    'swVersion': '1.0.0'
                })
            tasks = asyncio.gather(
                send_telemetry(),
                command_listener(),
                twin_patch_listener(),
            )

            # Run the stdin listener in the event loop
            loop = asyncio.get_running_loop()
            user_finished = loop.run_in_executor(None, stdin_listener)

            # Wait for user to indicate they are done listening for method calls
            await user_finished

            # Cancel tasks
            tasks.add_done_callback(lambda r: r.exception())
            tasks.cancel()
            await device_client.disconnect()
  
        else:
            print('Device could not connect')
    ```

1. 儲存 **environmental_sensor.py** 檔案。

## <a name="run-your-python-application"></a>執行 Python 應用程式

若要啟動裝置用戶端應用程式，請在命令列環境中執行下列命令：

```cmd
python3 environmental_sensor.py
```

您可以看到裝置連線到您的 Azure IoT Central 應用程式，並開始傳送遙測資料：

![執行用戶端應用程式](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

您可看到裝置如何回應命令和屬性更新：

![觀察用戶端應用程式](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>檢視未經處理資料

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>後續步驟

如果您想要繼續進行這套 IoT Central 教學課程並深入了解如何建置 IoT Central 解決方案，請參閱：

> [!div class="nextstepaction"]
> [建立閘道裝置範本](./tutorial-define-gateway-device-type.md)

身為裝置開發人員，您現在已了解如何使用 Python 建立裝置的基本概念，以下是一些建議的後續步驟：

* 閱讀[什麼是裝置範本？](./concepts-device-templates.md)，以深入了解在您實作裝置程式碼時裝置範本的角色。
* 如需深入了解如何向 IoT Central 註冊裝置，以及 IoT Central 如何保護裝置連線，請參閱[連線至 Azure IoT Central](./concepts-get-connected.md)。