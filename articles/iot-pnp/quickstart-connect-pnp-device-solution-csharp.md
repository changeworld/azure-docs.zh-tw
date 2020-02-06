---
title: 與連線至 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置互動 | Microsoft Docs
description: 使用 C# (.NET) 連線至已連接 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置，並與其互動。
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963966"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>快速入門：與已連線至解決方案的 IoT 隨插即用預覽版裝置互動 (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 隨插即用預覽版讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 C# (搭配 .NET) 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您的開發機器上必須安裝 .NET Core (2.x.x 或 3.x.x)。 您可以從[下載 .NET Core](https://dotnet.microsoft.com/download/dotnet-core/)下載適用於多個平台的 .NET Core SDK 慣用版本。

您可以在本機終端機視窗中執行下列命令，確認開發機器上的 .NET 版本： 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_ (請記下以供後續使用)：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>執行範例裝置

在本快速入門中，您會使用以 C# 撰寫的範例環境感應器，作為 IoT 隨插即用裝置。 下列指示說明如何安裝和執行裝置：

1. 在您選擇的目錄中開啟終端機視窗。 執行下列命令，將[適用於 C# (.NET) 的 Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫複製到下列位置：

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. 此終端機視窗此時會作為您的_裝置_終端機。 移至您複製存放庫的資料夾，然後瀏覽至 **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** 資料夾。

1. 設定_裝置連接字串_：

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 使用下列命令建置必要套件和執行範例：

    ```cmd\sh
        dotnet run
    ```

1. 您會看到訊息指出裝置已成功註冊，正在等候來自雲端的更新。 這表示裝置現在可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 請勿關閉此終端機，稍後您將需要用它來確認服務範例也已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在本快速入門中，您會使用 C# 中的範例 IoT 解決方案，與範例裝置進行互動。

1. 開啟另一個終端機視窗 (這將是您的_服務_終端機)。 移至複製存放庫的資料夾，並瀏覽至 **/azure-iot-samples-csharp/digitaltwin/Samples/service** 資料夾。

1. 設定「IoT 中樞連接字串」  和「裝置識別碼」  ，以允許服務與這兩者連線：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>讀取屬性

1. 當您在終端機上連接_裝置_時，您會看到下列訊息，指出裝置處於線上狀態：

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. 移至_服務_終端機，並使用下列命令執行範例以讀取裝置資訊：

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. 在_服務_終端機輸出中，捲動至 `environmentalSensor` 元件。 您會看到用來指出裝置是否在線上的 `state` 屬性已回報為 true  ：

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>更新可寫入屬性

1. 移至_服務_終端機，並設定下列變數來定義要更新的屬性：
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. 使用下列命令執行範例以更新屬性：

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. _服務_終端機輸出會顯示已更新的裝置資訊。 捲動至 `environmentalSensor` 元件，以查看新的亮度值 42。

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. 移至您的_裝置_終端機，您會看到裝置已接收更新：

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. 回到您的_服務_終端機並執行下列命令，以再次取得裝置資訊，確認屬性已更新。
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. 在_服務_終端機輸出的 `environmentalSensor` 元件底下，您會看到回報的更新亮度值。 注意：裝置可能需要一段時間才能完成更新。 您可以重複此步驟，直到裝置實際處理屬性更新為止。
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>叫用命令

1. 移至_服務_終端機，並設定下列變數來定義要叫用的屬性：
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. 使用下列命令來執行範例，以叫用命令：

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. _服務_終端機中的輸出應會顯示下列確認：

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. 移至_裝置_終端機，您會看到命令已認可：

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
