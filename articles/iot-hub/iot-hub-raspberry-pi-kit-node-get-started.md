---
title: 將 Raspberry Pi 連線至雲端中的 Azure IoT 中樞（node.js）
description: 瞭解如何在本教學課程中設定 Raspberry Pi，並將其連線至 Raspberry Pi 的 Azure IoT 中樞，以將資料傳送到 Azure 雲端平臺。
author: wesmc7777
manager: eliotgra
keywords: azure iot raspberry pi, raspberry pi iot 中樞, raspberry pi 將資料傳送至雲端, raspberry pi 至 cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.openlocfilehash: 3175956e35603cc4ad3a938f3d316c0af8f2d227
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81640522"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>將 Raspberry Pi 連接至 Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教學課程中，您會開始了解執行 Raspbian 的 Raspberry Pi 在使用方面的基本知識。 接著會了解如何使用 [Azure IoT 中樞](about-iot-hub.md)讓您的裝置順暢地與雲端連線。 如需 Windows 10 IoT 核心範例，請移至 [Windows 開發人員中心](https://www.windowsondevices.com/)。

還沒有套件嗎？ 試用 [Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)。 或在[這裡](https://azure.microsoft.com/develop/iot/starter-kits)購買新的套件。

## <a name="what-you-do"></a>您要做什麼

* 建立 IoT 中樞。

* 在 IoT 中樞對於 Pi 註冊裝置。

* 設定 Raspberry Pi。

* 在 Pi 上執行範例應用程式，將感應器資料傳送至 IoT 中樞。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 Azure IoT 中樞，並取得新的裝置連接字串。

* 如何連接 Pi 與 BME280 感應器。

* 如何在 Pi 上執行範例應用程式來收集感應器資料。

* 如何將感應器資料傳送至 IoT 中樞。

## <a name="what-you-need"></a>您需要什麼

![您需要什麼](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Raspberry Pi 2 或 Raspberry Pi 3 主機板。

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 連接至 Pi 的監視器、USB 鍵盤及滑鼠。

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
> 如果您沒有選用項目，則可以使用模擬的感應器資料。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>設定 Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>安裝 Pi 的 Raspbian 作業系統

準備好用來安裝 Raspbian 映像的 microSD 記憶卡。

1. 下載 Raspbian。

   a. [使用桌面的 Raspbian Buster](https://www.raspberrypi.org/downloads/raspbian/) （.zip 檔案）。

   b. 將 Raspbian 映像解壓縮到您電腦上的資料夾。

2. 將 Raspbian 安裝到 microSD 記憶卡。

   a. [下載並安裝 ETCHER SD 記憶卡燒錄器公用程式](https://etcher.io/)。

   b. 執行 Etcher 並選取您在步驟 1 中解壓縮的 Raspbian 映像。

   c. 選取 microSD 記憶卡磁碟機。 Etcher 可能已經選取正確的磁碟機。

   d. 按一下 [Flash] 以將 Raspbian 安裝到 microSD 記憶卡。

   e. 安裝完成時，請將 microSD 記憶卡從電腦移除。 您可以放心地直接移除 microSD 記憶卡，因為 Etcher 會在完成時自動退出或卸載 microSD 記憶卡。

   f. 將 microSD 記憶卡插入 Pi。

### <a name="enable-ssh-and-i2c"></a>啟用 SSH 和 I2C

1. 將 Pi 連接至監視器、鍵盤及滑鼠。

2. 啟動 Pi，然後使用`pi`做為使用者名稱和`raspberry`密碼來登入 Raspbian。

3. 按一下 [Raspberry] 圖示 >**偏好** > 設定] [**Raspberry Pi**設定]。

   ![[Raspbian 偏好設定] 功能表](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. 在 [介面]**** 索引標籤上，將 [I2C]**** 和 [SSH]**** 設定為 [啟用]****，然後按一下 [確定]****。 如果您沒有實體感應器，而且想要使用模擬的感應器資料，這便是選擇性步驟。

   ![在 Raspberry Pi 上啟用 I2C 和 SSH](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> 若要啟用 I2C 和 SPI，您可以在 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 和[Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c) 找到更多參考文件。

### <a name="connect-the-sensor-to-pi"></a>將感應器連接至 Pi

使用麵包板和跳線將 LED 和 BME280 連接至 Pi，如下所示。 如果您沒有感應器，請[略過本節](#connect-pi-to-the-network)。

![Raspberry Pi 和感應器連接](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

BME280 感應器可以收集溫度和溼度資料。 當裝置將訊息傳送至雲端時，LED 會閃爍。

針對感應器針腳，請使用下列接線方式：

| 啟動 (感應器和 LED)     | 結束 (電路版)            | 纜線顏色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (針腳 5G)             | 3.3V PWR (針腳 1)       | 白色纜線   |
| GND (針腳 7G)             | GND (針腳 6)            | 棕色纜線   |
| SDI (針腳 10G)            | I2C1 SDA (針腳 3)       | 紅色纜線     |
| SCK (針腳 8G)             | I2C1 SCL (針腳 5)       | 橘色纜線  |
| LED VDD (針腳 18F)        | GPIO 24 (針腳 18)       | 白色纜線   |
| LED GND (針腳 17F)        | GND (針腳 20)           | 黑色纜線   |

按一下以查看[Raspberry Pi 2 & 3 pin](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi)對應以供您參考。

將 BME280 成功連接至 Raspberry Pi 之後，應該如下圖所示。

![連接的 Pi 和 BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>將 Pi 連線到網路

透過 micro USB 纜線和電源供應器來開啟 Pi。 使用乙太網路纜線將 Pi 連接到有線網路，或遵循來自 Raspberry Pi Foundation 的[指示](https://www.raspberrypi.org/documentation/configuration/wireless/)，將 Pi 連接到無線網路。 在 Pi 成功連線到網路之後，您需要記下 [Pi 的 IP 位址](https://www.raspberrypi.org/documentation/remote-access/ip-address.md)。

![已連接到有線網路](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> 請確定 Pi 是連接到與您電腦相同的網路。 例如，如果您的電腦連線到無線網路，而 Pi 連線到有線網路，您可能不會在 devdisco 輸出中看到 IP 位址。

## <a name="run-a-sample-application-on-pi"></a>在 Pi 上執行範例應用程式

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>複製範例應用程式並安裝必要條件套件

1. 使用下列其中一個 SSH 用戶端，從您的主機電腦連接到 Raspberry Pi：

   **Windows 使用者**

   a. 下載並安裝適用於 Windows 的 [PuTTY](https://www.putty.org/)。

   b. 將 Pi 的 IP 位址複製到 [主機名稱] 或 [IP 位址] 區段，並且選取 SSH 作為連線類型。

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac 和 Ubuntu 使用者**

   在 Ubuntu 或 macOS 上使用內建的 SSH 用戶端。 您可能需要執行 `ssh pi@<ip address of pi>`，才能透過 SSH 來連線 Pi。

   > [!NOTE]
   > 預設使用者名稱為 `pi`，密碼為 `raspberry`。

2. 將 Node.js 和 NPM 安裝到 Pi。

   請先檢查您的 Node.js 版本。

   ```bash
   node -v
   ```

   如果版本低於 10. x，或 Pi 上沒有 node.js，請安裝最新版本。

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. 複製範例應用程式。

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. 安裝該範例的所有套件。 此安裝包含 Azure IoT 裝置 SDK、BME280 感應器程式庫及 Wiring Pi 程式庫。

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >端視網路連線而定，可能需要幾分鐘才能完成此安裝程序。

### <a name="configure-the-sample-application"></a>設定範例應用程式

1. 執行下列命令以開啟組態檔：

   ```bash
   nano config.json
   ```

   ![組態檔](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   此檔案中有兩個您可以設定的項目。 第一個是 `interval`，這定義傳送至雲端之訊息間的時間間隔 (以毫秒為單位)。 第二個是 `simulatedData`，這是指出是否要使用模擬感應器資料的布林值。

   如果**沒有感應器**，請將 `simulatedData` 值設定為 `true`，使範例應用程式建立和使用模擬感應器資料。

   *注意：本教學課程中使用的 i2c 位址預設為0x77。根據您的設定，可能也會 0x76 .. 如果您遇到 i2c 錯誤，請嘗試將此值變更為118，並查看是否有更好的效果。若要查看您的感應器所使用的位址， `sudo i2cdetect -y 1`請在 raspberry pi 上的 shell 中執行*

2. 輸入 Control-O > 按 Enter 鍵 > Control-X 來儲存並結束。

### <a name="run-the-sample-application"></a>執行範例應用程式

執行下列命令，執行範例應用程式：

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > 確定複製裝置連接字串，並貼到單引號中。

您應該會看見下列輸出，顯示傳送至 IoT 中樞的感應器資料和訊息。

![輸出 - 從 Raspberry Pi 傳送至 IoT 中樞的感應器資料](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>讀取您的中樞所接收的訊息

若要監視您的 IoT 中樞從裝置接收的訊息，其中一種方式是使用 Visual Studio Code 的 Azure IoT Tools。 若要深入瞭解，請參閱[使用 Visual Studio Code 的 Azure IoT Tools，在您的裝置與 IoT 中樞之間傳送和接收訊息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

如需更多方式來處理您的裝置所傳送的資料，請繼續進行下一節。

## <a name="next-steps"></a>後續步驟

您已執行範例應用程式來收集感應器資料，並將它傳送到您的 IoT 中樞。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
