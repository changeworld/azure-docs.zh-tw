---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 70df45877a310d74e7c5c82292d18b1c0eb32da8
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521361"
---
您也可以使用下列資源：

- [JAVA SDK 參考檔](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?preserve-view=true&view=azure-java-stable)
- [服務用戶端範例](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)
- [數位 Twins 範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples)

## <a name="iot-hub-service-client-examples"></a>IoT 中樞服務用戶端範例

本節說明使用 IoT 中樞服務用戶端的 JAVA 範例，以及**DeviceTwin**命名空間中的**DeviceTwin**和**DeviceMethod**類別。 您可以使用 **DeviceTwin** 類別，透過裝置 twins 與裝置狀態進行互動。 您也可以使用 **DeviceTwin** 類別來查詢 IoT 中樞內的 [裝置註冊](../articles/iot-hub/iot-hub-devguide-query-language.md) 。 您可以使用 **DeviceMethod** 類別在裝置上呼叫命令。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。 在程式碼片段中， `deviceId` 變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-device-twin-and-model-id"></a>取得裝置對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的裝置對應項和模型識別碼：

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);

// ...

DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-device-twin"></a>更新裝置對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 您必須先取得對應項，才能進行更新。 屬性是在裝置的預設元件中定義：

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair("targetTemperature", propertyValue)));
twinClient.updateTwin(twin);
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 您必須先取得對應項，才能進行更新。 屬性定義于 **thermostat1** 元件中：

```java
public static Set<Pair> CreateComponentPropertyPatch(@NonNull String propertyName, @NonNull double propertyValue, @NonNull String componentName)
{
    JsonObject patchJson = new JsonObject();
    patchJson.addProperty("__t", "c");
    patchJson.addProperty(propertyName, propertyValue);
    return singleton(new Pair(componentName, patchJson));
}

// ...

DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(CreateComponentPropertyPatch("targetTemperature", propertyValue, "thermostat1"));
twinClient.updateTwin(twin);
```

針對元件中的屬性，屬性修補程式如下列範例所示：

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 60.2
  }
}
```

### <a name="call-command"></a>呼叫命令

下列程式碼片段說明如何叫用 `getMaxMinReport` 在預設元件中定義的命令：

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **thermostat1** 元件中：

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "thermostat1*getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

## <a name="iot-hub-digital-twin-examples"></a>IoT 中樞數位對應項範例

您可以使用**digitaltwin**命名空間中的**DigitalTwinAsyncClient**類別，以使用數位 twins 來與裝置狀態互動。 下列範例也會使用相同命名空間中的 **UpdateOperationUtility** 和 **BasicDigitalTwin** 類別。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。

`digitalTwinid`變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-digital-twin-and-model-id"></a>取得數位對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的數位對應項和模型識別碼：

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch = new CountDownLatch(1);
asyncClient.getDigitalTwin(digitalTwinid, BasicDigitalTwin.class)
    .subscribe(
        getResponse ->
        {
            System.out.println("Digital Twin Model Id: " + getResponse.getMetadata().getModelId());
            System.out.println("Digital Twin: " + prettyBasicDigitalTwin(getResponse));
            latch.countDown();
        },
        error ->
        {
            System.out.println("Get Digital Twin failed: " + error);
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyBasicDigitalTwin(BasicDigitalTwin basicDigitalTwin)
{
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(basicDigitalTwin);
}
```

### <a name="update-digital-twin"></a>更新數位對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 屬性是在裝置的預設元件中定義：

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch1 = new CountDownLatch(1);

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

// Add a new property.
updateOperationUtility.appendAddPropertyOperation("/" + "targetTemperature", 35);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch1.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch1.countDown();
        });
latch1.await(10, TimeUnit.SECONDS);
GetDigitalTwin();

// Replace an existing property.
CountDownLatch latch2 = new CountDownLatch(1);
updateOperationUtility.appendReplacePropertyOperation("/targetTemperature", 50);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch2.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch2.countDown();
        });

latch2.await(10, TimeUnit.SECONDS);
GetDigitalTwin();
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 屬性定義于 **thermostat1** 元件中：

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

// Get digital twin.
ServiceResponseWithHeaders<String, DigitalTwinGetHeaders> getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);

// Construct the options for conditional update.
DigitalTwinUpdateRequestOptions options = new DigitalTwinUpdateRequestOptions();
options.setIfMatch(getResponse.headers().eTag());

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

Map<String, Object> t1properties = new HashMap<>();
t1properties.put("targetTemperature", 50);
updateOperationUtility.appendReplaceComponentOperation("/thermostat1", t1properties);

digitalTwinUpdateOperations = updateOperationUtility.getUpdateOperations();
updateResponse = client.updateDigitalTwinWithResponse(digitalTwinid, digitalTwinUpdateOperations, options);
System.out.println("Update Digital Twin response status: " + updateResponse.response().message());

getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);
```

### <a name="call-command"></a>呼叫命令

下列程式碼片段說明如何叫用 `getMaxMinReport` 在預設元件中定義的命令：

```java
CountDownLatch latch = new CountDownLatch(1);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

// Invoke a method on root level.
asyncClient.invokeCommand(digitalTwinid, "getMaxMinReport", commandInput)
    .subscribe(
        response ->
        {
            System.out.println("Invoked Command getMaxMinReport response: " + prettyString(response.getPayload()));
            latch.countDown();
        },
        error ->
        {
            RestException ex = (RestException)error;
            if(ex.response().code() == 404) {
                System.out.println("Invoked Command getMaxMinReport failed: " + error);
            }
            else {
                System.out.println("Ensure the device sample is running for this sample to succeed");
            }
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **thermostat1** 元件中：

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

DigitalTwinInvokeCommandRequestOptions options = new DigitalTwinInvokeCommandRequestOptions();
try {
    ServiceResponseWithHeaders<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> commandResponse = client.invokeComponentCommandWithResponse(digitalTwinid, "thermostat1", "getMaxMinReport", commandInput, options);
    System.out.println("Command getMaxMinReport, payload: " + prettyString(commandResponse.body().getPayload()));
    System.out.println("Command getMaxMinReport, status: " + commandResponse.body().getStatus());
} catch (RestException ex)
{
    if(ex.response().code() == 404)
    {
        System.out.println("Ensure the device sample is running for this sample to succeed.");
    }
    else
    {
        throw ex;
    }
}

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

## <a name="read-device-telemetry"></a>讀取裝置遙測

IoT 隨插即用裝置會將 DTDL 模型中定義的遙測資料傳送至 IoT 中樞。 根據預設，IoT 中樞會將遙測路由傳送至可供您使用的事件中樞端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)。

下列程式碼片段說明如何從預設事件中樞端點讀取遙測。 此程式碼片段中的程式碼取自 IoT 中樞快速入門將 [遙測資料從裝置傳送至 iot 中樞，並使用後端應用程式讀取它](../articles/iot-hub/quickstart-send-telemetry-java.md)：

```java
import com.azure.messaging.eventhubs.EventHubClientBuilder;
import com.azure.messaging.eventhubs.EventHubConsumerAsyncClient;

// ...

EventHubClientBuilder eventHubClientBuilder = new EventHubClientBuilder()
    .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
    .connectionString(eventHubCompatibleConnectionString);

try (EventHubConsumerAsyncClient eventHubConsumerAsyncClient = eventHubClientBuilder.buildAsyncConsumerClient()) {

    receiveFromAllPartitions(eventHubConsumerAsyncClient);

}

// ...

private static void receiveFromAllPartitions(EventHubConsumerAsyncClient eventHubConsumerAsyncClient) {

eventHubConsumerAsyncClient
    .receive(false) // set this to false to read only the newly available events
    .subscribe(partitionEvent -> {
        System.out.println();
        System.out.printf("%nTelemetry received from partition %s:%n%s",
            partitionEvent.getPartitionContext().getPartitionId(), partitionEvent.getData().getBodyAsString());
        System.out.printf("%nApplication properties (set by device):%n%s", partitionEvent.getData().getProperties());
        System.out.printf("%nSystem properties (set by IoT Hub):%n%s",
            partitionEvent.getData().getSystemProperties());
    }, ex -> {
        System.out.println("Error receiving events " + ex);
    }, () -> {
        System.out.println("Completed receiving events");
    });
}
```

上述程式碼的下列輸出會顯示只有預設元件的「無元件 **控溫器** IoT 隨插即用裝置所傳送的溫度遙測。 `dt-dataschema`系統屬性會顯示模型識別碼：

```cmd/sh
Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=dd960185-6ddb-4b5f-89bb-e26b0b3c201e, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:10 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=1c05cece-070b-4e2e-b2e8-e263858594a3, content-type=application/json}

Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=d10a7350-43ef-4cf6-9db5-a4b08684cd9d, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:15 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=d3a80af4-1246-41a0-a09a-582a12c17a00, content-type=application/json}
```

上述程式碼的下列輸出顯示多元件 **TemperatureController** IoT 隨插即用裝置所傳送的溫度遙測。 `dt-subject`系統屬性會顯示傳送遙測的元件名稱。 在此範例中，這兩個元件是 `thermostat1` 和， `thermostat2` 如 DTDL 模型中所定義。 `dt-dataschema`系統屬性會顯示模型識別碼：

```cmd/sh
Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=413002d0-2107-4c08-8f4a-995ae1f4047b, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=da8bd068-850e-43fb-862f-66080d5969e4, content-type=application/json, dt-subject=thermostat1}

Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=2d9407e5-413f-4f8d-bd30-cd153e03c72f, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=ed419c4e-ef2c-4acf-8991-6245059c5fdc, content-type=application/json, dt-subject=thermostat2}
```

## <a name="read-device-twin-change-notifications"></a>讀取裝置對應項變更通知

您可以設定 IoT 中樞來產生裝置對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生裝置對應項變更通知時，先前的 JAVA 程式碼片段中所顯示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received from partition 1:
{"version":11,"properties":{"reported":{"maxTempSinceLastReboot":43.4,"$metadata":{"$lastUpdated":"2020-10-20T11:50:41.123127Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:50:41.123127Z"}},"$version":10}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:50:41.1231270+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@12fd5bb4, correlation-id=11339418426a, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:50:41 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

當 IoT 中樞為具有元件的裝置產生裝置對應項變更通知時，先前的 JAVA 程式碼片段中所示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received from partition 1:
{"version":9,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":32.5},"$metadata":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","thermostat1":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","__t":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"}}},"$version":8}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:48:01.2960851+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@23949bae, correlation-id=113334d542e1, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:48:01 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

## <a name="read-digital-twin-change-notifications"></a>讀取數位對應項變更通知

您可以設定 IoT 中樞來產生數位對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生數位對應項變更通知時，先前的 JAVA 程式碼片段中所顯示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received from partition 1:
[{"op":"replace","path":"/$metadata/maxTempSinceLastReboot/lastUpdateTime","value":"2020-10-20T11:52:40.627628Z"},{"op":"replace","path":"/maxTempSinceLastReboot","value":16.9}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:52:40.6276280+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@4475ce2a, correlation-id=1133db52c0e0, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:52:40 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```

當 IoT 中樞為具有元件的裝置產生數位對應項變更通知時，先前的 JAVA 程式碼片段中所示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received from partition 1:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-20T11:31:04.7811405Z"}},"maxTempSinceLastReboot":27.2}}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:31:04.7811405+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@75981aa, correlation-id=1130d6f4d212, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:31:04 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```
