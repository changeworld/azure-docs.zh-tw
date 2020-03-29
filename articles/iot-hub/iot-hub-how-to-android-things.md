---
title: 使用 Azure IoT SDK 針對 Android Things 平台進行開發 | Microsoft Docs
description: 開發人員指南 - 了解如何使用 Azure IoT 中樞 SDK 在 Android Things 上進行開發。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: a06583e9aab4b082517d47c1022f7bec5184b9bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673388"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>使用 Azure IoT SDK 針對 Android Things 平台進行開發

[Azure IoT 中樞 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 為 Windows、Linux、OSX、MBED 等常用平台及行動裝置平台 (例如 Android 和 iOS) 提供第一層支援。  我們的承諾是在 IoT 部署中提供更優質的選擇和彈性，Java SDK 也提供 [Android Things](https://developer.android.com/things/) 平台。  開發人員可以利用 Android Things 作業系統在裝置端的優點，同時使用 [Azure IoT 中樞](about-iot-hub.md)作為訊息集中中樞，將同時連線的裝置擴充至數百萬個。

本教學課程說明使用 Azure IoT Java SDK 在 Android Things 上建置裝置端應用程式的步驟。

## <a name="prerequisites"></a>Prerequisites

* 支援 Android Things 的硬體，且可執行 Android Things OS。  您可以依循 [Android Things 文件](https://developer.android.com/things/get-started/kits#flash-at)，以了解如何刷新 Android Things OS。  請確定您的 Android Things 裝置已連接必要週邊設備 (例如鍵盤、顯示器和滑鼠) 並已連線到網際網路。  本教學課程使用 Raspberry Pi 3。

* 最新版的 [Android Studio](https://developer.android.com/studio/)

* 最新版的 [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以新增 IoT 中樞 CLI 擴充功能和建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyAndroidThingsDevice** ：這是為註冊設備指定的名稱。 使用如顯示的 MyAndroidThingsDevice。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. 在 Azure 雲外殼中運行以下命令，以獲取剛剛註冊*的設備的設備連接字串*。 下面`YourIoTHubName`替換為您為 IoT 中心選擇的名稱。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

## <a name="building-an-android-things-application"></a>建置 Android Things 應用程式

1. 建置 Android Things 應用程式的第一步是連接您的 Android Things 裝置。 將您的 Android Things 裝置連接到顯示器，然後將它連線到網際網路。 Android Things 提供如何連線到 WiFi 的[文件](https://developer.android.com/things/get-started/kits)。 在您連線到網際網路之後，請記下 [網路] 底下列出的 IP 位址。

2. 使用 [adb](https://developer.android.com/studio/command-line/adb) 工具，利用上述記下的 IP 位址來連線到您的 Android Things 裝置。 從終端機使用此命令，再次檢查連線。 您應該將您的設備列為"已連接"。

   ```
   adb devices
   ```

3. 從此[存放庫](https://github.com/Azure-Samples/azure-iot-samples-java)或使用 Git 下載 Android/Android Things 範例。

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. 在 Android Studio 中，開啟 Android 專案 (位於 "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample")。

5. 開啟 gradle.properties 檔案，並使用您先前記下的裝置連接字串取代 "Device_connection_string"。
 
6. 按一下 [執行 - 偵錯]，並選取您的裝置，以將此程式碼部署至您的 Android Things 裝置。

7. 當應用程式成功啟動之後，您可以看見應用程式在您的 Android Things 裝置上執行。 此範例應用程式會傳送隨機產生的溫度讀數。

## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

您可以透過 IoT 中樞檢視所收到的資料。 IoT 中樞 CLI 擴充功能可以連線到 IoT 中樞上的服務端**事件**端點。 擴充功能會接收模擬裝置所傳送的「裝置到雲端」訊息。 IoT 中樞後端應用程式通常在雲端中執行，以接收和處理「裝置到雲端」訊息。

在 Azure Cloud Shell 中執行下列命令，以您的 IoT 中樞名稱取代 `YourIoTHubName`：

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

* 深入了解[如何使用 IoT 中樞 SDK 來管理連線能力和可靠傳訊](iot-hub-reliability-features-in-sdks.md)。
* 深入了解如何對 iOS 和 Android 等[行動平台進行開發](iot-hub-how-to-develop-for-mobile-devices.md)。
* [Auzre IoT SDK 平台支援](iot-hub-device-sdk-platform-support.md)
