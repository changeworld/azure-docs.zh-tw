---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/25/2020
ms.openlocfilehash: f4536beae18a50d3e1d42fc1593cf826c94418f8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033823"
---
## <a name="prerequisites"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目︰

* 使用 [自訂應用程式] 範本建立的 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](../articles/iot-central/core/quick-deploy-iot-central.md)。 應用程式必須已在 2020 年 7 月 14 日或之後建立。
* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) 版本的開發電腦。
* [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫的本機複本，其中包含範例程式碼。 使用此連結來下載存放庫的複本：[下載 ZIP](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 接著將檔案解壓縮到本機電腦上的適當位置。

## <a name="review-the-code"></a>檢閱程式碼

在您先前下載的 Microsoft Azure IoT Samples for C# 存放庫中，以 Visual Studio 開啟 azure-iot-samples-csharp-master\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj 專案檔。 在 **控溫器** 專案中，開啟 Program.cs 和 ThermostatSample.cs 檔案，檢視此範例的程式碼。

執行範例以連線到 IoT Central 時，該檔案會使用裝置佈建服務 (DPS) 來註冊裝置並產生連接字串。 此範例會從環境中取出所需的 DPS 連線資訊。

在 Program.cs 中，`main` 方法會呼叫 `SetupDeviceClientAsync` 來執行下列動作：

* 在透過 DPS 佈建裝置時，使用模型識別碼 `dtmi:com:example:Thermostat;1`。 IoT Central 會使用型號識別碼來識別或產生此裝置的裝置範本。 若要深入了解，請參閱[將裝置與裝置範本相關聯](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)。
* 建立 **DeviceClient** 執行個體以連線到 IoT Central。

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      s_logger.LogDebug($"Initializing via DPS");
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
        // ...
        break;

    default:
        // ...
  }

  return deviceClient;
}
```

Main 方法接著會建立 **ThermostatSample** 執行個體，並呼叫 `PerformOperationsAsync` 方法來處理與 IoT Central 的互動。

在 ThermostatSample.cs 中， `PerformOperationsAsync` 方法會：

* 設定處理常式，以接收目標溫度所需的屬性更新。
* 設定 **getMaxMinReport** 命令的處理常式。
* 定期傳送溫度遙測資料。
* 當達到新的最高溫度時，會傳送上次重新開機後的最高溫度。

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(TargetTemperatureUpdateCallbackAsync, _deviceClient, cancellationToken);

  await _deviceClient.SetMethodHandlerAsync("getMaxMinReport", HandleMaxMinReportCommand, _deviceClient, cancellationToken);

  bool temperatureReset = true;
  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
      _temperature = Math.Round(_random.NextDouble() * 40.0 + 5.0, 1);
      temperatureReset = false;
    }

    await SendTemperatureAsync();
    await Task.Delay(5 * 1000);
  }
}
```

`SendTemperatureAsync` 方法會顯示裝置如何將溫度遙測資料傳送至 IoT Central：

```csharp
private async Task SendTemperatureAsync()
{
  await SendTemperatureTelemetryAsync();

  double maxTemp = _temperatureReadingsDateTimeOffset.Values.Max<double>();
  if (maxTemp > _maxTemp)
  {
    _maxTemp = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync();
  }
}
```

`UpdateMaxTemperatureSinceLastRebootAsync` 方法會將 `maxTempSinceLastReboot` 屬性更新傳送至 IoT Central：

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync()
{
  const string propertyName = "maxTempSinceLastReboot";

  var reportedProperties = new TwinCollection();
  reportedProperties[propertyName] = _maxTemp;

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties);
}
```

`TargetTemperatureUpdateCallbackAsync` 方法會處理來自 IoT Central 的可寫入目標溫度屬性更新：

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
    const string propertyName = "targetTemperature";

    (bool targetTempUpdateReceived, double targetTemperature) = GetPropertyFromTwin<double>(desiredProperties, propertyName);
    if (targetTempUpdateReceived)
    {
      string jsonPropertyPending = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.InProgress}, " +
          $"\"av\": {desiredProperties.Version} }} }}";
      var reportedPropertyPending = new TwinCollection(jsonPropertyPending);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedPropertyPending);

      // Update Temperature in 2 steps
      double step = (targetTemperature - _temperature) / 2d;
      for (int i = 1; i <= 2; i++)
      {
        _temperature = Math.Round(_temperature + step, 1);
        await Task.Delay(6 * 1000);
      }

      string jsonProperty = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.Completed}, " +
        $"\"av\": {desiredProperties.Version}, \"ad\": \"Successfully updated target temperature\" }} }}";
      var reportedProperty = new TwinCollection(jsonProperty);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedProperty);
  }
  else
  {
    // ...
  }
}
```

`HandleMaxMinReportCommand` 方法會處理從 IoT Central 呼叫的命令：

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
  try
  {
    DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
    var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

    Dictionary<DateTimeOffset, double> filteredReadings = _temperatureReadingsDateTimeOffset
      .Where(i => i.Key > sinceInDateTimeOffset)
      .ToDictionary(i => i.Key, i => i.Value);

    if (filteredReadings != null && filteredReadings.Any())
    {
      var report = new
      {
        maxTemp = filteredReadings.Values.Max<double>(),
        minTemp = filteredReadings.Values.Min<double>(),
        avgTemp = filteredReadings.Values.Average(),
        startTime = filteredReadings.Keys.Min(),
        endTime = filteredReadings.Keys.Max(),
      };

      byte[] responsePayload = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(report));
      return Task.FromResult(new MethodResponse(responsePayload, (int)StatusCode.Completed));
    }

    return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
  }
  catch (JsonReaderException ex)
  {
    // ...
  }
}
```

## <a name="get-connection-information"></a>取得連線資訊

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>執行程式碼

若要執行範例應用程式：

1. 在 Visual Studio 中開啟 azure-iot-samples-csharp-master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat/Thermostat.csproj 專案檔。

1. 在 Visual Studio 中，瀏覽至 [專案] > [控溫器屬性] > [偵錯]。 然後將下列環境變數新增至專案：

    | 名稱 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 您先前記下的識別碼範圍值。 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 您先前所記下的已產生裝置金鑰值。 |

您現在可以在 Visual Studio 中執行和偵錯範例。

下列輸出顯示裝置註冊和連線到 IoT Central。 範本開始傳送遙測：

```output
[11/25/2020 11:07:58]info: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Press Control+C to quit the sample.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set up the device client.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Initializing via DPS
[11/25/2020 11:08:11]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler to receive "targetTemperature" updates.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler for "getMaxMinReport" command.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 36.5°C } is Completed.
[11/25/2020 11:08:18]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:23]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:29]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

您可看到裝置如何回應命令和屬性更新：

```output
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 19/11/2020 06:30:00.
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: MaxMinReport since 19/11/2020 06:30:00: maxTemp=36.5, minTemp=36.5, avgTemp=36.5, startTime=25/11/2020 11:08:13, endTime=25/11/2020 11:09:51

...

[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Received - { "targetTemperature": 56°C }.
[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is InProgress.
[11/25/2020 11:14:40]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 56°C } is Completed.
[11/25/2020 11:14:43]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is Completed.
```
