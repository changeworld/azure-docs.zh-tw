---
title: 將 IoT 隨插即用範例 C# 元件裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 建置並執行使用多個元件的 IoT 隨插即用範例 C# 裝置程式碼，並連線至 IoT 中樞。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 72b85388bf80a6ed2b9617b606940c243c5711f1
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945527"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>教學課程：將在 Windows 上執行的 IoT 隨插即用多個元件裝置應用程式連線至 IoT 中樞 (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

本教學課程說明如何建置具有元件的範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具來檢視其傳送至中樞的資訊。 範例應用程式是以 C# 撰寫的，並隨附於適用於 C# 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成此教學課程，請在您的本機 Windows 環境上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用範例程式碼複製 SDK 存放庫

如果您已完成[快速入門：將在 Windows 上執行的範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 (C#)](quickstart-connect-device-csharp.md)，您已經複製了存放庫。

從 C# GitHub 存放庫的 Azure IoT 範例複製範例。 在您選擇的資料夾中開啟命令提示字元。 執行下列命令以複製[適用於 .NET 的 Microsoft Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫：

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>執行範例裝置

在本快速入門中，您可以使用以 C# 撰寫的範例溫度控制器裝置，作為 IoT 隨插即用裝置。 若要執行範例裝置：

1. 在 Visual Studio 2019 中開啟 *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* 專案檔。

1. 在 Visual Studio 中，瀏覽至 [專案] > [TemperatureController 屬性] > [偵錯]。 然後將下列環境變數新增至專案：

    | 名稱 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 您完成[設定環境](set-up-environment.md)時所記下的值 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 您完成[設定環境](set-up-environment.md)時所記下的值 |


1. 您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

1. 您會看到訊息，指出裝置已傳送一些資訊並回報其本身的線上狀態。 這些訊息表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。 請勿關閉此 Visual Studio 執行個體，稍後您將需要用來確認服務範例已正常運作。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>檢閱程式碼

這個範例會實作 IoT 隨插即用的溫度控制器裝置。 這個範例所實作的模型會使用[多個元件](concepts-components.md)。 [適用於溫度裝置的 Digital Twins 定義語言 (DTDL) 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) \(英文\) 會定義裝置所實作的遙測、屬性和命令。

裝置程式碼會使用標準 `CreateFromConnectionString` 方法連線到您的 IoT 中樞。 裝置會在連線要求中，傳送其實作之 DTDL 模型的模型識別碼。 傳送模型識別碼的裝置是 IoT 隨插即用裝置：

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

模型識別碼會儲存於程式碼中，如下列程式碼片段所示：

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

當裝置連線到您的 IoT 中樞之後，程式碼就會註冊命令處理常式。 `reboot` 命令會定義於預設元件中。 `getMaxMinReport` 命令會個別定義於這兩個控溫器元件中：

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

針對這兩個控溫器元件上所需的屬性更新，會有個別的處理常式：

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

範例程式碼會從每個控溫器元件傳送遙測：

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync` 方法會使用 `PnpHhelper` 類別來建立每個元件的訊息：

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper` 類別包含可以搭配多個元件模型使用的其他範例方法。

使用 Azure IoT 總管工具，檢視來自這兩個控溫器元件的遙測和屬性：

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT 總管中的多個元件裝置":::

您也可以使用 Azure IoT 總管工具，在這兩個控溫器元件的其中之一或是預設元件中呼叫命令。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何將具有元件的 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](concepts-developer-guide-device-csharp.md)
