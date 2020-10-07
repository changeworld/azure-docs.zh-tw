---
title: 與連線至 Azure IoT 解決方案的 IoT 隨插即用裝置互動 (Java) | Microsoft Docs
description: 使用 Java 連線至已連線 Azure IoT 解決方案的 IoT 隨插即用裝置，並與其互動。
author: ericmitt
ms.author: ericmitt
ms.date: 9/17/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0f43b667b94e39548d81e6c6258d987f47074cb1
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761323"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-java"></a>快速入門：與已連線至解決方案的 IoT 隨插即用裝置互動 (Java)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 隨插即用讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 Java 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成此快速入門，請在您的本機 Windows 環境上安裝下列軟體：

* Java SE 開發套件 8。 在 [Azure 和 Azure Stack 的 Java 長期支援](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)中，選取 [長期支援] 下的 [Java 8]。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用範例程式碼複製 SDK 存放庫

如果您已完成[快速入門：將在 Windows 上執行的範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 (Java)](quickstart-connect-device-java.md)，您已經複製了存放庫。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將[適用於 Java 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) GitHub 存放庫複製到下列位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>建置並執行範例裝置

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要深入了解範例設定，請參閱[範例讀我檔案](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md)。

在本快速入門中，您可以使用以 Java 撰寫的範例控溫器裝置，作為 IoT 隨插即用裝置。 若要執行範例裝置：

1. 開啟終端機視窗，並瀏覽至本機資料夾，此資料夾中包含從 GitHub 複製的適用於 Java 存放庫的 Microsoft Azure IoT SDK。

1. 此終端機視窗會作為您的**裝置**終端機。 移至所複製存放庫的根資料夾。 執行下列命令以安裝所有相依性：

1. 執行下列命令以建置範例裝置應用程式：

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. 若要執行範例裝置應用程式，請瀏覽至 device\iot-device-samples\pnp-device-sample\thermostat-device-sample 資料夾，然後執行下列命令：

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例裝置保持執行狀態。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在 [設定 IoT 隨插即用的環境快速入門和教學課程](set-up-environment.md)，您建立了兩個環境變數，以設定連線至 IoT 中樞和裝置的範例：

* **IOTHUB_CONNECTION_STRING**：您先前記下的 IoT 中樞連接字串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在本快速入門中，您會使用以 Java 撰寫的範例 IoT 解決方案，與您剛剛設定的範例裝置進行互動。

> [!NOTE]
> 這個範例會使用 **IoT 中樞服務用戶端** 中的 **com.microsoft.azure.sdk.iot.service.*;** 命名空間。 若要深入了解如何擷取模型識別碼，請參閱[開發人員指南](concepts-developer-guide-service.md)。

1. 開啟另一個終端機視窗，作為您的**服務**終端機。

1. 在複製的 Java SDK 存放庫中，瀏覽至 ervice\iot-service-samples\pnp-service-sample\thermostat-service-samples 資料夾。

1. 若要執行範例服務應用程式，請執行下列命令：

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-digital-twin"></a>取得數位分身

下列程式碼片段示範如何在服務中擷取裝置對應項：

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-digital-twin"></a>更新數位分身

下列程式碼片段示範如何使用「修補程式」，透過數位對應項來更新屬性：

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

裝置輸出會顯示裝置回應此屬性更新的方式。

### <a name="invoke-a-command"></a>叫用命令

下列程式碼片段示範如何在裝置上呼叫命令：

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

裝置輸出會顯示裝置回應此命令的方式。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](concepts-developer-guide-device-csharp.md)
