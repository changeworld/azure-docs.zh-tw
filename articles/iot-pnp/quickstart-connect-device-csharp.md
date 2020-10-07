---
title: 將 IoT 隨插即用範例 C# 裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 在 Windows 上建置並執行連線至 IoT 中樞的 IoT 隨插即用範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d1deac1c7932a8f3cec06d9c264ba401f7f1341d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577028"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-c"></a>快速入門：將在 Windows 上執行的範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具檢視其所傳送的遙測資料。 範例應用程式是以 CSharp 撰寫的，並隨附於適用於 C# 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成本快速入門，您必須在開發機器上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。

## <a name="download-the-code"></a>下載程式碼

在本快速入門中，您會準備可用來複製及建置 Azure IoT 中樞裝置 C# SDK 的開發環境。

在您選擇的資料夾中開啟命令提示字元。 執行下列命令，將[適用於 .NET 的 Microsoft Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫複製到下列位置：

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>建置程式碼

您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

1. 在 Visual Studio 2019 中開啟 azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj 專案檔。

1. 在 Visual Studio 中，瀏覽至 [專案] > [控溫器屬性] > [偵錯]。 然後將下列環境變數新增至專案：

    | 名稱 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 您完成[設定環境](set-up-environment.md)時所記下的值 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 您完成[設定環境](set-up-environment.md)時所記下的值 |

您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

## <a name="run-the-device-sample"></a>執行裝置範例

若要在 Windows 上於 Visual Studio 中追蹤程式碼執行，請在 program.cs 檔案中針對 `main` 函式新增中斷點。

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>檢閱程式碼

此範例會實作一個簡單的 IoT 隨插即用控溫器裝置。 此範例實作的模型不會使用 IoT 隨插即用[元件](concepts-components.md)。 [適用於控溫器裝置的 Digital Twins 定義語言 (DTDL) 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)會定義裝置所實作的遙測、屬性和命令。

裝置程式碼會使用標準 `CreateFromConnectionString` 方法連線到您的 IoT 中樞。 裝置會在連線要求中，傳送其實作之 DTDL 模型的模型識別碼。 傳送模型識別碼的裝置是 IoT 隨插即用裝置：

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

模型識別碼會儲存於程式碼中，如下列程式碼片段所示：

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

更新屬性、處理命令及傳送遙測資料的程式碼，與不使用 IoT 隨插即用慣例的裝置程式碼相同。

此範例會使用 JSON 程式庫來剖析從您的 IoT 中樞傳送的承載中的 JSON 物件：

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
