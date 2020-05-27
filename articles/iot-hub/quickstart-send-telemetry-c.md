---
title: 將遙測傳送至 Azure IoT 中樞快速入門 (C) | Microsoft Docs
description: 在此快速入門中，您會執行兩個範例 C 應用程式，以將模擬的遙測資料傳送至 IoT 中樞以及從 IoT 中樞讀取遙測資料，以便在雲端中處理。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 4fbebfe2ec554c7c9c2f85a5657daeeee8dae31a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727075"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>快速入門：將遙測從裝置傳送至 IoT 中樞，並使用後端應用程式讀取遙測 (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中樞是一項 Azure 服務，可讓您從 IoT 裝置將大量的遙測擷取到雲端進行儲存或處理。 在此快速入門中，您透過 IoT 中樞，將遙測從模擬裝置應用程式傳送到後端應用程式以進行處理。

此快速入門使用來自[適用於 C 的 Azure IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md) 的 C 範例應用程式來將遙測資料傳送到 IoT 中樞。 Azure IoT 裝置 SDK 是針對可攜性與廣泛的平台相容性以 [ANSI C (C99)](https://wikipedia.org/wiki/C99) 所撰寫。 執行範例應用程式之前，您將先建立 IoT 中樞並向該中樞註冊模擬裝置。

此文章是針對 Windows 所撰寫，但您也可以在 Linux 上完成此快速入門。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* 安裝 [Visual Studio 2019](https://www.visualstudio.com/vs/) 並啟用[使用 C++ 的桌面開發](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作負載。

* 安裝最新版的 [Git](https://git-scm.com/download/)。

* 請確定您的防火牆已開啟連接埠 8883。 本快速入門中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。


* 執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務 (DPS) 的特定命令新增至 Azure CLI。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>準備開發環境

針對此快速入門，您將使用[適用於 C 的 Azure IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md)。 

針對下列環境，您可以透過安裝這些套件與程式庫來使用該 SDK：

* **Linux**：Ubuntu 16.04 與 18.04 可使用 apt-get 套件，適用的 CPU 架構如下：amd64、arm64、armhf 與 i386。 如需詳細資訊，請參閱[在Ubuntu 上使用 apt-get 來建立 C 裝置用戶端專案](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md) \(英文\)。

* **mbed**：針對在 mbed 平台上建立裝置應用程式的開發人員，我們已發佈可讓您快速了解如何使用 Azure IoT 中樞的程式庫與範例。 如需詳細資訊，請參閱[使用 mbed 程式庫](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed) \(英文\)。

* **Arduino**：如果您在 Arduino 上開發，您可以利用 Arduino IDE 程式庫管理員中可用的 Azure IoT 程式庫。 如需詳細資訊，請參閱 [適用於 Arduino 的 Azure IoT 中樞](https://github.com/azure/azure-iot-arduino)。

* **iOS**：IoT 中樞裝置 SDK 能以 CocoaPod 形式取得並用於 Mac 與 iOS 裝置開發。 如需詳細資訊，請參閱 [Microsoft Azure IoT 的 iOS 範例](https://cocoapods.org/pods/AzureIoTHubClient) \(英文\)。

不過，在此快速入門中，您將準備用來從 GitHub 複製並建置 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) \(英文\) 的開發環境。 GitHub 上的 SDK 包括此快速入門中使用的範例程式碼。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始安裝 `CMake`**之前**，請務必將 Visual Studio 先決條件 (Visual Studio 和「使用 C++ 進行桌面開發」工作負載) 安裝在您的機器上。 在符合先決條件，並且驗證過下載項目之後，請安裝 CMake 建置系統。

2. 尋找[最新版本](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK 的標籤名稱。

3. 開啟命令提示字元或 Git Bash 殼層。 執行下列命令以複製最新版的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫。 使用您在上一個步驟中找到的標籤作為 `-b` 參數的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    預期此作業需要幾分鐘的時間才能完成。

4. 在 git 存放庫的根目錄中建立 `cmake` 子目錄，並瀏覽至該資料夾。 從 `azure-iot-sdk-c` 目錄執行下列命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 執行下列命令以建置開發用戶端平台專屬的 SDK 版本。 `cmake` 目錄中會產生模擬裝置的 Visual Studio 解決方案。

    ```cmd
    cmake ..
    ```

    如果 `cmake` 找不到 C++ 編譯器，您在執行上述命令時，可能會收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)中執行此命令。 

    建置成功後，最後幾行輸出會類似於下列輸出：

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本節中，您將使用 Azure Cloud Shell 搭配 [IoT 擴充功能](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)來註冊模擬裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyCDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyCDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得「裝置連接字串」  ：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後將會在快速入門中使用此值。

## <a name="send-simulated-telemetry"></a>傳送模擬的遙測

模擬裝置應用程式會連線到您 IoT 中樞上的裝置特定端點，並且以模擬遙測形式傳送字串。

1. 使用文字編輯器開啟 iothub_convenience_sample.c 原始程式檔並檢閱用於傳送遙測的範例程式碼。 此檔案位於 Azure IoT C SDK 複製所在工作目錄底下的下列位置：

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. 尋找 `connectionString` 常數的宣告：

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    使用稍早所記錄的裝置連接字串來取代 `connectionString` 常數的值。 接著，將您的變更儲存到 **iothub_convenience_sample.c**。

3. 在本機終端機視窗中，瀏覽到您在 Azure IoT SDK 中建立之 CMake 目錄中的 *iothub_convenience_sample* 專案目錄。 從工作目錄輸入下列命令：

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. 在本機終端機視窗中執行 CMake，以使用已更新的 `connectionString` 值建置範例：

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. 在本機終端機視窗中，執行下列命令以執行模擬裝置應用程式：

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

在本節中，您將使用 Azure Cloud Shell 搭配 [IoT 擴充功能](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)來監視由模擬裝置傳送的裝置訊息。

1. 使用 Azure Cloud Shell，執行下列命令以連線到您的 IoT 中樞並讀取訊息：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![使用 Azure CLI 來閱讀裝置訊息](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已設定 IoT 中樞、註冊裝置、使用 C 應用程式將模擬遙測資料傳送至中樞，並使用 Azure Cloud Shell 從中樞讀取遙測資料。

若要深入了解如何搭配 Azure IoT Hub C SDK 進行開發，請繼續閱讀下列操作指南：

> [!div class="nextstepaction"]
> [使用 Azure IoT 中樞 C SDK 進行開發](iot-hub-devguide-develop-for-constrained-devices.md)
