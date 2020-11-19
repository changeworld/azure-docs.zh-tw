---
title: 快速入門：使用 JAVA 將遙測傳送至 Azure IoT 中樞
description: 本快速入門中，您會執行兩個範例 Java 應用程式，以將模擬的遙測資料傳送至 IoT 中樞和從 IoT 中樞讀取遙測資料，以便在雲端中處理。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 05/26/2020
ms.openlocfilehash: 76326d0b68a3ff71bf95c09147d003e769e103db
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844637"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>快速入門：將遙測傳送至 Azure IoT 中樞，並使用 Java 應用程式加以讀取

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

在本快速入門中，您會將遙測傳送至 Azure IoT 中樞，並使用 Java 應用程式加以讀取。 IoT 中樞是一項 Azure 服務，可讓您從 IoT 裝置將大量的遙測擷取到雲端進行儲存或處理。 本快速入門會使用兩個預先撰寫的 Java 應用程式：一個用來傳送遙測，而另一個用來從中樞讀取遙測。 在執行下列兩個應用程式之前，您需要建立一個 IoT 中樞，並向中樞註冊一個裝置。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* Java SE 開發套件 8。 在 [Azure 和 Azure Stack 的 Java 長期支援](/java/azure/jdk/?view=azure-java-stable)中，選取 [長期支援]  下的 [Java 8]  。

    您可以使用下列命令，以確認開發電腦上目前的 Java 版本：

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi)。

    您可以使用下列命令，以確認開發電腦上目前的 Maven 版本：

    ```cmd/sh
    mvn --version
    ```

* [範例 Java 專案](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip)。

* 在您的防火牆中開啟的連接埠 8883。 本快速入門中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyJavaDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyJavaDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得「裝置連接字串」  ：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後將會在快速入門中使用此值。

3. 您還需要 IoT 中樞的「事件中樞相容端點」、「事件中樞相容路徑」和「服務主要金鑰」，以便讓後端應用程式連線到 IoT 中樞並擷取訊息。 下列命令會針對您的 IoT 中樞擷取這些值：

     **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    請記下這三個值，您稍後將會在快速入門中使用。

## <a name="send-simulated-telemetry"></a>傳送模擬的遙測

模擬裝置應用程式會連線到 IoT 中樞上的裝置特定端點，並且傳送模擬的溫度和溼度遙測。

1. 在本機終端機視窗中，瀏覽至範例 Java 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device** 資料夾。

2. 在您選擇的文字編輯器中開啟 **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** 檔案。

    使用您稍早所記錄的裝置連接字串來取代 `connString` 變數的值。 然後將變更儲存到 **SimulatedDevice.java**。

3. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫，並建置模擬裝置應用程式：

    ```cmd/sh
    mvn clean package
    ```

4. 在本機終端機視窗中，執行下列命令以執行模擬裝置應用程式：

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![由裝置傳送到 IoT 中樞的遙測輸出](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

後端應用程式會連線到 IoT 中樞上的服務端 **事件** 端點。 應用程式會接收模擬裝置所傳送的「裝置到雲端」訊息。 IoT 中樞後端應用程式通常在雲端中執行，以接收和處理「裝置到雲端」訊息。

1. 在另一個本機終端機視窗中，瀏覽至範例 Java 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\read-d2c-messages** 資料夾。

2. 在您選擇的文字編輯器中開啟 **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** 檔案。 更新下列變數，並將您的變更儲存至檔案。

    | 變數 | 值 |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | 使用您稍早所記錄的事件中樞相容端點來取代變數的值。 |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | 使用您稍早所記錄的事件中樞相容路徑來取代變數的值。 |
    | `IOT_HUB_SAS_KEY`                | 使用您稍早所記錄的服務主要金鑰來取代變數的值。 |

3. 在本機終端機視窗中，執行下列命令安裝所需的程式庫並建置後端應用程式：

    ```cmd/sh
    mvn clean package
    ```

4. 在本機終端機視窗中，執行下列命令以執行後端應用程式：

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    下列螢幕擷取畫面顯示由模擬裝置傳送遙測至中樞時，後端應用程式接收遙測的輸出：

    ![當後端應用程式接收傳送至 IoT 中樞的遙測時輸出](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您會設定 IoT 中樞、註冊裝置、使用 Java 應用程式將模擬的遙測傳送到中樞，並使用簡單的後端應用程式從中樞讀取遙測。

若要了解如何從後端應用程式控制您的模擬裝置，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [快速入門：控制連線到 IoT 中樞的裝置](quickstart-control-device-java.md)
