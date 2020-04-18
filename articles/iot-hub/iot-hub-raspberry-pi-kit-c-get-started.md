---
title: 使用 C 將 Raspberry Pi 連線到 Azure IoT 中樞 | Microsoft Docs
description: 了解如何設定及連線 Raspberry Pi 至 Azure IoT 中樞，讓 Raspberry Pi 將資料傳送到 Azure 雲端平台
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 03f9d58cab725335b0f4090ac1a7289c32c0af7f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640553"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>將 Raspberry Pi 連接至 Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教學課程中，您會開始了解執行 Raspbian 的 Raspberry Pi 在使用方面的基本知識。 接著會了解如何使用 [Azure IoT 中樞](about-iot-hub.md)讓您的裝置順暢地與雲端連線。 如需 Windows 10 IoT 核心範例，請移至 [Windows 開發人員中心](https://www.windowsondevices.com/)。

還沒有套件嗎？ 試用 [Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)。 或在[這裡](https://azure.microsoft.com/develop/iot/starter-kits)購買新的套件。

## <a name="what-you-do"></a>您要做什麼

* 建立 IoT 中樞。

* 在 IoT 中樞對於 Pi 註冊裝置。

* 設定 Raspberry Pi。

* 在 Pi 上執行範例應用程式，將感應器資料傳送至 IoT 中樞。

將 Raspberry Pi 連接至您建立的 IoT 中樞。 然後，在 Pi 上執行範例應用程式，以收集 BME280 感應器中的溫度和溼度資料。 最後，將感應器資料傳送至 IoT 中樞。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 Azure IoT 中樞，並取得新的裝置連接字串。

* 如何連接 Pi 與 BME280 感應器。

* 如何在 Pi 上執行範例應用程式來收集感應器資料。

* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

![您需要什麼](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2 或 Raspberry Pi 3 電路板。

* 有效的 Azure 訂用帳戶。 如果沒有 Azure 帳戶,只需幾分鐘即可[創建免費的 Azure 試用帳戶](https://azure.microsoft.com/free/)。

* 連接至 Pi 的監視器、 USB 鍵盤和滑鼠。

* 執行 Windows 或 Linux 的 Mac 或 PC。

* 網際網路連線。

* 16 GB 以上的 microSD 記憶卡。

* 一個 USB-SD 配接器或 microSD 記憶卡，以將作業系統映像燒錄到 microSD 記憶卡中。

* 具備 6 英呎 micro USB 纜線的 5V 2A 電源供應器。

下列項目是選用項目︰

* 組裝的 Adafruit BME280 溫度、壓力溼度感應器。

* 麵包板。

* 6 條 F/M 跳線。

* 1 顆漫射型 10 mm LED。

> [!NOTE]
> 這些專案是可選的,因為代碼示例支援模擬感測器數據。
>

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>設定 Raspberry Pi

現在成立了樹莓派。

### <a name="install-the-raspbian-operating-system-for-pi"></a>安裝 Pi 的 Raspbian 作業系統

準備好用來安裝 Raspbian 映像的 microSD 記憶卡。

1. 下載 Raspbian。

   1. [下載使用桌面](https://www.raspberrypi.org/downloads/raspbian/)(.zip 檔案)的 Raspbian 拉伸。

   2. 將 Raspbian 映像解壓縮到您電腦上的資料夾。

2. 將 Raspbian 安裝到 microSD 記憶卡。

   1. [下載並安裝蝕刻SD卡燒錄機實用程式](https://etcher.io/)。

   2. 執行 Etcher 並選取您在步驟 1 中解壓縮的 Raspbian 映像。

   3. 選取 microSD 記憶卡磁碟機。 注意：Etcher 可能已經選取正確的磁碟機。

   4. 按一下 [Flash] 以將 Raspbian 安裝到 microSD 記憶卡。

   5. 安裝完成時，請將 microSD 記憶卡從電腦移除。 您可以放心地直接移除 microSD 記憶卡，因為 Etcher 會在完成時自動退出或卸載 microSD 記憶卡。

   6. 將 microSD 記憶卡插入 Pi。

### <a name="enable-ssh-and-spi"></a>啟用 SSH 和 SPI

1. 將 Pi 連接到監視器、鍵盤和滑鼠,啟動 Pi,然後`pi`使用`raspberry`使用者名和 密碼登錄到 Raspbian。
 
2. 點擊樹莓圖示>**偏好** > **樹莓派設定**。

   ![[Raspbian 偏好設定] 功能表](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. 在 [介面]**** 索引標籤上，將 [SPI]**** 和 [SSH]**** 設定為 [啟用]****，然後按一下 [確定]****。 如果您沒有實體感應器，而且想要使用模擬的感應器資料，這便是選擇性步驟。

   ![在 Raspberry Pi 上啟用 SPI 和 SSH](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> 若要啟用 SSH 和 SPI，您可以在 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 和[RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) 找到更多參考文件。
>

### <a name="connect-the-sensor-to-pi"></a>將感應器連接至 Pi

使用麵包板和跳線將 LED 和 BME280 連接至 Pi，如下所示。 如果沒有感應器,[請跳過此部份](#connect-pi-to-the-network)。

![Raspberry Pi 和感應器連接](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

BME280 感應器可以收集溫度和溼度資料。 而如果裝置與雲端之間有通訊，LED 將會閃爍。

針對感應器針腳，請使用下列接線方式：

| 啟動 (感應器和 LED)     | 結束 (電路版)            | 纜線顏色   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (針腳 5G)         | GPIO 4 (針腳 7)         | 白色纜線   |
| LED GND (針腳 6G)         | GND (針腳 6)            | 黑色纜線   |
| VDD (針腳 18F)            | 3.3V PWR (針腳 17)      | 白色纜線   |
| GND (針腳 20F)            | GND (針腳 20)           | 黑色纜線   |
| SCK (針腳 21F)            | SPI0 SCLK (針腳 23)     | 橘色纜線  |
| SDO (針腳 22F)            | SPI0 MISO (針腳 21)     | 黃色纜線  |
| SDI (針腳 23F)            | SPI0 MOSI (針腳 19)     | 綠色纜線   |
| CS (針腳 24F)             | SPI0 CS (針腳 24)       | 藍色纜線    |

按一下以檢視 [Raspberry Pi 2 和 3 針腳對應](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)進行參考。

將 BME280 成功連接至 Raspberry Pi 之後，應該如下圖所示。

![連接的 Pi 和 BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>將 Pi 連線到網路

透過 micro USB 纜線和電源供應器來開啟 Pi。 使用乙太網路纜線將 Pi 連接到有線網路，或遵循來自 Raspberry Pi Foundation 的[指示](https://www.raspberrypi.org/documentation/configuration/wireless/)，將 Pi 連接到無線網路。 在 Pi 成功連線到網路之後，您需要記下 [Pi 的 IP 位址](https://www.raspberrypi.org/documentation/remote-access/ip-address.md)。

![已連接到有線網路](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>在 Pi 上執行範例應用程式

### <a name="sign-into-your-raspberry-pi"></a>登入您的樹莓派

1. 使用下列其中一個 SSH 用戶端，從主機電腦連接到 Raspberry Pi。
   
   **Windows 使用者**
   1. 下載並安裝適用於 Windows 的 [PuTTY](https://www.putty.org/)。 
   1. 將 Pi 的 IP 位址複製到 [主機名稱] 或 [IP 位址] 區段，並且選取 SSH 作為連線類型。
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac 和 Ubuntu 使用者**

   在 Ubuntu 或 macOS 上使用內建的 SSH 用戶端。 您可能需要執行 `ssh pi@<ip address of pi>`，才能透過 SSH 來連線 Pi。
   > [!NOTE]
   > 預設使用者名稱為 `pi`，密碼為 `raspberry`。


### <a name="configure-the-sample-application"></a>設定範例應用程式

1. 執行下列命令，複製範例應用程式：

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. 執行安裝程式指令碼：

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > 如果您**沒有實體 BME280**，則可以使用 '--simulated-data' 做為命令列參數，以模擬溫度和溼度資料。 `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>建置並執行範例應用程式

1. 執行下列命令，建置範例應用程式：

   ```bash
   cmake . && make
   ```
   
   ![建置輸出](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. 執行下列命令，執行範例應用程式：

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > 確定複製裝置連接字串，並貼到單引號中。
   >

您應該會看見下列輸出，顯示傳送至 IoT 中樞的感應器資料和訊息。

![輸出 - 從 Raspberry Pi 傳送至 IoT 中樞的感應器資料](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>閱讀中心收到的消息

監視 IoT 中心從設備接收的消息的一種方法是使用 Azure IoT 工具進行可視化工作室代碼。 要瞭解更多資訊,請參閱[使用 Azure IoT 工具進行視覺化工作室碼,在裝置和 IoT 中心之間傳送和接收訊息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

有關處理設備發送數據的更多方法,請繼續執行下一節。

## <a name="next-steps"></a>後續步驟

您已執行範例應用程式收集感應器資料並傳送至 IoT 中樞。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
