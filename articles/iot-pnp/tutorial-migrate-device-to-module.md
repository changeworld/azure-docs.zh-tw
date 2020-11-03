---
title: 與一般 IoT 隨插即用模組連線 | Microsoft Docs
description: 在一般模組中使用 C# IoT 隨插即用裝置程式碼範例。
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d425152f83821e1d157065370bd02e2d990ec876
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426933"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>教學課程：與 IoT 隨插即用模組連線 (C#)

本教學課程說明如何與一般 IoT 隨插即用[模組](../iot-hub/iot-hub-devguide-module-twins.md)連線。

若裝置在連線到 IoT 中樞，並實作模型識別碼所指定數位分身定義語言 (DTDL) 模型中所述的屬性和方法時，發佈裝置的模型識別碼，則該裝置即為 IoT 隨插即用裝置。 若要深入了解裝置如何使用 DTDL 和模型識別碼，請參閱 [IoT 隨插即用開發人員指南](./concepts-developer-guide-device-csharp.md)。 模組會以相同的方式使用模型識別碼和 DTDL 模型。

為了示範如何實作 IoT 隨插即用模組，本教學課程將說明如何將控溫器 C# 裝置範例轉換為一般模組。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成此教學課程，請在您的本機 Windows 環境上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。

使用 Azure IoT 總管工具，將名為 **my-module-device** 的新裝置新增至您的 IoT 中樞。

將名為 **my-module** 的模組新增至 **my-module-device** ：

1. 在 Azure IoT 總管工具中，瀏覽至 **my-module-device** 裝置。

1. 選取 [模組身分識別]，然後選取 [+ 新增]。

1. 輸入 **my-module** 做為模組身分識別名稱，然後選取 [儲存]。

1. 在模組身分識別清單中，選取 **my-module** 。 然後複製 [主要連接字串]。 您稍後會在本教學課程中使用此模組連接字串。

1. 選取 [模組對應項] 索引標籤，並注意沒有所需或報告的屬性：

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>下載程式碼

如果您尚未這麼做，請將 Azure IoT 中樞裝置 C# SDK GitHub 存放庫複製到本機電腦：

在您選擇的資料夾中開啟命令提示字元。 使用下列命令，將 [Azure IoT C# 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫複製到下列位置：

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>準備專案

若要開啟並準備範例專案：

1. 在 Visual Studio 2019 中開啟 *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* 專案檔。

1. 在 Visual Studio 中，瀏覽至 [專案] > [控溫器屬性] > [偵錯]。 然後將下列環境變數新增至專案：

    | 名稱 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | 您先前所記下的模組連接字串 |

    若要深入了解範例設定，請參閱[範例讀我檔案](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md)。

## <a name="modify-the-code"></a>修改程式碼

若要修改程式碼以做為模組而非裝置：

1. 在 Visual Studio 中，開啟 Parameter.cs 並修改設定 **PrimaryConnectionString** 變數的那一行，如下所示：

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. 在 Visual Studio 中，開啟 Program.cs，並將 `DeviceClient` 類別的七個執行個體取代為 `ModuleClient` 類別。

    > [!TIP]
    > 使用 Visual Studio 搜尋將功能取代為 [大小寫須相符]，並啟用 [全字拼寫須相符] 將 `DeviceClient` 取代為 `ModuleClient`。

1. 在 Visual Studio 中，開啟 Thermostat.cs，並將 `DeviceClient` 類別的兩個執行個體取代為 `ModuleClient` 類別，如下所示。

1. 將變更儲存到您修改的檔案。

如果您執行程式碼，然後使用 Azure IoT 總管來檢視已更新的模組對應項，您會看到具有 [模型識別碼] 和 [模組報告] 屬性的更新裝置對應項：

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>與裝置模組互動

服務 SDK 可讓您擷取已連線 IoT 隨插即用裝置和模組的模型識別碼。 您可以使用服務 SDK 來設定可寫入的屬性和呼叫命令：

1. 在 Visual Studio 的另一個執行個體中，開啟 azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj 專案。

1. 在 Visual Studio 中，瀏覽至 [專案] > [控溫器屬性] > [偵錯]。 然後將下列環境變數新增至專案：

    | 名稱 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | 您完成[設定環境](set-up-environment.md)時所記下的值 |

    > [!TIP]
    > 您也可以在 Azure IoT 總管工具中找到您的 IoT 中樞連接字串。

1. 開啟 Program.cs 檔案，並修改呼叫命令的那一行，如下所示：

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. 在 Program.cs 檔案中，修改擷取裝置對應項的那一行，如下所示：

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. 請確定模組用戶端範例仍在執行中，然後執行此服務範例。 服務範例的輸出會顯示來自裝置對應項和命令呼叫的模型識別碼：

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    模組用戶端的輸出會顯示命令處理常式的回應：

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>轉換成 IoT Edge 模組

若要將此範例轉換成 IoT 隨插即用 IoT Edge 模組，您必須將應用程式容器化。 您不需要進行任何進一步的程式碼變更。 連接字串環境變數是在啟動時由 IoT Edge 執行時間插入。 如需深入了解，請參閱[使用 Visual Studio 2019 來開發適用於 Azure IoT Edge 的模組並針對其進行偵錯](../iot-edge/how-to-visual-studio-develop-module.md)。

若要了解如何部署您的容器化模組，請參閱：

* [在 Ubuntu 虛擬機器上執行 Azure IoT Edge](../iot-edge/how-to-install-iot-edge-ubuntuvm.md)。
* [在以 Debian 為基礎的 Linux 系統上安裝 Azure IoT Edge 執行階段](../iot-edge/how-to-install-iot-edge.md)。

您可以使用 Azure IoT 總管工具來查看：

* 模組對應項中 IoT Edge 裝置的模型識別碼。
* 來自 IoT Edge 裝置的遙測。
* IoT Edge 模組對應項屬性更新，以觸發 IoT 隨插即用通知。
* IoT Edge 模組會回應您的 IoT 隨插即用命令。

## <a name="next-steps"></a>下一步

在此教學課程中，您已了解如何將具有模組的 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](./concepts-developer-guide-device-csharp.md)