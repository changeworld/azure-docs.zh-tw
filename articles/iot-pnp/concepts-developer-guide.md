---
title: 開發人員指南-IoT 隨插即用預覽 |Microsoft Docs
description: 開發人員的 IoT 隨插即用描述
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f1860f9645a50789803d509a6a1ea98bc0ea1a9e
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950131"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>IoT 隨插即用 Preview 開發人員指南

IoT 隨插即用 Preview 可讓您建立智慧型裝置，以將其功能公告至 Azure IoT 應用程式。 當客戶將裝置連線到已啟用 IoT 隨插即用的應用程式時，IoT 隨插即用裝置不需要手動設定。

本指南說明建立遵循 [IoT 隨插即用慣例](concepts-convention.md)之裝置所需的基本步驟，以及您可以用來與裝置互動的可用 REST api。

若要建立 IoT 隨插即用裝置，請遵循下列步驟：

1. 確定您的裝置使用 MQTT 或 MQTT over Websocket 通訊協定來連線到 Azure IoT 中樞。
1. 建立 [數位 Twins 定義語言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) 模型來描述您的裝置。 若要深入瞭解，請參閱 [瞭解 IoT 隨插即用模型中的元件](concepts-components.md)。
1. 更新您的裝置，以公告 `model-id` 作為裝置連線的一部分。
1. 使用[IoT 隨插即用慣例](concepts-convention.md)來執行遙測、屬性和命令

當您的裝置準備就緒之後，請使用 [Azure IoT explorer](howto-use-iot-explorer.md) 來驗證裝置是否遵循 IoT 隨插即用的慣例。

> [!Tip]
> 本文中的所有程式碼片段都使用 c #，但這些概念適用于任何適用于 C、Python、Node 和 JAVA 的可用 Sdk。

## <a name="model-id-announcement"></a>模型識別碼宣告

若要宣告模型識別碼，裝置必須將它包含在連接資訊中：

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

新的多載 `ClientOptions` 適用于 `DeviceClient` 用來初始化連接的所有方法。

模型識別碼公告已新增至下一版的 Sdk

|SDK|版本|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|節點|1.17.0|
|Python|2.1.4|

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

沒有元件的模型不需要任何特殊屬性。

使用元件時，裝置必須設定具有元件名稱的訊息屬性：

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>唯讀屬性

沒有元件的模型不需要任何特殊的結構：

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

使用元件時，必須在元件名稱內建立屬性：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
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

這些屬性可以由裝置設定，或由解決方案進行更新。 如果解決方案更新屬性，用戶端就會收到通知，做為中的回呼 `DeviceClient` 。 若要遵循 IoT 隨插即用慣例，裝置必須通知服務已成功接收屬性。

#### <a name="report-a-writable-property"></a>報告可寫入的屬性

當裝置報告可寫入的屬性時，它必須包含 `ack` 慣例中定義的值。

若要報告沒有元件的可寫入屬性：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

若要從元件報告可寫入的屬性，對應項必須包含標記：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
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

沒有元件的模型會看到單一屬性，並建立 `ack` 以接收的版本報告的：

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
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
          "ad": "complete"
      }
  }
}
```

具有元件的模型會接收以元件名稱包裝的所需屬性，且應該回報 `ack` 報告的屬性：

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

元件的裝置對應項會顯示所需和報告的區段，如下所示：

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

沒有元件的模型會收到服務所叫用的命令名稱。

具有元件的模型會收到命令名稱，並以元件和分隔符號作為前置詞 `*` 。

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>要求和回應承載

命令使用類型來定義其要求和回應裝載。 裝置必須還原序列化傳入輸入參數，並將回應序列化。 下列範例顯示如何使用裝載中定義的複雜類型來執行命令：

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

下列程式碼片段顯示裝置如何執行此命令定義，包括用來啟用序列化和還原序列化的類型：

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> 要求和回應名稱不會出現在透過網路傳輸的序列化承載中。

## <a name="interact-with-the-device"></a>與裝置互動 

IoT 隨插即用可讓您使用已向 IoT 中樞宣告其模型識別碼的裝置。 例如，您可以直接存取裝置的屬性和命令。

若要使用連線到 IoT 中樞的 IoT 隨插即用裝置，請使用 IoT 中樞 REST API 或其中一個 IoT 語言 Sdk。 下列範例會使用 REST API 的 IoT 中樞。 目前的 API 版本為 `2020-05-31-preview` 。 附加 `?api-version=2020-05-31` 至 REST PI 呼叫。

如果呼叫您的控溫器裝置 `t-123` ，您可以使用 REST API get 呼叫，取得裝置所執行之所有介面上的所有屬性：

```REST
GET /digitalTwins/t-123
```

此呼叫會包含 `$metadata.$model` 具有裝置所宣告之模型識別碼的 Json 屬性。

所有介面上的所有屬性都是使用 `GET /DigitalTwin/{device-id}` REST API 範本來存取，其中 `{device-id}` 是裝置的識別碼：

```REST
GET /digitalTwins/{device-id}
```

您可以直接呼叫 IoT 隨插即用的裝置命令。 如果 `Thermostat` 裝置中的元件 `t-123` 有 `restart` 命令，您可以使用 REST API 的 POST 呼叫來呼叫它：

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

更常見的情況是，您可以透過此 REST API 範本來呼叫命令：

- `device-id`：裝置的識別碼。
- `component-name`：裝置功能模型中的 [implements] 區段的介面名稱。
- `command-name`：命令的名稱。

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>接下來的步驟

現在您已瞭解裝置模型化，以下是一些額外的資源：

- [數位分身定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型元件](./concepts-components.md)
