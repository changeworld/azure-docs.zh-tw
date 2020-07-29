---
title: 快速入門：使用 JAVA 從 Azure IoT 中樞控制裝置
description: 在此快速入門中，您會執行兩個範例 Java 應用程式。 其中一個應用程式是後端應用程式，可以從遠端控制連線到中樞的裝置。 另一個應用程式則是模擬可以從遠端控制且連線到中樞的裝置。
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
ms.date: 06/21/2019
ms.openlocfilehash: 3c6a8285c672e6683a259b2a65be4e6ecbd4aafa
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322273"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>快速入門：使用 Java 來控制連線到 Azure IoT 中樞的裝置

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

在本快速入門中，您可以使用直接方法來控制使用 Java 應用程式連線到 Azure IoT 中樞的模擬裝置。 IoT 中樞是一項 Azure 服務，可讓您從雲端管理您的 IoT 裝置，並將大量的裝置遙測內嵌到雲端進行儲存或處理。 您可以使用直接方法，針對連線到 IoT 中樞的裝置，從遠端變更裝置的行為。 本快速入門使用兩個 Java 應用程式：可回應從後端應用程式所呼叫直接方法的模擬裝置應用程式，以及在模擬裝置上呼叫直接方法的服務應用程式。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* Java SE 開發套件 8。 在 [Azure 和 Azure Stack 的 Java 長期支援](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)中，選取 [長期支援]  下的 [Java 8]  。

* [Apache Maven 3](https://maven.apache.org/download.cgi)。

* [範例 Java 專案](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip)。

* 在您的防火牆中開啟的連接埠 8883。 本快速入門中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

您可以使用下列命令，以確認開發電腦上目前的 Java 版本：

```cmd/sh
java -version
```

您可以使用下列命令，以確認開發電腦上目前的 Maven 版本：

```cmd/sh
mvn --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>新增 Azure IoT 擴充功能

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務 (DPS) 的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-java.md)，則可以略過此步驟。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-java.md)，則可以略過此步驟。

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyJavaDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyJavaDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyJavaDevice \
      --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

## <a name="retrieve-the-service-connection-string"></a>擷取服務連接字串

您也需要_服務連接字串_，讓後端應用程式能夠連線到您的 IoT 中樞並擷取訊息。 下列命令可擷取 IoT 中樞的服務連接字串：

**YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

記下服務連接字串，它看起來如下：

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

您稍後會在快速入門中使用此值。 服務連接字符串與您在上一個步驟中記下的裝置連接字串不同。

## <a name="listen-for-direct-method-calls"></a>接聽直接方法呼叫

模擬裝置應用程式會連線到 IoT 中樞上的特定裝置端點、傳送模擬的遙測，並接聽來自中樞的直接方法呼叫。 在此快速入門中，來自中樞的直接方法呼叫會告知裝置變更其傳送遙測的間隔。 模擬的裝置在執行直接方法後，會將通知傳送回您的中樞。

1. 在本機終端機視窗中，瀏覽至範例 Java 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device-2** 資料夾。

2. 在您選擇的文字編輯器中開啟 **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** 檔案。

    使用您稍早所記錄的裝置連接字串來取代 `connString` 變數的值。 然後將變更儲存到 **SimulatedDevice.java**。

3. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫，並建置模擬裝置應用程式：

    ```cmd/sh
    mvn clean package
    ```

4. 在本機終端機視窗中，執行下列命令以執行模擬裝置應用程式：

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![由裝置傳送到您 IoT 中樞的遙測輸出](./media/quickstart-control-device-java/iot-hub-application-send-telemetry-output.png)

## <a name="call-the-direct-method"></a>呼叫直接方法

後端應用程式會連線到 IoT 中樞上的服務端端點。 應用程式會透過您的 IoT 中樞對裝置進行直接方法呼叫，並接聽通知。 IoT 中樞後端應用程式通常會在雲端中執行。

1. 在另一個本機終端機視窗中，瀏覽至範例 Java 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\back-end-application** 資料夾。

2. 在您選擇的文字編輯器中開啟 **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** 檔案。

    使用稍早所記錄的服務連接字串來取代 `iotHubConnectionString` 變數的值。 然後將您的變更儲存到 **BackEndApplication.java**。

3. 在本機終端機視窗中，執行下列命令安裝所需的程式庫並建置後端應用程式：

    ```cmd/sh
    mvn clean package
    ```

4. 在本機終端機視窗中，執行下列命令以執行後端應用程式：

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    下列螢幕擷取畫面顯示應用程式對裝置進行直接方法呼叫並接收通知時的輸出：

    ![應用程式透過您的 IoT 中樞做出直接方法呼叫的輸出](./media/quickstart-control-device-java/iot-hub-direct-method-call-output.png)

    執行後端應用程式之後，在執行模擬裝置的主控台視窗中將會出現一則訊息，且它傳送訊息的速率也會變更：

    ![來自裝置的主控台訊息顯示其變更的速率](./media/quickstart-control-device-java/iot-hub-sent-message-change-rate.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您從後端應用程式對裝置進行直接方法呼叫，並在模擬裝置應用程式中回應直接方法呼叫。

若要了解如何將「裝置到雲端」訊息路由傳送至雲端中的不同目的地，請繼續下一個教學課程。

> [!div class="nextstepaction"]
> [教學課程：將遙測路由傳送至不同的端點以進行處理](tutorial-routing.md)
