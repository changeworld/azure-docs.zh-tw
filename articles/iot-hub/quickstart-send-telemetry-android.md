---
title: 將遙測傳送至 Azure IoT 中樞的快速入門 (Android) | Microsoft Docs
description: 本快速入門中，您可以執行 Android 應用程式範例，將模擬的遙測傳送到 IoT 中樞以及從 IoT 中樞讀取遙測，以便在雲端中處理。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: ef5f81090f4d06f15f1a7263699961cd360e12e5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842187"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>快速入門：從 Android 裝置傳送 IoT 遙測

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

在本快速入門中，您將會從執行於實體或模擬裝置的 Android 應用程式中，將遙測傳送至 Azure IoT 中樞。 IoT 中樞是一項 Azure 服務，可讓您從 IoT 裝置將大量的遙測擷取到雲端進行儲存或處理。 本快速入門會使用預先撰寫的 Android 應用程式來傳送遙測。 我們將使用 Azure Cloud Shell 從 IoT 中樞讀取遙測。 在執行應用程式之前，您需要建立一個 IoT 中樞，並向中樞註冊一個裝置。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [具有 Android SDK 27 的 Android Studio](https://developer.android.com/studio/)。 如需詳細資訊，請參閱 [android-installation](https://developer.android.com/studio/install)。 本中的範例會使用 Android SDK 27。

* [範例 Android 應用程式](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)。 此範例屬於 [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存放庫。

* 在您的防火牆中開啟的連接埠 8883。 本快速入門中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyAndroidDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyAndroidDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得「裝置連接字串」  ：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後將會在本快速入門中使用此值來傳送遙測。

## <a name="send-simulated-telemetry"></a>傳送模擬的遙測

1. 在 Android Studio 中開啟 GitHub 的 Android 專案範例。 專案會位在 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 存放庫副本 (已複製或下載) 的下列目錄中： *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*。

2. 在 Android Studio 中開啟專案範例的 *gradle.properties*，並以您稍早所記錄的裝置連接字串取代 **Device_Connection_String** 預留位置。

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. 在 Android Studio 中，按一下 [檔案] > [同步專案與 Gradle 檔案]。 確認建置是否完成。

   > [!NOTE]
   > 如果專案同步失敗，其可能的原因包括：
   >
   > * 對您的 Android Studio 版本而言，專案中參考的 Android Gradle 外掛程式和 Gradle 的版本已過時。 請依照[這些指示](https://developer.android.com/studio/releases/gradle-plugin)，針對您的安裝參考及安裝正確版本的外掛程式和 Gradle。
   > * Android SDK 的授權合約尚未簽署。 請依照建置輸出中的指示，簽署授權合約並下載 SDK。

4. 完成建置後，按一下 [執行] > [執行「應用程式」]。 設定應用程式，以在實體 Android 裝置或 Android 模擬器上執行。 如需有關如何在實體裝置或模擬器上執行 Android 應用程式的詳細資訊，請參閱[執行應用程式](https://developer.android.com/training/basics/firstapp/running-app)。

5. 載入應用程式後，按一下 [啟動] 按鈕，以開始將遙測傳送至 IoT 中樞：

    ![Application](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

在此節中，您將使用 Azure Cloud Shell 搭配 [IoT 擴充功能](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)來監視由 Android 裝置傳送的裝置訊息。

1. 使用 Azure Cloud Shell，執行下列命令以連線到您的 IoT 中樞並讀取訊息：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    下列螢幕擷取畫面顯示 IoT 中樞從 Android 裝置接收所傳送遙測時的輸出：

      ![使用 Azure CLI 來閱讀裝置訊息](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您會設定 IoT 中樞、註冊裝置、使用 Android 應用程式將模擬的遙測傳送到中樞，並使用 Azure Cloud Shell 從中樞讀取遙測。

若要了解如何從後端應用程式控制您的模擬裝置，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [快速入門：控制連線到 IoT 中樞的裝置](quickstart-control-device-android.md)
