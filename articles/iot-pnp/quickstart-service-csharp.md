---
title: 與連線至 Azure IoT 解決方案的 IoT 隨插即用裝置互動 (C#) | Microsoft Docs
description: 使用 C# 連線至已連線 Azure IoT 解決方案的 IoT 隨插即用裝置，並與其互動。
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b35268cd8d36901f750225713407c5392e5c429e
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759164"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>快速入門：與已連線至解決方案的 IoT 隨插即用裝置互動 (C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 隨插即用讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 C# 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成本快速入門，您必須在開發機器上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用範例程式碼複製 SDK 存放庫

如果您已完成[快速入門：將在 Windows 上執行的範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 (C#)](quickstart-connect-device-csharp.md)，您已經複製了存放庫。

複製 Microsoft Azure IoT SDK for .NET GitHub 存放庫中的範例。 在您選擇的資料夾中開啟命令提示字元。 執行下列命令以複製[適用於 .NET 的 Microsoft Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫：

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>執行範例裝置

在本快速入門中，您可以使用以 C# 撰寫的範例控溫器裝置，作為 IoT 隨插即用裝置。 若要執行範例裝置：

1. 在 Visual Studio 2019 中開啟 azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj 專案檔。

1. 在 Visual Studio 中，瀏覽至 [專案] > [控溫器屬性] > [偵錯]。 然後將下列環境變數新增至專案：

    | 名稱 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 您完成[設定環境](set-up-environment.md)時所記下的值 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 您完成[設定環境](set-up-environment.md)時所記下的值 |


1. 您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

1. 您會看到訊息，指出裝置已傳送一些資訊並回報其本身的線上狀態。 這些訊息表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。 請勿關閉此 Visual Studio 執行個體，稍後您將需要用來確認服務範例已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在 [設定 IoT 隨插即用的環境快速入門和教學課程](set-up-environment.md)，您建立了兩個環境變數，以設定連線至 IoT 中樞和裝置的範例：

* **IOTHUB_CONNECTION_STRING**：您先前記下的 IoT 中樞連接字串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在本快速入門中，您會使用 C# 中的範例 IoT 解決方案，與您剛剛設定的範例裝置進行互動。

1. 在 Visual Studio 的另一個執行個體中，開啟 azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj 專案。

1. 在 Visual Studio 中，瀏覽至 [專案] > [控溫器屬性] > [偵錯]。 然後將下列環境變數新增至專案：

    | 名稱 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | 您完成[設定環境](set-up-environment.md)時所記下的值 |

1. 您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

### <a name="get-digital-twin"></a>取得數位分身

下列程式碼片段會顯示服務應用程式如何擷取數位對應項：

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> 這個範例會使用 **IoT 中樞服務用戶端** 中的 **Microsoft.Azure.Devices.Client;** 命名空間。 若要深入了解如何擷取模型識別碼，請參閱[開發人員指南](concepts-developer-guide-service.md)。

這會程式碼產生下列輸出：

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

下列程式碼片段會示範如何使用「修補程式」，透過數位對應項來更新屬性：

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

當服務更新 `targetTemperature` 屬性時，此程式碼會從裝置產生下列輸出：

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>叫用命令

下列程式碼片段示範如何呼叫命令：

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

當服務呼叫 `getMaxMinReport` 命令時，此程式碼會從裝置產生下列輸出：

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](concepts-developer-guide-device-csharp.md)
