---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7961dae0ef227a7e321992e0b239f7a3e6e66b0a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511629"
---
此快速入門顯示如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具檢視其所傳送的遙測資料。 範例應用程式是以 Java 撰寫的，並隨附於適用於 Java 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

若要在 Windows 上完成此快速入門，請在您的本機 Windows 環境上安裝下列軟體：

* Java SE 開發套件 8。 在 [Azure 和 Azure Stack 的 Java 長期支援](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)中，選取 [長期支援] 下的 [Java 8]。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

## <a name="download-the-code"></a>下載程式碼

在此快速入門中，您要準備可用來複製及建置 Azure IoT 中樞裝置 Java SDK 的開發環境。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將 [Azure IoT Java SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-java) \(英文\) GitHub 存放庫複製到此位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>建置程式碼

在 Windows 上，瀏覽至所複製 Java SDK 存放庫的根資料夾。

執行下列命令以建置範例應用程式：

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>執行裝置範例

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要深入了解範例設定，請參閱[範例讀我檔案](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md)。

瀏覽至 *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample* 資料夾。

若要執行範例應用程式，請執行下列命令：

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>檢閱程式碼

此範例會實作一個簡單的 IoT 隨插即用控溫器裝置。 此範例實作的模型不會使用 IoT 隨插即用[元件](../articles/iot-pnp/concepts-components.md)。 [適用於控溫器裝置的 DTDL 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) \(英文\) 會定義裝置所實作的遙測、屬性和命令。

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

更新屬性、處理命令及傳送遙測資料的程式碼，與不使用 IoT 隨插即用慣例的裝置程式碼相同。

此範例會使用 JSON 程式庫來剖析從您的 IoT 中樞傳送的承載中的 JSON 物件：

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```
