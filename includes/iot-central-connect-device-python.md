---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 2eff30333362d461f196972fbaedbeac8f2ae7c9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033824"
---
## <a name="prerequisites"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目︰

* 使用 [自訂應用程式] 範本建立的 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](../articles/iot-central/core/quick-deploy-iot-central.md)。 應用程式必須已在 2020 年 7 月 14 日或之後建立。
* 已安裝 [Python](https://www.python.org/) 3.7 版或更新版本的開發電腦。 您可以在命令列執行 `python --version` 來檢查版本。 Python 適用於多種作業系統。 本教學課程中的指示假設您是在 Windows 命令提示字元中執行 **python** 命令。
* [Microsoft Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) GitHub 存放庫的本機複本，其中包含範例程式碼。 使用此連結來下載存放庫的複本：[下載 ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip)。 接著將檔案解壓縮到本機電腦上的適當位置。

## <a name="review-the-code"></a>檢閱程式碼

在您先前下載的 Microsoft Azure IoT SDK for Python 複本中，以文字編輯器開啟 azure-iot-sdk-python/azure-iot-device/samples/pnp/simple_thermostat.py 檔案。

執行範例以連線到 IoT Central 時，該檔案會使用裝置佈建服務 (DPS) 來註冊裝置並產生連接字串。 此範例會從命令列環境中，取出所需的 DPS 連線資訊。

`main` 函式：

* 使用 DPS 來佈建裝置。 佈建資訊包括模型識別碼。 IoT Central 會使用型號識別碼來識別或產生此裝置的裝置範本。 若要深入了解，請參閱[將裝置與裝置範本相關聯](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)。
* 建立 `Device_client` 物件，並在開啟連線之前設定 `dtmi:com:example:Thermostat;1` 模型識別碼。
* 將 `maxTempSinceLastReboot` 屬性傳送至 IoT Central。
* 建立 `getMaxMinReport` 命令的接聽程式。
* 建立屬性接聽程式，以接聽可寫入的屬性更新。
* 啟動迴圈，每 10 秒傳送一次溫度遙測。

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

`provision_device` 函式會使用 DPS 來佈建裝置，並向 IoT Central 註冊。 函式包含裝置型號識別碼，IoT Central 會用來[將裝置與佈建承載的裝置範本產生關聯](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)：

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener` 函式會處理命令要求、在裝置收到 `getMaxMinReport` 命令時執行 `max_min_handler` 函式，以及執行 `create_max_min_report_response` 函式來產生回應：

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener` 會處理可寫入的屬性更新 (例如 `targetTemperature`)，並產生 JSON 回應：

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

`send_telemetry_from_thermostat` 函式會將遙測訊息傳送至 IoT Central：

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>取得連線資訊

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>執行程式碼

若要執行範例應用程式，請開啟命令列環境，然後瀏覽至 azure-iot-sdk-python/azure-iot-device/samples/pnp 資料夾，其中包含 simple_thermostat.py 範例檔案。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

安裝必要的套件：

```cmd/sh
pip install azure-iot-device
```

執行範例：

```cmd/sh
python simple_thermostat.py
```

下列輸出顯示裝置註冊和連線到 IoT Central。 範例會在開始傳送遙測之前，先傳送 `maxTempSinceLastReboot` 屬性：

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

您可看到裝置如何回應命令和屬性更新：

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```
