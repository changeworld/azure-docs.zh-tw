---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a92e37326cf9db0eba186b2bdca44c452c02ea91
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521362"
---
您也可以使用下列資源：

- [.NET SDK 參考檔](/dotnet/api/microsoft.azure.devices)
- [服務用戶端範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)
- [數位 Twins 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples)

## <a name="iot-hub-service-client-examples"></a>IoT 中樞服務用戶端範例

本節說明使用 IoT 中樞服務用戶端和 **RegistryManager** 和 **ServiceClient** 類別的 c # 範例。 您可以使用 **RegistryManager** 類別，透過裝置 twins 與裝置狀態進行互動。 您也可以使用 **RegistryManager** 類別來查詢 IoT 中樞內的 [裝置註冊](../articles/iot-hub/iot-hub-devguide-query-language.md) 。 您可以使用 **ServiceClient** 類別在裝置上呼叫命令。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。 在程式碼片段中， `deviceTwinId` 變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-device-twin-and-model-id"></a>取得裝置對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的裝置對應項和模型識別碼：

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>更新裝置對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 此範例會顯示您在更新它之前，需要如何取得對應項 `ETag` 。 屬性是在裝置的預設元件中定義：

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 此範例會顯示您在更新它之前，需要如何取得對應項 `ETag` 。 屬性定義于 **Thermostat1** 元件中：

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

針對元件中的屬性，屬性修補程式如下列範例所示：

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>呼叫命令

下列程式碼片段說明如何叫用 `getMaxMinReport` 在預設元件中定義的命令：

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **Thermostat1** 元件中：

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="iot-hub-digital-twin-examples"></a>IoT 中樞數位對應項範例

您可以使用 **DigitalTwinClient** 類別，利用數位 twins 與裝置狀態互動。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。

本節說明使用數位 Twins API 的 c # 範例。 下列程式碼片段使用下列類別來代表控溫器和溫度控制器裝置的數位對應項：

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

`digitalTwinId`變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-digital-twin-and-model-id"></a>取得數位對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的數位對應項和模型識別碼：

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>更新數位對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 屬性是在裝置的預設元件中定義：

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature",   desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 屬性定義于 **Thermostat1** 元件中：

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>呼叫命令

下列程式碼片段說明如何叫用 `getMaxMinReport` 在預設元件中定義的命令：

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **Thermostat1** 元件中：

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="read-device-telemetry"></a>讀取裝置遙測

IoT 隨插即用裝置會將 DTDL 模型中定義的遙測資料傳送至 IoT 中樞。 根據預設，IoT 中樞會將遙測路由傳送至可供您使用的事件中樞端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)。

下列程式碼片段說明如何從預設事件中樞端點讀取遙測。 此程式碼片段中的程式碼取自 IoT 中樞快速入門將 [遙測資料從裝置傳送至 iot 中樞，並使用後端應用程式讀取它](../articles/iot-hub/quickstart-send-telemetry-dotnet.md)：

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

上述程式碼的下列輸出會顯示只有預設元件的「無元件 **控溫器** IoT 隨插即用裝置所傳送的溫度遙測。 `dt-dataschema`系統屬性會顯示模型識別碼：

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

上述程式碼的下列輸出顯示多元件 **TemperatureController** IoT 隨插即用裝置所傳送的溫度遙測。 `dt-subject`系統屬性會顯示傳送遙測的元件名稱。 在此範例中，這兩個元件是 `thermostat1` 和， `thermostat2` 如 DTDL 模型中所定義。 `dt-dataschema`系統屬性會顯示模型識別碼：

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>讀取裝置對應項變更通知

您可以設定 IoT 中樞來產生裝置對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生裝置對應項變更通知時，先前的 c # 程式碼片段中所顯示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

當 IoT 中樞為具有元件的裝置產生裝置對應項變更通知時，先前的 c # 程式碼片段中所顯示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>讀取數位對應項變更通知

您可以設定 IoT 中樞來產生數位對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生數位對應項變更通知時，先前的 c # 程式碼片段中所顯示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

當 IoT 中樞為具有元件的裝置產生數位對應項變更通知時，先前的 c # 程式碼片段中所顯示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```
