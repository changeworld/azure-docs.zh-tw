---
title: 將 IoT 隨插即用預覽版的範例 C# 裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 在 Windows 上建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 015e20fa975563fee8ac2d61f9bad1f9f03738ce
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352569"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c"></a>快速入門：將在 Windows 上執行的範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞 (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具檢視其所傳送的遙測資料。 範例應用程式是以 CSharp 撰寫的，並隨附於適用於 C# 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

若要在 Windows 上完成本快速入門，請在您的本機 Windows 環境上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT 總管

若要在本快速入門的第二個部分與範例裝置互動，您可以使用 **Azure IoT 總管**工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 您也可以使用 Azure IoT 總管工具來尋找 IoT 中樞連接字串。

執行下列命令，針對您新增至中樞的裝置取得_裝置連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>下載程式碼

在本快速入門中，您會準備可用來複製及建置 Azure IoT 中樞裝置 C# SDK 的開發環境。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將 [Azure IoT C# SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-csharp) GitHub 存放庫複製到下列位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>建置程式碼

在 Visual Studio 2019 中開啟 azure-iot-sdk-csharp/iothub/device/samples/PnpDeviceSamples/Thermostat/Thermostat.csproj 專案檔。

您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

## <a name="run-the-device-sample"></a>執行裝置範例

建立名為 **IOTHUB_DEVICE_CONNECTION_STRING** 的環境變數，來儲存您先前記下的裝置連接字串。

若要在 Windows 上 Visual Studio 追蹤程式碼執行，請在 program.cs 檔案中新增 `main` 函式的中斷點。

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，使用 Azure IoT 總管工具來驗證其是否正確運作。

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

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
