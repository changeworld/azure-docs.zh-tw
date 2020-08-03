---
title: 與已連線至解決方案的 IoT 隨插即用預覽版裝置互動 - Java | Microsoft Docs
description: 使用 Java 連線至已連接 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置，並與其互動。
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 752d1d925c55ed79c7eb1673c6602adb9c2371fb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320981"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>快速入門：與已連線至解決方案的 IoT 隨插即用預覽版裝置互動 (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 隨插即用預覽版讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 Java 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您的開發機器上必須要有 Java SE 8。 您也需要安裝 Maven 3。

如需如何備妥這些項目的詳細資訊，請在適用於 Java 的 Microsoft Azure IoT 裝置 SDK 中參閱[準備您的開發環境](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_ (請記下以供後續使用)：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>執行範例裝置

在本快速入門中，您會使用以 Java 撰寫的範例環境感應器，作為 IoT 隨插即用裝置。 下列指示說明如何安裝和執行裝置：

1. 在您選擇的目錄中開啟終端機視窗。 執行下列命令，將[適用於 Java 的 Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub 存放庫複製到下列位置：

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. 此終端機視窗此時會作為您的_裝置_終端機。 移至您複製存放庫的資料夾，然後瀏覽至 **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** 資料夾。 藉由執行下列命令，安裝必要程式庫並建置模擬裝置應用程式：

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. 設定_裝置連接字串_：

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 執行下列命令來執行裝置資料夾中的範例。

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. 您會看到指出裝置已連線、正在執行各種安裝步驟，以及等待服務更新的訊息，然後顯示遙測記錄。 這表示裝置現在可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。 請勿關閉此終端機，稍後您將需要用它來確認服務範例也已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在本快速入門中，您會使用 Java 中的範例 IoT 解決方案，與範例裝置進行互動。

1. 開啟另一個終端機視窗 (這將是您的_服務_終端機)。 移至您複製存放庫的資料夾，然後瀏覽至 **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample** 資料夾。

1. 藉由執行下列命令，安裝必要程式庫並建置服務範例：

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. 設定「IoT 中樞連接字串」  和「裝置識別碼」  ，以允許服務與這兩者連線：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>讀取屬性

1. 當您在終端機上連接_裝置_時，其中一個輸出訊息是指出其線上狀態的下列訊息。 用來指出裝置是否在線上的 `state` 屬性為 true  ：

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. 移至_服務_終端機，並使用下列命令執行服務範例以讀取裝置資訊：

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. 在_服務_終端機輸出中，捲動至 `environmentalSensor` 元件。 您會看到 `state` 屬性已回報為 _true_：
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. 使用下列命令執行服務範例以更新屬性：

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. _服務_終端機輸出會顯示已更新的裝置資訊。 捲動至 `environmentalSensor` 元件，以查看新的亮度值 42。

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. 移至您的_裝置_終端機，您會看到裝置已接收更新：

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. 回到您的_服務_終端機並執行下列命令，以再次取得裝置資訊，確認屬性已更新。
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. 在_服務_終端機輸出的 `environmentalSensor` 元件底下，您會看到回報的更新亮度值。 注意：裝置可能需要一段時間才能完成更新。 您可以重複此步驟，直到裝置實際處理屬性更新為止。
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>叫用命令

1. 移至_服務_終端機，並設定下列變數來定義要叫用的屬性：
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. 使用下列命令來執行服務範例，以叫用命令：

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. _服務_終端機中的輸出應會顯示下列確認：

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. 移至_裝置_終端機，您會看到命令已認可：

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
