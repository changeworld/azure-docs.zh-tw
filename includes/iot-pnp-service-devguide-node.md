---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 7bf32de017a5f8ad19eb044ae7dbcdc2eaa96ca5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521363"
---
您也可以使用下列資源：

- [Node.js SDK 參考檔](/javascript/api/azure-iothub?preserve-view=true&view=azure-node-latest)
- [服務用戶端範例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [數位 Twins 範例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>IoT 中樞服務用戶端範例

本節說明使用 IoT 中樞服務客戶**端和登錄和****用戶端**類別的 JavaScript 範例。 您可以使用 **登錄類別，** 透過裝置 twins 與裝置狀態進行互動。 您也可以使用登錄 **類別來** 查詢 IoT 中樞內的 [裝置註冊](../articles/iot-hub/iot-hub-devguide-query-language.md) 。 您可以使用 **用戶端** 類別在裝置上呼叫命令。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。 在程式碼片段中， `deviceId` 變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-device-twin-and-model-id"></a>取得裝置對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的裝置對應項和模型識別碼：

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>更新裝置對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 此範例會顯示您在更新之前需要取得對應項的方式。 屬性是在裝置的預設元件中定義：

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 此範例會顯示您在更新之前需要取得對應項的方式。 屬性定義于 **thermostat1** 元件中：

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
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

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **thermostat1** 元件中：

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT 中樞數位對應項範例

您可以使用 **DigitalTwinClient** 類別，利用數位 twins 與裝置狀態互動。 裝置的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型會定義裝置所執行的屬性和命令。

本節說明使用數位 Twins API 的 JavaScript 範例。

`digitalTwinId`變數會保存向 IoT 中樞註冊的 IoT 隨插即用裝置的裝置識別碼。

### <a name="get-the-digital-twin-and-model-id"></a>取得數位對應項和模型識別碼

若要取得連線到 IoT 中樞之 IoT 隨插即用裝置的數位對應項和模型識別碼：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>更新數位對應項

下列程式碼片段顯示如何更新 `targetTemperature` 裝置上的屬性。 屬性是在裝置的預設元件中定義：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

下列程式碼片段顯示如何更新 `targetTemperature` 元件上的屬性。 屬性定義于 **thermostat1** 元件中：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>呼叫命令

下列程式碼片段說明如何叫用 `getMaxMinReport` 在預設元件中定義的命令：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

下列程式碼片段說明如何 `getMaxMinReport` 在元件上呼叫命令。 此命令定義于 **thermostat1** 元件中：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>讀取裝置遙測

IoT 隨插即用裝置會將 DTDL 模型中定義的遙測資料傳送至 IoT 中樞。 根據預設，IoT 中樞會將遙測路由傳送至可供您使用的事件中樞端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)。

下列程式碼片段說明如何從預設事件中樞端點讀取遙測。 此程式碼片段中的程式碼取自 IoT 中樞快速入門將 [遙測資料從裝置傳送至 iot 中樞，並使用後端應用程式讀取它](../articles/iot-hub/quickstart-send-telemetry-node.md)：

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

上述程式碼的下列輸出顯示多元件 **TemperatureController** IoT 隨插即用裝置所傳送的溫度遙測。 `dt-subject`系統屬性會顯示傳送遙測的元件名稱。 在此範例中，這兩個元件是 `thermostat1` 和， `thermostat2` 如 DTDL 模型中所定義。 `dt-dataschema`系統屬性會顯示模型識別碼：

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>讀取裝置對應項變更通知

您可以設定 IoT 中樞來產生裝置對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生裝置對應項變更通知時，先前的 JavaScript 程式碼片段中所顯示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

當 IoT 中樞為具有元件的裝置產生裝置對應項變更通知時，先前的 JavaScript 程式碼片段中所示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>讀取數位對應項變更通知

您可以設定 IoT 中樞來產生數位對應項變更通知，以路由傳送至支援的端點。 若要深入瞭解，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至 > 非遙測事件的不同端點](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

當 IoT 中樞為無元件控溫器裝置產生數位對應項變更通知時，先前的 JavaScript 程式碼片段中所示的程式碼會產生下列輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

當 IoT 中樞為具有元件的裝置產生數位對應項變更通知時，先前的 JavaScript 程式碼片段中所示的程式碼會產生下列輸出。 此範例顯示具有控溫器元件的溫度感應器裝置產生通知時的輸出。 應用程式屬性 `iothub-message-schema` ，並 `opType` 提供變更通知類型的相關資訊：

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
