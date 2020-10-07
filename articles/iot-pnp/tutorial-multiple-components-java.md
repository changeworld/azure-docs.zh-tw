---
title: 將 IoT 隨插即用範例 Java 元件裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 建置並執行使用多個元件的 IoT 隨插即用範例 Java 裝置程式碼，並連線至 IoT 中樞。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a7c1f0d207a113b2c12010cbc0a8876edd9269bc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577249"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-java"></a>教學課程：將範例 IoT 隨插即用多重元件裝置應用程式連線至 IoT 中樞 (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

本教學課程說明如何建置多個元件範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure CLI 檢視其所傳送的遙測資料。 範例應用程式是以 Java 撰寫的，並隨附於適用於 Java 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure CLI 來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

本教學課程說明如何建置具有元件的範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具來檢視其傳送至中樞的資訊。 範例應用程式是以 Java 撰寫的，並隨附於適用於 Java 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成此教學課程，請在您的本機 Windows 環境上安裝下列軟體：

* Java SE 開發套件 8。 在 [Azure 和 Azure Stack 的 Java 長期支援](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)中，選取 [長期支援] 下的 [Java 8]。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

## <a name="download-the-code"></a>下載程式碼

如果您已完成[快速入門：將在 Windows 上執行的範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 (Java)](quickstart-connect-device-java.md)，您已經複製了存放庫。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將 [Azure IoT Java SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-java) GitHub 存放庫複製到下列位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

預期此作業需要幾分鐘的時間才能完成。

## <a name="build-the-code"></a>建置程式碼

在 Windows 上，瀏覽至所複製 Java SDK 存放庫的根資料夾。 執行下列命令以建置相依性：

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>執行裝置範例

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要執行範例應用程式，請瀏覽至 *\device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample* 資料夾，然後執行下列命令：

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>檢閱程式碼

這個範例會實作 IoT 隨插即用的溫度控制器裝置。 這個範例所實作的模型會使用[多個元件](concepts-components.md)。 [適用於溫度裝置的 Digital Twins 定義語言 (DTDL) 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) \(英文\) 會定義裝置所實作的遙測、屬性和命令。

裝置程式碼會使用標準的 `DeviceClient` 類別連線到您的 IoT 中樞。 裝置會在連線要求中，傳送其實作之 DTDL 模型的模型識別碼。 傳送模型識別碼的裝置是 IoT 隨插即用裝置：

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

模型識別碼會儲存於程式碼中，如下列程式碼片段所示：

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

當裝置連線到您的 IoT 中樞之後，程式碼就會註冊命令處理常式。

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

針對這兩個控溫器元件上所需的屬性更新，有個別的處理常式：

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

範例程式碼會從每個控溫器元件傳送遙測：

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading` 方法會使用 `PnpHhelper` 類別來建立每個元件的訊息：

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper` 類別包含可以搭配多個元件模型使用的其他範例方法。

使用 Azure IoT 總管工具，檢視來自這兩個控溫器元件的遙測和屬性：

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Azure IoT 總管中的多個元件裝置":::

您也可以使用 Azure IoT 總管工具，在這兩個控溫器元件的其中之一或是預設元件中呼叫命令。

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何將具有元件的 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](concepts-developer-guide-device-csharp.md)
