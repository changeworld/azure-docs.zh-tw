---
title: 將 IoT DevKit AZ3166 連線到 Azure IoT 中樞
description: 在本教學課程中了解如何設定 IoT DevKit AZ3166 並將其連線到 Azure IoT 中樞，以便它可以將資料傳送到 Azure 雲端平台。
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: 0d3afc8d24b95f170bd22b3dc2cf29e7f97e77c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839549"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>將 IoT DevKit AZ3166 連線至 Azure IoT 中樞

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

您可以使用 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 來開發及設定運用 Microsoft Azure 服務的物聯網 (IoT) 解決方案原型。 其包含具有豐富周邊與感應器的 Arduino 相容電路板、開放原始碼電路板套件，以及豐富的[範例庫](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 IoT 中樞並註冊 MXChip IoT DevKit 適用的裝置。
* 如何將 IoT DevKit 連線到 Wi-Fi 並設定 IoT 中樞連接字串。
* 如何將 DevKit 感應器遙測資料傳送至 IoT 中樞。
* 如何為 IoT DevKit 準備開發環境並開發應用程式。

還沒有 DevKit 嗎？ 請試用 [DevKit 模擬器](https://azure-samples.github.io/iot-devkit-web-simulator/)或[購買 DevKit](https://aka.ms/iot-devkit-purchase)。

您可從[程式碼範例庫](https://docs.microsoft.com/samples/browse/?term=mxchip)尋找所有 DevKit 教學課程的原始程式碼。

## <a name="what-you-need"></a>您需要什麼

* 具有 Micro-USB 傳輸線的 MXChip IoT DevKit 電路板。 [立即買一個](https://aka.ms/iot-devkit-purchase)。
* 執行 Windows 10、macOS 10.10+ 或 Ubuntu 18.04+ 的電腦。
* 有效的 Azure 訂用帳戶。 [啟動 30 天免費試用 Microsoft Azure 帳戶](https://azureinfo.microsoft.com/us-freetrial.html)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>準備硬體

將下列硬體連接到您的電腦：

* DevKit 面板
* Micro-USB 傳輸線

![必要的硬體](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

若要將 DevKit 連線至電腦，請執行下列步驟：

1. 將 USB 端連線到電腦。

2. 將 Micro-USB 端連線到 DevKit。

3. 綠色的電源 LED 燈可確認連線成功。

   ![硬體連線](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>快速入門：將遙測資料從 DevKit 傳送至 IoT 中樞

本快速入門會使用預先編譯的 DevKit 韌體，將遙測傳送至 IoT 中樞。 執行之前，您需要建立一個 IoT 中樞，並向中樞註冊一部裝置。

### <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyNodeDevice**：您所註冊的裝置名稱。 使用所示的 **MyNodeDevice**。 如果您為裝置選擇不同的名稱，則必須在本文中使用該名稱，並先在應用程式範例中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > 如果在執行 `device-identity` 時發生錯誤，請安裝[適用於 Azure CLI 的 Azure IoT 延伸模組](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) (英文)。
   > 執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 延伸模組會將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務 (DPS) 的特定命令新增至 Azure CLI。
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

### <a name="send-devkit-telemetry"></a>傳送 DevKit 遙測

DevKit 會連線到 IoT 中樞上的裝置特定端點，並傳送溫度和溼度遙測。

1. 下載 IoT DevKit 的最新版 [GetStarted 韌體](https://aka.ms/devkit/prod/getstarted/latest)。

1. 確定 IoT DevKit 透過 USB 連線到電腦。 開啟檔案總管，其中有一個稱為 **AZ3166** 的大量儲存裝置。

    ![開啟 Windows 檔案總管](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. 將剛下載的韌體拖放到大量儲存裝置中，裝置會隨即自動閃爍。

    ![複製韌體](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. 在 DevKit 上，按住按鈕 **B**，按下再放開 [重設] 按鈕，然後放開按鈕 **B**。DevKit 會進入 AP 模式。 若要確認，螢幕上會顯示 DevKit 的服務組識別碼 (SSID) 及設定入口網站 IP 位址。

    ![Reset 按鈕、按鈕 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![設定 AP 模式](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 在已啟用 Wi-Fi 的不同裝置 (電腦或行動電話) 上，使用網頁瀏覽器來連線到上述步驟中所顯示的 IoT DevKit SSID。 如果要求輸入密碼，請將其保留空白。

    ![連線 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. 在瀏覽器中開啟 **192.168.0.1**。 選取想要讓 IoT DevKit 連線的 Wi-Fi，鍵入 Wi-Fi 密碼，然後貼上先前記下的裝置連接字串。 然後按一下 [儲存]。

    ![設定 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit 僅支援 2.4GHz 網路。 如需更多詳細資料，請參閱[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)。

1. 當看到結果頁面時，WiFi 資訊和裝置連接字串將會儲存到 IoT DevKit。

    ![設定結果](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > 設定好 Wi-Fi 後，即使將裝置的接線拔除，該裝置上仍會留有用於該連線的認證。

1. IoT DevKit 會在幾秒內重新啟動。 在 DevKit 螢幕上，您會看到 DevKit 的 IP 位址，後面接著遙測資料 (包括溫度和濕度值)，並將訊息計數傳送至 Azure IoT 中樞。

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![傳送資料](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. 若要確認遙測資料已傳送至 Azure，請在 Azure Cloud Shell 中執行下列命令：

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>準備開發環境

請遵循下列步驟以準備 DevKit 的開發環境：

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>安裝 Visual Studio Code 與 Azure IoT Tools 延伸模組套件

1. 安裝 [Arduino IDE](https://www.arduino.cc/en/Main/Software)。 它會提供必要的工具鏈，以便編譯和上傳 Arduino 程式碼。
    * **Windows**：使用 Windows Installer 版本。 請勿從 App Store 安裝。
    * **macOS**：將解壓縮的 **Arduino.app** 拖放到 `/Applications` 資料夾中。
    * **Ubuntu**：將它解壓縮到資料夾中，例如 `$HOME/Downloads/arduino-1.8.8`

2. 安裝 [Visual Studio Code](https://code.visualstudio.com/)，這是跨平台原始程式碼編輯器，其具有強大的 IntelliSense、程式碼完成和偵錯支援，以及可從市集安裝的豐富延伸模組。

3. 啟動 VS Code，在延伸模組市集中尋找 **Arduino** 並加以安裝。 此延伸模組提供在 Arduino 平台上進行開發的增強體驗。

    ![安裝 Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 在延伸模組市集中尋找 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 並加以安裝。

    ![顯示延伸模組 marketplace Azure IoT Tools 的螢幕擷取畫面。](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    或者，將此 URL 複製並貼到瀏覽器視窗中：`vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Azure IoT Tools 延伸模組套件包含 [Azure IoT Device Workbench](https://aka.ms/iot-workbench)，可用來在各種 IoT DevKit 裝置上進行開發和偵錯。 [Azure IoT 中樞延伸模組](https://aka.ms/iot-toolkit)也隨附於 Azure IoT Tools 延伸模組套件，可用來管理 Azure IoT 中樞並與其互動。

5. 為 VS Code 設定 Arduino 設定。

    在 Visual Studio Code 中，按一下 [檔案] > [喜好設定] > [設定] (在 macOS 上，則為 [程式碼] > [喜好設定] > [設定])。 然後按一下 [設定] 頁面右上角的**開啟設定 (JSON)** 圖示。

    ![安裝 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    依據您的平台，新增下列幾行來設定 Arduino： 

    * **Windows**：

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**：

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**：

        以您的使用者名稱取代下方的 **{username}** 預留位置。

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Arduino:Board Manager]。 搜尋 **AZ3166** 並安裝最新版本。

    ![安裝 DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>安裝 ST-Link 驅動程式

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) 是 IoT DevKit 用來與開發電腦進行通訊的 USB 介面。 您必須將其安裝在 Windows 上，才能對裝置閃爍顯示編譯的裝置程式碼。 遵循 OS 特有步驟，以允許電腦存取您的裝置。

* **Windows**：從 [STMicroelectronics 網站](https://www.st.com/en/development-tools/stsw-link009.html)下載並安裝 USB 驅動程式。
* **macOS**：macOS 不需要任何驅動程式。
* **Ubuntu**：在終端機中執行命令並登出後再登入，以讓群組變更生效：

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

您現在已可準備及設定您的開發環境。 讓我們建立您剛執行的 GetStarted 範例。

## <a name="build-your-first-project"></a>建立您的第一個專案

### <a name="open-sample-code-from-sample-gallery"></a>從範例庫開啟範例程式碼

IoT DevKit 包含豐富的範例庫，可用來了解如何將 DevKit 連線到各種 Azure 服務。

1. 確定 IoT DevKit **未**連線至您的電腦。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。

1. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Azure IoT Device Workbench：**開啟範例...]** 。然後選取 [IoT DevKit] 作為面板。

1. 在 [IoT Workbench 範例] 頁面上，尋找 [開始使用]，然後按一下 [開啟範例]。 然後選取預設路徑來下載範例程式碼。

    ![開啟範例](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>佈建 Azure IoT 中樞和裝置

不用從 Azure 入口網站佈建 Azure IoT 中樞和裝置，即可在 VS Code 中執行這項操作，而無需離開開發環境。

1. 在新開啟的專案視窗中，按一下 `F1` 以開啟命令選擇區，鍵入並選取 [Azure IoT Device Workbench:**佈建 Azure 服務...]** 。遵循逐步指南，完成您的 Azure IoT 中樞佈建並建立 IoT 中樞裝置。

    ![佈建命令](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > 如果尚未登入 Azure。 請遵循快顯通知進行登入。

1. 選取您要使用的訂用帳戶。

    ![選取訂用帳戶](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 然後選取或建立新的[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)。

    ![選取資源群組](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 在指定的資源群組中，遵循指南以選取或建立新的 Azure IoT 中樞。

    ![選取 IoT 中樞步驟](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![選取 IoT 中樞](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![已選取 IoT 中樞](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 在輸出視窗中，您會看到已佈建的 Azure IoT 中樞。

    ![已佈建的 IoT 中樞](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 在已佈建的 Azure IoT 中樞中選取或建立新裝置。

    ![選取 IoT 裝置步驟](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![選取已佈建的 IoT 裝置](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 現在已佈建 Azure IoT 中樞並在其中建立裝置。 此外，裝置連接字串也會儲存在 VS Code 中，以供稍後用來設定 IoT DevKit。

    ![佈建完成](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>設定及編譯裝置程式碼

1. 在右下方的狀態列中，檢查 **MXCHIP AZ3166** 是否顯示為已選取的面板，且使用 **STMicroelectronics** 作為序列埠。

    ![選取主機板和 COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Azure IoT Device Workbench：Configure Device Settings...] \(Azure IoT Device Workbench：設定裝置設定...\)，然後選取 [Config Device Connection String] \(設定裝置連接字串\) > [Select IoT Hub Device Connection String] \(選取 IoT 中樞裝置連接字串\)。

1. 在 DevKit 上，按住**按鈕 A**，按下再放開 [重設] 按鈕，然後放開**按鈕 A**。您的 DevKit 會進入設定模式並儲存連接字串。

    ![連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. 再按一次 `F1`，然後輸入並選取 [Azure IoT Device Workbench：**上傳裝置程式碼]** 。 此時會開始編譯程式碼，並上傳到 DevKit。

    ![Arduino 上傳](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit 會重新開機，然後開始執行程式碼。

> [!NOTE]
> 如果發生任何錯誤或中斷，您一律可藉由再次執行命令來復原。

## <a name="test-the-project"></a>測試專案

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>檢視要傳送至 Azure IoT 中樞的遙測資料

按一下狀態列上的電源插頭圖示，以開啟「序列監視器」：

![序列監視器](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

當您看到以下結果時，表示範例應用程式已成功執行：

* 「序列監視器」會顯示傳送至 IoT 中樞的訊息。
* MXChip IoT DevKit 上的 LED 燈在閃爍。

![序列監視器輸出](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> 您可能會在測試期間發生 LED 燈未閃爍的錯誤，Azure 入口網站不會顯示來自裝置的傳入資料，但裝置 OLED 螢幕會顯示為 **Running...** 。若要解決此問題，請在 Azure 入口網站中，移至 IoT 中樞內的裝置，並將訊息傳送至裝置。 如果在 VS Code 的序列監視器中看到下列回應，可能是來自裝置的直接通訊在路由器層級遭到封鎖。 請檢查為了連線裝置所設定的防火牆和路由器規則。 此外，請確定輸出連接埠 1833 已開啟。
> 
> ERROR: mqtt_client.c (ln 454):Error: failure opening connection to endpoint  
> INFO: >>>Connection status: disconnected  
> ERROR: tlsio_mbedtls.c (ln 604):Underlying IO open failed  
> ERROR: mqtt_client.c (ln 1042):Error: io_open failed  
> ERROR: iothubtransport_mqtt_common.c (ln 2283): failure connecting to address atcsliothub.azure-devices.net.  
> INFO: >>>Re-connect.  
> INFO:IoThub Version:1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>檢視 Azure IoT 中樞所接收的遙測資料

您可以使用 [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 來監視 IoT 中樞的裝置到雲端 (D2C) 訊息。

1. 登入 [Azure 入口網站](https://portal.azure.com/)，尋找您所建立的 IoT 中樞。

    ![Azure 入口網站](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. 在 [共用存取原則] 窗格中，按一下 [iothubowner] 原則，然後記下您 IoT 中樞的連接字串。

    ![Azure IoT 中樞連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. 在 VS Code 中，按一下 `F1`，鍵入並選取 [Azure IoT 中樞：設定 IoT 中樞連接字串]。 將連接字串複製到其中。

    ![設定 Azure IoT 中樞連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 展開左側 [AZURE IOT 中樞裝置] 窗格，以滑鼠右鍵按一下所建立的裝置名稱，然後選取 [開始監視內建事件端點]。

    ![監視 D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. 在 [輸出] 窗格中，您可以看到 IoT 中樞的連入 D2C 訊息。

    ![顯示 IoT 中樞傳入 D2C 訊息的螢幕擷取畫面。](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>檢閱程式碼

`GetStarted.ino` 是主要的 Arduino Sketch 檔案。

![D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

若要查看裝置遙測如何傳送至 Azure IoT 中樞，請開啟相同資料夾中的 `utility.cpp` 檔案。 檢視 [API 參考](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/)以了解如何在 IoT DevKit 上使用感應器和周邊。

所使用的 `DevKitMQTTClient` 是[適用於 C 的 Microsoft Azure IoT SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)中用來與 Azure IoT 中樞互動的 **iothub_client** 包裝函式。

## <a name="problems-and-feedback"></a>問題與意見反應

如果發生問題，您可以查看 [IoT DevKit 常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)中的解決方案，或從 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) 連絡我們。 您也可以在這個頁面留下評論，以對我們提供意見反應。

## <a name="next-steps"></a>後續步驟

您已成功將 MXChip IoT DevKit 連線到 IoT 中樞，並將擷取到的感應器資料傳送至 IoT 中樞。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
