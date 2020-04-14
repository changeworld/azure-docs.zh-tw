---
title: 將 IoT 開發基 AZ3166 連線到 Azure IoT 中心
description: 在本教學課程中了解如何設定 IoT DevKit AZ3166 並將其連線到 Azure IoT 中樞，以便它可以將資料傳送到 Azure 雲端平台。
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 631a20c7bf73aa2af363fdc0019ef24cccc58f9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258587"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>將 IoT DevKit AZ3166 連線至 Azure IoT 中樞

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

您可以使用 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 來開發及設定運用 Microsoft Azure 服務的物聯網 (IoT) 解決方案原型。 它包括一個與Arduino相容的板,具有豐富的外圍設備和感測器,開源板包,和豐富的[樣品庫](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 IoT 中樞並註冊 MXChip IoT DevKit 適用的裝置。
* 如何將 IoT DevKit 連接到 Wi-Fi 並配置 IoT 中心連接字串。
* 如何將 DevKit 感測器遙測數據發送到 IoT 中心。
* 如何為 IoT 開發 Kit 準備開發環境和開發應用程式。

還沒有 DevKit 嗎？ 請試用 [DevKit 模擬器](https://azure-samples.github.io/iot-devkit-web-simulator/)或[購買 DevKit](https://aka.ms/iot-devkit-purchase)。

你可以從[代碼示例庫中](https://docs.microsoft.com/samples/browse/?term=mxchip)找到所有 DevKit 教程的原始程式碼。

## <a name="what-you-need"></a>您需要什麼

* 帶有微型 USB 電纜的 MXChip IoT 開發套件板。 [立即買一個](https://aka.ms/iot-devkit-purchase)。
* 運行 Windows 10、macOS 10.10+ 或 Ubuntu 18.04+ 的電腦。
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>快速入門:將遙測資料從 DevKit 傳送到 IoT 中心

快速入門使用預先編譯的 DevKit 韌體將遙測發送到 IoT 中心。 在運行它之前,您將創建一個IoT中心,並將設備註冊到集線器。

### <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyNodeDevice:** 您正在註冊的設備的名稱。 如圖所示,請使用**MyNodeDevice。** 如果您為裝置選擇不同的名稱，則必須在本文中使用該名稱，並先在應用程式範例中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > 如果執行`device-identity`錯誤,請安裝 Azure [CLI 的 Azure IoT 擴展。](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)
   > 執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 延伸將特定於 IoT 中心、IoT 邊緣和 IoT 設備配置服務 (DPS) 的命令添加到 Azure CLI。
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. 在 Azure 雲外殼中執行以下指令,以取得剛剛註冊_的裝置的裝置連接字串_:

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

### <a name="send-devkit-telemetry"></a>傳送 DevKit 遙測

DevKit 連接到IoT中心上的特定於設備的終結點,並發送溫度和濕度遙測數據。

1. 下載 IoT DevKit 的最新版本的[Get 開始韌體](https://aka.ms/devkit/prod/getstarted/latest)。

1. 確保 IoT DevKit 透過 USB 連接到您的電腦。 打開檔案資源管理員有一個USB大容量存放裝置稱為**AZ3166。**

    ![開啟 [Windows 檔案總管]](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. 拖放剛剛下載到大容量存放裝置的韌體,它會自動閃爍。

    ![複製韌體](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. 在 DevKit 上,按住按鈕**B**,按下並釋放 **「重置」** 按鈕,然後釋放按鈕**B**。您的 DevKit 進入 AP 模式。 要確認,螢幕將顯示 DevKit 的服務集識別碼 (SSID) 和設定門戶 IP 位址。

    ![Reset 按鈕、按鈕 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![設定 AP 模式](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 使用其他啟用 Wi-Fi 的裝置(電腦或行動電話)上的 Web 瀏覽器連接到上一步驟中顯示的 IoT DevKit SSID。 如果它要求輸入密碼,請將其留空。

    ![連線 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. 在瀏覽器中打開**192.168.0.1。** 選擇您希望 IoT DevKit 連接到的 Wi-Fi,鍵入 Wi-Fi 密碼,然後粘貼您之前注意到的設備連接字串。 然後按一下 [儲存]。

    ![設定 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit 僅支援 2.4GHz 網路。 如需更多詳細資料，請參閱[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)。

1. 當您看到結果頁面時,WiFi 資訊和設備連接字串將存儲在 IoT DevKit 中。

    ![設定結果](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > 設定好 Wi-Fi 後，即使將裝置的接線拔除，該裝置上仍會留有用於該連線的認證。

1. IoT DevKit 將在幾秒鐘內重新啟動。 在 DevKit 螢幕上,您將看到 DevKit 的 IP 位址遵循遙測數據(包括溫度和濕度值,並將消息計數發送到 Azure IoT 中心。

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![傳送資料](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. 要驗證傳送到 Azure 的遙測資料,請在 Azure 雲命令中執行以下指令:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>準備開發環境

依以下步驟為 DevKit 準備開發環境:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>使用 Azure IoT 工具延伸套件安裝視覺化工作室碼

1. 安裝 [Arduino IDE](https://www.arduino.cc/en/Main/Software)。 它會提供必要的工具鏈，以便編譯和上傳 Arduino 程式碼。
    * **Windows**：使用 Windows Installer 版本。 不要從應用商店安裝。
    * **macOS**：將解壓縮的 **Arduino.app** 拖放到 `/Applications` 資料夾中。
    * **Ubuntu**：將它解壓縮到 `$HOME/Downloads/arduino-1.8.8` 之類的資料夾中

2. 安裝[Visual Studio 代碼](https://code.visualstudio.com/),一個跨平臺原始程式碼編輯器,具有強大的智慧感知、代碼完成和調試支援以及豐富的擴展,可以從市場安裝。

3. 啟動 VS Code，在延伸模組市集中尋找 **Arduino** 並加以安裝。 此延伸模組提供在 Arduino 平台上進行開發的增強體驗。

    ![安裝 Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 在延伸模組市集中尋找 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 並加以安裝。

    ![安裝 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    或使用此直接連結:
    > [!div class="nextstepaction"]
    > [安裝 Azure IoT 工具延伸套件](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Azure IoT 工具擴展包包含[Azure IoT 設備工作台](https://aka.ms/iot-workbench),用於在各種 IoT 開發基設備上開發和調試。 [Azure IoT 中心擴展](https://aka.ms/iot-toolkit)包也包含在 Azure IoT 工具擴展套件中,用於管理和與 Azure IoT 中心進行交互。

5. 為 VS Code 設定 Arduino 設定。

    在可視化工作室代碼中,按下 **「檔>首選項>設置**(在 macOS 上,**代碼>首選項>設置**)。 然後按下 *「設定」* 頁面右上角的 **「打開設定 (JSON)」** 圖示。

    ![安裝 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    依據您的平台，新增下列幾行來設定 Arduino： 

    * **視窗**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **烏本圖**:

        以您的使用者名稱取代下方的 **{username}** 預留位置。

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. 按一下 `F1` 開啟命令選擇區，輸入並選取 [Arduino: Board Manager]****。 搜尋 **AZ3166** 並安裝最新版本。

    ![安裝 DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>安裝 ST-Link 驅動程式

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) 是 IoT DevKit 用來與開發電腦進行通訊的 USB 介面。 您需要將其安裝在 Windows 上,才能將已編譯的設備代碼快閃記憶體到 DevKit。 遵循 OS 特有步驟，以允許電腦存取您的裝置。

* **Windows**：從 [STMicroelectronics 網站](https://www.st.com/en/development-tools/stsw-link009.html)下載並安裝 USB 驅動程式。
* **macOS**：macOS 不需要驅動程式。
* **Ubuntu**: 在終端中執行命令並登出並登入,以便群組更改生效:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

您現在已可準備及設定您的開發環境。 讓我們構建您剛剛運行的 Get 開始範例。

## <a name="build-your-first-project"></a>建立您的第一個專案

### <a name="open-sample-code-from-sample-gallery"></a>從範例庫中開啟範例碼

IoT DevKit 包含豐富的範例庫,可用於瞭解將 DevKit 連接到各種 Azure 服務。

1. 確定 IoT DevKit **未**連線至您的電腦。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。

1. 按下`F1`以打開命令調色板,鍵入並選擇**Azure IoT 設備工作台:打開範例..."** 然後選擇**IoT 開發人員工具組**作為板。

1. 在 [IoT Workbench 範例] 頁面上，尋找 [開始使用]****，然後按一下 [開啟範例]****。 然後選取預設路徑來下載範例程式碼。

    ![開啟範例](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>預配 Azure IoT 中心和裝置

無需從 Azure 門戶預配 Azure IoT 中心和設備,而是在 VS 代碼中執行此操作,而無需離開開發環境。

1. 在新的開啟的項目視窗中,按下以`F1`開啟命令調色板,鍵入並選擇 Azure **IoT 裝置工作台:預配 Azure 服務...** 按照分步指南完成 Azure IoT 中心預配和創建 IoT 中心設備。

    ![預先接命令](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > 如果您尚未在 Azure 中簽名。 按照彈出窗口進行登錄。

1. 選取您要使用的訂用帳戶。

    ![選擇子](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 選擇或建立新[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)。

    ![選取資源群組](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 在指定的資源組中,按照指南選擇或創建新的 Azure IoT 中心。

    ![選擇 IoT 中心步驟](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![選擇 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![選取的 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 在輸出視窗中,您將看到 Azure IoT 中心預配。

    ![IoT 中心設定](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 在預配的 Azure IoT 中心中選擇或創建新設備。

    ![選擇 IoT 裝置步驟](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![選擇已預先配的 IoT 裝置](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 現在,您已經預配了 Azure IoT 中心並在其中創建了設備。 此外,設備連接字串將儲存在 VS 代碼中,以便以後設定 IoT DevKit。

    ![已完成準備金](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>設定及編譯裝置碼

1. 在右下方的狀態列中，檢查 **MXCHIP AZ3166** 是否顯示為已選取的面板，且使用 **STMicroelectronics** 作為序列埠。

    ![選取面板和 COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. 按下`F1`以開啟指令調色板,鍵入並選擇**Azure IoT 裝置工作台:設定裝置設定...,** 然後選擇 **「設定裝置連接字串>選擇 IoT 中心裝置連接字串**。

1. 在 DevKit 上,按住**按鍵 A**,按下並釋放**重置**按鈕,然後釋放**按鈕 A**。您的 DevKit 進入配置模式並儲存連接字串。

    ![連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. 再次`F1`按下,鍵入並選擇**Azure IoT 裝置工作台:上傳裝置代碼**。 此時會開始編譯程式碼，並上傳到 DevKit。

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
> 在測試期間,您可能會遇到 LED 指示燈未閃爍、Azure 門戶不顯示來自設備的傳入數據的錯誤,但設備 OLED 螢幕將顯示為 **"正在運行..."** 要解決此問題,在 Azure 門戶中,轉到 IoT 中心中的設備並向設備發送消息。 如果在 VS Code 中的串列監視器中看到以下回應,則可能是在路由器級別阻止來自設備的直接通信。 檢查為連接設備配置的防火牆和路由器規則。 此外,請確保出站埠 1833 處於打開狀態。
> 
> 錯誤: mqtt_client.c (ln 454): 錯誤: 開啟到終結點的連線失敗  
> 資訊: >>>连接状态:已斷開連接  
> 錯誤: tlsio_mbedtls.c (ln 604): 基礎 IO 開啟失敗  
> 錯誤: mqtt_client.c (ln 1042): 錯誤: io_open失敗  
> 錯誤: iothubtransport_mqtt_common.c (ln 2283): 無法連接到位址atcsliothub.azure-devices.net。  
> 資訊: >>>重新连接。  
> 資訊: IoThub 版本: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>檢視 Azure IoT 中樞所接收的遙測資料

您可以使用 [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 來監視 IoT 中樞的裝置到雲端 (D2C) 訊息。

1. 登入 [Azure 入口網站](https://portal.azure.com/)，尋找您所建立的 IoT 中樞。

    ![Azure 入口網站](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. 在 [共用存取原則]**** 窗格中，按一下 [iothubowner]**** 原則，然後記下您 IoT 中樞的連接字串。

    ![Azure IoT 中樞連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. 在 VS`F1`代碼中,按下、鍵入並選擇**Azure IoT 中心:設定 IoT 中心連接字串**。 將連接字串複製到其中。

    ![設定 Azure IoT 中樞連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 展開右側的**AZURE IOT HUB DEVICES**窗格,右鍵單擊您創建的設備名稱,然後選擇 **「開始監視內置事件終結點**」。

    ![監視 D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. 在 [輸出]**** 窗格中，您可以看到 IoT 中樞的連入 D2C 訊息。

    ![D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>檢閱程式碼

是`GetStarted.ino`主Arduino草圖檔。

![D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

要查看如何將設備遙測發送到 Azure IoT 中心,請參閱`utility.cpp`在同一 資料夾中打開該檔。 檢視[API 參考](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/),瞭解如何在 IoT DevKit 上使用感測器和外圍設備。

使用的`DevKitMQTTClient`是[Microsoft Azure IoT SDK 和庫](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)的**iothub_client**的包裝,供 C 與 Azure IoT 中心互動。

## <a name="problems-and-feedback"></a>問題與意見反應

如果發生問題，您可以查看 [IoT DevKit 常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)中的解決方案，或從 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) 連絡我們。 您也可以在這個頁面留下評論，以對我們提供意見反應。

## <a name="next-steps"></a>後續步驟

您已成功將 MXChip IoT DevKit 連線到 IoT 中樞，並將擷取到的感應器資料傳送至 IoT 中樞。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
