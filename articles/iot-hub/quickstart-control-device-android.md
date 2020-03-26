---
title: 從 Azure IoT 中樞控制裝置的快速入門 (Android) | Microsoft Docs
description: 在此快速入門中，您會執行兩個範例 Java 應用程式。 其中一個應用程式是服務應用程式，可以從遠端控制連線到中樞的裝置。 另一個應用程式則是在可從遠端控制且連線到中樞的實體或模擬裝置上執行。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 5a912199869cb2cce690a3502345ec6f9ca43d22
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675680"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>快速入門：控制連線到 IoT 中樞的裝置 (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

在此快速入門中，您可以使用直接方法來控制連線到 IoT 中樞的模擬裝置。 IoT 中樞是一項 Azure 服務，可讓您從雲端管理您的 IoT 裝置，並將大量的裝置遙測內嵌到雲端進行儲存或處理。 您可以使用直接方法，針對連線到 IoT 中樞的裝置，從遠端變更裝置的行為。 本快速入門使用兩個應用程式：可回應從後端服務應用程式所呼叫直接方法的模擬裝置應用程式，以及在 Android 裝置上呼叫直接方法的服務應用程式。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [具有 Android SDK 27 的 Android Studio](https://developer.android.com/studio/)。 如需詳細資訊，請參閱[安裝 Android Studio](https://developer.android.com/studio/install)。

* [Git](https://git-scm.com/download/)。

* [裝置 SDK 範例 Android 應用程式](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)，包含在 [Azure IoT 範例 (Java)](https://github.com/Azure-Samples/azure-iot-samples-java)中。

* [服務 SDK 範例 Android 應用程式](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)，包含在 Azure IoT 範例 (Java)中。

* 在您的防火牆中開啟的連接埠 8883。 本快速入門中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>新增 Azure IoT 擴充功能

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務 (DPS) 的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-android.md)，則可以略過此步驟，並使用已建立的 IoT 中樞。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-android.md)，則可以略過此步驟，並使用在先前快速入門中註冊的相同裝置。

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyAndroidDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyAndroidDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

## <a name="retrieve-the-service-connection-string"></a>擷取服務連接字串

您也需要_服務連接字串_，讓後端服務應用程式能夠連線到您的 IoT 中樞以便執行方法和擷取訊息。 下列命令可擷取 IoT 中樞的服務連接字串：

**YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

記下服務連接字串，它看起來如下：

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

您稍後會在快速入門中使用此值。 服務連接字符串與您在上一個步驟中記下的裝置連接字串不同。

## <a name="listen-for-direct-method-calls"></a>接聽直接方法呼叫

本快速入門中的這兩個範例都屬於 GitHub 上的 azure-iot-samples-java 存放庫。 下載或複製 [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存放庫。

裝置 SDK 的應用程式範例可以在實體 Android 裝置或 Android 模擬器上執行。 範例會連線到 IoT 中樞上的特定裝置端點、傳送模擬的遙測，並接聽來自中樞的直接方法呼叫。 在此快速入門中，來自中樞的直接方法呼叫會告知裝置變更其傳送遙測的間隔。 模擬的裝置在執行直接方法後，會將通知傳送回您的中樞。

1. 在 Android Studio 中開啟 GitHub 的 Android 專案範例。 專案會位在 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存放庫副本 (已複製或下載) 的下列目錄中。

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. 在 Android Studio 中開啟專案範例的 *gradle.properties*，並以您稍早所記錄的裝置連接字串取代 **Device_Connection_String** 預留位置。

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. 在 Android Studio 中，按一下 [檔案]   > [同步專案與 Gradle 檔案]  。 確認建置是否完成。

   > [!NOTE]
   > 如果專案同步失敗，其可能的原因包括：
   >
   > * 對您的 Android Studio 版本而言，專案中參考的 Android Gradle 外掛程式和 Gradle 的版本已過時。 請依照[這些指示](https://developer.android.com/studio/releases/gradle-plugin)，針對您的安裝參考及安裝正確版本的外掛程式和 Gradle。
   > * Android SDK 的授權合約尚未簽署。 請依照建置輸出中的指示，簽署授權合約並下載 SDK。

4. 完成建置後，按一下 [執行]   > [執行「應用程式」]  。 設定應用程式，以在實體 Android 裝置或 Android 模擬器上執行。 如需有關如何在實體裝置或模擬器上執行 Android 應用程式的詳細資訊，請參閱[執行應用程式](https://developer.android.com/training/basics/firstapp/running-app)。

5. 載入應用程式後，按一下 [啟動]  按鈕，以開始將遙測傳送至 IoT 中樞：

    ![用戶端裝置 android 應用程式的範例螢幕擷取畫面](media/quickstart-control-device-android/sample-screenshot.png)

當您執行服務 SDK 範例時，此應用程式必須持續在實體裝置或模擬器上執行，以更新執行間段期間的遙測間隔。

## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

在此節中，您將使用 Azure Cloud Shell 搭配 [IoT 擴充功能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)來監視由 Android 裝置傳送的訊息。

1. 使用 Azure Cloud Shell，執行下列命令以連線到您的 IoT 中樞並讀取訊息：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    下列螢幕擷取畫面顯示 IoT 中樞從 Android 裝置接收所傳送遙測時的輸出：

      ![使用 Azure CLI 來閱讀裝置訊息](media/quickstart-control-device-android/read-data.png)

依預設，遙測應用程式每隔 5 秒就會從 Android 裝置送出遙測。 在下一節中，您將使用直接方法呼叫來更新 Android IoT 裝置的遙測間隔。

## <a name="call-the-direct-method"></a>呼叫直接方法

服務應用程式會連線到 IoT 中樞上的服務端端點。 應用程式會透過您的 IoT 中樞對裝置進行直接方法呼叫，並接聽通知。

在不同的實體 Android 裝置或 Android 模擬器上執行此應用程式。

IoT 中樞後端服務應用程式通常會在雲端中執行，如此可較簡單地降低與機密連接字串相關的風險 (連接字串可控制 IoT 中樞上的所有裝置)。 在此範例中，我們會以 Android 應用程式的形式執行後端服務應用程式 (僅供示範用)。 本快速入門的其他語言版本會提供更符合典型後端服務應用程式的範例。

1. 在 Android Studio 中開啟 GitHub 的 Android 專案服務範例。 專案會位在 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存放庫副本 (已複製或下載) 的下列目錄中。

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. 在 Android Studio 中，開啟範例專案的 gradle.properties  。 以您稍早記下的服務連接字串和您註冊的 Android 裝置識別碼來更新 **ConnectionString** 和 **DeviceId** 屬性值。

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. 在 Android Studio 中，按一下 [檔案]   > [同步專案與 Gradle 檔案]  。 確認建置是否完成。

   > [!NOTE]
   > 如果專案同步失敗，其可能的原因包括：
   >
   > * 對您的 Android Studio 版本而言，專案中參考的 Android Gradle 外掛程式和 Gradle 的版本已過時。 請依照[這些指示](https://developer.android.com/studio/releases/gradle-plugin)，針對您的安裝參考及安裝正確版本的外掛程式和 Gradle。
   > * Android SDK 的授權合約尚未簽署。 請依照建置輸出中的指示，簽署授權合約並下載 SDK。

4. 完成建置後，按一下 [執行]   > [執行「應用程式」]  。 設定應用程式，以在不同的實體 Android 裝置或 Android 模擬器上執行。 如需有關如何在實體裝置或模擬器上執行 Android 應用程式的詳細資訊，請參閱[執行應用程式](https://developer.android.com/training/basics/firstapp/running-app)。

5. 載入應用程式之後，將 [設定傳訊間隔]  的值設為 [1000]  ，然後按一下 [叫用]  。

    遙測傳訊間隔是以毫秒為單位。 裝置範例的預設遙測間隔會設為 5 秒。 此變更會更新 Android IoT 裝置，使遙測變成每秒傳送。

    ![輸入遙測間隔](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. 應用程式會收到通知，指出方法是否成功執行。

    ![直接方法通知](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您從後端應用程式對裝置進行直接方法呼叫，並在模擬裝置應用程式中回應直接方法呼叫。

若要了解如何將「裝置到雲端」訊息路由傳送至雲端中的不同目的地，請繼續下一個教學課程。

> [!div class="nextstepaction"]
> [教學課程：將遙測路由傳送至不同的端點以進行處理](tutorial-routing.md)
