---
title: 裝置開發人員指南 (JAVA) -IoT 隨插即用 |Microsoft Docs
description: JAVA 裝置開發人員的 IoT 隨插即用描述
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a5c29e683648d53a7deaa3e6bb0493cfec269afa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579644"
---
# <a name="iot-plug-and-play-device-developer-guide-java"></a>IoT 隨插即用 (JAVA) 的裝置開發人員指南

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>模型識別碼宣告

若要宣告模型識別碼，裝置必須將它包含在連接資訊中：

```java
ClientOptions options = new ClientOptions();
options.setModelId(MODEL_ID);
deviceClient = new DeviceClient(deviceConnectionString, protocol, options);
```

多載適用 `ClientOptions` `DeviceClient` 于用來初始化連接的所有方法。

> [!TIP]
> 針對模組和 IoT Edge，請使用 `ModuleClient` 取代 `DeviceClient` 。

## <a name="dps-payload"></a>DPS 承載

使用「裝置布建 [服務」 (DPS) ](../iot-dps/about-iot-dps.md) 的裝置可以包含在布建程式 `modelId` 期間使用下列 JSON 承載所要使用的。

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>執行遙測、屬性和命令

如 [瞭解 IoT 隨插即用模型中的元件](concepts-components.md)所述，裝置構建者必須決定是否要使用元件來描述其裝置。 使用元件時，裝置必須遵循本節所述的規則。

### <a name="telemetry"></a>遙測

預設元件不需要任何特殊屬性。

使用嵌套元件時，裝置必須設定具有元件名稱的訊息屬性：

```java
private static void sendTemperatureTelemetry(String componentName) {
  double currentTemperature = temperature.get(componentName);

  Map<String, Object> payload = singletonMap("temperature", currentTemperature);

  Message message = new Message(gson.toJson(payload));
  message.setContentEncoding("utf-8");
  message.setContentTypeFinal("application/json");

  if (componentName != null) {
      message.setProperty("$.sub", componentName);
  }
  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
}
```

### <a name="read-only-properties"></a>唯讀屬性

從預設元件報告屬性不需要任何特殊的結構：

```java
Property reportedProperty = new Property("maxTempSinceLastReboot", 38.7);

deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

使用嵌套元件時，必須在元件名稱內建立屬性：

```java
Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("maxTemperature", 38.7);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty)

deviceClient.sendReportedProperties(reportedProperty);
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>可寫入屬性

這些屬性可以由裝置設定，或由解決方案進行更新。 如果解決方案更新屬性，用戶端會收到通知作為或中的回呼 `DeviceClient` `ModuleClient` 。 若要遵循 IoT 隨插即用慣例，裝置必須通知服務已成功接收屬性。

#### <a name="report-a-writable-property"></a>報告可寫入的屬性

當裝置報告可寫入的屬性時，它必須包含 `ack` 慣例中定義的值。

從預設元件報告可寫入的屬性：

```java
@AllArgsConstructor
private static class EmbeddedPropertyUpdate {
  @NonNull
  @SerializedName("value")
  public Object value;
  @NonNull
  @SerializedName("ac")
  public Integer ackCode;
  @NonNull
  @SerializedName("av")
  public Integer ackVersion;
  @SerializedName("ad")
  public String ackDescription;
}

EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(23.2, 200, 3, "Successfully updated target temperature");
Property reportedPropertyCompleted = new Property("targetTemperature", completedUpdate);
deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

若要從嵌套的元件報告可寫入的屬性，對應項必須包含標記：

```java
Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
    put("value", 23.2);
    put("ac", 200);
    put("av", 3);
    put("ad", "complete");
}};

Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("targetTemperature", embeddedProperty);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty));

deviceClient.sendReportedProperties(reportedProperty);
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>訂閱所需的屬性更新

服務可以更新所需的屬性，這些屬性會在連線的裝置上觸發通知。 此通知包含已更新的所需屬性，包括識別更新的版本號碼。 裝置必須使用與 `ack` 報告屬性相同的訊息來回應。

預設元件會看到單一屬性，並 `ack` 使用收到的版本建立報告：

```java
private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, 200, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
  Collections.singletonMap(
    new Property("targetTemperature", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), null));
deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

裝置對應項會在所需和報告的區段中顯示內容：

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

嵌套的元件會接收以元件名稱包裝的所需屬性，且應該回報 `ack` 報告的屬性：

```java
private static final Map<String, Double> temperature = new HashMap<>();

private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        String componentName = (String) context;

        if (property.getKey().equalsIgnoreCase(componentName)) {
            double targetTemperature = (double) ((TwinCollection) property.getValue()).get(propertyName);

            Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
                put("value", temperature.get(componentName));
                put("ac", 200);
                put("av", property.getVersion().longValue());
                put("ad", "Successfully updated target temperature.");
            }};

            Map<String, Object> componentProperty = new HashMap<String, Object>() {{
                put("__t", "c");
                put(propertyName, embeddedProperty);
            }};

            Set<Property> completedPropertyPatch = new Property(componentName, componentProperty));

            deviceClient.sendReportedProperties(completedPropertyPatch);
        } else {
            log.debug("Property: Received an unrecognized property update from service.");
        }
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat1", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat1")),
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat2", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat2"))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

嵌套元件的裝置對應項會顯示所需和所報告的區段，如下所示：

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>命令

預設元件會收到服務所叫用的命令名稱。

嵌套的元件會收到前面加上元件名稱和分隔符號的命令名稱 `*` 。

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);

// ...
private static final Map<String, Double> temperature = new HashMap<>();

private static class MethodCallback implements DeviceMethodCallback {
  final String reboot = "reboot";
  final String getMaxMinReport1 = "thermostat1*getMaxMinReport";
  final String getMaxMinReport2 = "thermostat2*getMaxMinReport";

  @Override
  public DeviceMethodData call(String methodName, Object methodData, Object context) {
    String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);

    switch (methodName) {
      case reboot:
        int delay = gson.fromJson(jsonRequest, Integer.class);

        Thread.sleep(delay * 1000);

        temperature.put("thermostat1", 0.0d);
        temperature.put("thermostat2", 0.0d);

        return new DeviceMethodData(200, null);

      // ...

      default:
        log.debug("Command: command=\"{}\" is not implemented, no action taken.", methodName);
          return new DeviceMethodData(404, null);
    }
  }
}
```

#### <a name="request-and-response-payloads"></a>要求和回應承載

命令使用類型來定義其要求和回應裝載。 裝置必須還原序列化傳入輸入參數，並將回應序列化。

下列範例顯示如何使用裝載中定義的複雜類型來執行命令：

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

下列程式碼片段顯示裝置如何執行此命令定義，包括用來啟用序列化和還原序列化的類型：

```java
deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), "getMaxMinReport", new MethodIotHubEventCallback(), "getMaxMinReport");

// ...

private static class GetMaxMinReportMethodCallback implements DeviceMethodCallback {
    String commandName = "getMaxMinReport";

    @Override
    public DeviceMethodData call(String methodName, Object methodData, Object context) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = gson.fromJson(jsonRequest, Date.class);

        String responsePayload = String.format(
                "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                maxTemp,
                minTemp,
                avgTemp,
                since,
                endTime);

        return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
    }
}
```

> [!Tip]
> 要求和回應名稱不會出現在透過網路傳輸的序列化承載中。

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
