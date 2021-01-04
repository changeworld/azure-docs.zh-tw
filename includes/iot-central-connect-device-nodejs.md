---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 6a6baa14d7521f4a85350af7b08b5fcbe82ddf6b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033821"
---
## <a name="prerequisites"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目︰

* 使用 [自訂應用程式] 範本建立的 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](../articles/iot-central/core/quick-deploy-iot-central.md)。 應用程式必須已在 2020 年 7 月 14 日或之後建立。
* 已安裝 [Node.js](https://nodejs.org/) 版本 6 或更新版本的開發電腦。 您可以在命令列執行 `node --version` 來檢查版本。 本教學課程中的指示假設您是在 Windows 命令提示字元中執行 **node** 命令。 不過，您可以在許多其他作業系統上使用 Node.js。
* [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub 存放庫的本機複本，其中包含範例程式碼。 使用此連結來下載存放庫的複本：[下載 ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip)。 接著將檔案解壓縮到本機電腦上的適當位置。

## <a name="review-the-code"></a>檢閱程式碼

在您先前下載的 Microsoft Azure IoT SDK for Node.js 複本中，以文字編輯器開啟 azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js 檔案。

執行範例以連線到 IoT Central 時，該檔案會使用裝置佈建服務 (DPS) 來註冊裝置並產生連接字串。 此範例會從命令列環境中，取出所需的 DPS 連線資訊。

`main` 方法：

* 建立 `client` 物件，並在開啟連線之前設定 `dtmi:com:example:Thermostat;1` 模型識別碼。 IoT Central 會使用模型識別碼來識別或產生此裝置的裝置範本。 若要深入了解，請參閱[將裝置與裝置範本相關聯](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)。
* 建立命令處理常式。
* 啟動迴圈，每 10 秒傳送一次溫度遙測。
* 將 `maxTempSinceLastReboot` 屬性傳送至 IoT Central。 IoT Central 會忽略 `serialNumber` 屬性，因為這不是裝置模型的一部分。
* 建立可寫入的屬性處理常式。

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice` 函式會顯示裝置如何使用 DPS 來註冊及連線到 IoT Central。 承載包含模型識別碼，IoT Central 會用來[將裝置與裝置範本產生關聯](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)：

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry` 函式會顯示裝置如何將溫度遙測資料傳送至 IoT Central。 `getCurrentTemperatureObject` 方法會傳回類似 `{ temperature: 45.6 }` 的物件：

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main` 方法會使用下列兩種方法，將 `maxTempSinceLastReboot` 屬性傳送至 IoT Central。 `main` 方法會使用類似 `{maxTempSinceLastReboot: 80.9}` 的物件呼叫 `createReportPropPatch`：

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

`main` 方法會使用下列兩種方法，從 IoT Central 處理目標溫度可寫入屬性的更新。 請注意 `propertyUpdateHandle` 如何使用版本和狀態碼來建立回應：

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

`main` 方法會使用下列兩種方法來處理 `getMaxMinReport` 命令的呼叫。 `getMaxMinReportObject` 方法會將報表產生為 JSON 物件：

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>取得連線資訊

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>執行程式碼

若要執行範例應用程式，請開啟命令列環境，然後瀏覽至 azure-iot-sdk-node/device/samples/pnpp 資料夾，其中包含 simple_thermostat.js 範例檔案。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

安裝必要的套件：

```cmd/sh
npm install
```

執行範例：

```cmd/sh
node simple_thermostat.js
```

下列輸出顯示裝置註冊和連線到 IoT Central。 接著範例會在開始傳送遙測之前，先傳送 `maxTempSinceLastReboot` 屬性：

```output
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

您可看到裝置如何回應命令和屬性更新：

```output
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```
