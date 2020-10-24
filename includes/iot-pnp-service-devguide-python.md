---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: bef7807c0df580a6763a69619cdaa3d9d29f72e6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521360"
---
您也可以使用下列資源：

- [Python SDK 參考檔](/python/api/azure-iot-hub/azure.iot.hub?preserve-view=true&view=azure-python)
- [服務用戶端範例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [數位 Twins 範例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>IoT 中樞服務用戶端範例

本節說明使用 IoT 中樞服務用戶端和 **IoTHubRegistryManager** 和 **CloudToDeviceMethod** 類別的 Python 範例。 您可以使用 **IoTHubRegistryManager** 類別，透過裝置 twins 與裝置狀態進行互動。 您也可以使用 **IoTHubRegistryManager** 類別來查詢 IoT 中樞內的 [裝置註冊](../articles/iot-hub/iot-hub-devguide-query-language.md) 。 您可以使用 **CloudToDeviceMethod** 類別在裝置上呼叫命令。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。 在程式碼片段中， `device_id` 變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-device-twin-and-model-id"></a>取得裝置對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的裝置對應項和模型識別碼：

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>更新裝置對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 此範例會顯示您在更新它之前，需要如何取得對應項 `etag` 。 屬性是在裝置的預設元件中定義：

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 此範例會顯示您在更新它之前，需要如何取得對應項 `ETag` 。 屬性定義于 **thermostat1** 元件中：

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

針對元件中的屬性，屬性修補程式如下列範例所示：

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>呼叫命令

下列程式碼片段說明如何叫用 `getMaxMinReport` 在預設元件中定義的命令：

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **thermostat1** 元件中：

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>IoT 中樞數位對應項範例

您可以使用 **DigitalTwinClient** 類別，利用數位 twins 與裝置狀態互動。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。

`device_id`變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-digital-twin-and-model-id"></a>取得數位對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的數位對應項和模型識別碼：

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>更新數位對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 屬性是在裝置的預設元件中定義：

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 屬性定義于 **thermostat1** 元件中：

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>呼叫命令

下列程式碼片段說明如何叫用 `getMaxMinReport` 在預設元件中定義的命令：

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **thermostat1** 元件中：

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>讀取裝置遙測

IoT 隨插即用裝置會將 DTDL 模型中定義的遙測資料傳送至 IoT 中樞。 根據預設，IoT 中樞會將遙測路由傳送至可供您使用的事件中樞端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)。

下列程式碼片段說明如何從預設事件中樞端點讀取遙測。 此程式碼片段中的程式碼取自 IoT 中樞快速入門將 [遙測資料從裝置傳送至 iot 中樞，並使用後端應用程式讀取它](../articles/iot-hub/quickstart-send-telemetry-python.md)：

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

上述程式碼的下列輸出會顯示只有預設元件的「無元件 **控溫器** IoT 隨插即用裝置所傳送的溫度遙測。 `dt-dataschema`系統屬性會顯示模型識別碼：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

上述程式碼的下列輸出顯示多元件 **TemperatureController** IoT 隨插即用裝置所傳送的溫度遙測。 `dt-subject`系統屬性會顯示傳送遙測的元件名稱。 在此範例中，這兩個元件是 `thermostat1` 和， `thermostat2` 如 DTDL 模型中所定義。 `dt-dataschema`系統屬性會顯示模型識別碼：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>讀取裝置對應項變更通知

您可以設定 IoT 中樞來產生裝置對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生裝置對應項變更通知時，先前的 Python 程式碼片段中所顯示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

當 IoT 中樞為具有元件的裝置產生裝置對應項變更通知時，先前的 Python 程式碼片段中所示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>讀取數位對應項變更通知

您可以設定 IoT 中樞來產生數位對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生數位對應項變更通知時，先前的 Python 程式碼片段中所顯示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

當 IoT 中樞為具有元件的裝置產生數位對應項變更通知時，先前的 Python 程式碼片段中所示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
