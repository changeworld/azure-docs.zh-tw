---
title: 在 Linux 上安裝 Azure IoT Edge | Microsoft Docs
description: 在執行 Ubuntu 或 Raspbian 的 Linux 裝置上 Azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: af53dea76670be500e7be20063487e3e4a2177b6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79285080"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>在以 Debian 為基礎的 Linux 系統上安裝 Azure IoT Edge 執行時間

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。 若要深入瞭解，請參閱[瞭解 Azure IoT Edge 執行時間及其架構](iot-edge-runtime.md)。

本文列出在 X64、ARM32 或 ARM64 Linux 裝置上安裝 Azure IoT Edge 執行時間的步驟。 我們提供 Ubuntu Server 16.04、Ubuntu Server 18.04 和 Raspbian Stretch 的安裝套件。 如需支援的 Linux 作業系統和架構清單，請參閱[Azure IoT Edge 支援的系統](support.md#operating-systems)。

> [!NOTE]
> Linux 軟體存放庫中的套件受限於每個套件中的授權條款 (/usr/share/doc/package-name)。 在使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

## <a name="install-the-latest-runtime-version"></a>安裝最新的執行階段版本

請使用下列各節，在您的裝置上安裝最新版本的 Azure IoT Edge 執行時間。

>[!NOTE]
>ARM64 裝置的支援現供[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="register-microsoft-key-and-software-repository-feed"></a>註冊 Microsoft 金鑰和軟體存放庫摘要

準備您的裝置以進行 IoT Edge 執行時間安裝。

安裝存放庫設定。 選擇符合您裝置作業系統的**16.04**或**18.04**命令：

* **Ubuntu Server 16.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian 延展**：

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

複製產生的清單。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

安裝 Microsoft GPG 公開金鑰

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>安裝容器執行階段

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器執行階段。 針對生產案例，建議您使用下面提供的[Moby 型](https://mobyproject.org/)引擎。 Moby 引擎是 Azure IoT Edge 正式支援的唯一容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

更新裝置上的套件清單。

   ```bash
   sudo apt-get update
   ```

安裝 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

安裝 Moby 命令列介面 (CLI)。 CLI 對於開發相當有用，但是對於生產部署則是選擇性的。

   ```bash
   sudo apt-get install moby-cli
   ```

如果您在安裝 Moby 容器執行時間時收到錯誤，請遵循下列步驟來[驗證您的 Linux 核心是否符合 Moby 相容性](#verify-your-linux-kernel-for-moby-compatibility)（如本文稍後所提供）。

### <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

**IoT Edge 安全性守護**程式會在 IoT Edge 裝置上提供並維護安全性標準。 此精靈會在每次開機時啟動，並藉由啟動 IoT Edge 執行階段讓裝置進入啟動程序。

安裝命令也會安裝**libiothsm**的標準版本（如果尚未存在的話）。

更新裝置上的套件清單。

   ```bash
   sudo apt-get update
   ```

安裝安全性精靈。 套件會安裝在 `/etc/iotedge/`。

   ```bash
   sudo apt-get install iotedge
   ```

成功安裝 IoT Edge 之後，輸出將會提示您更新設定檔。 請遵循[設定安全性守護](#configure-the-security-daemon)程式一節中的步驟，以完成裝置布建。

## <a name="install-a-specific-runtime-version"></a>安裝特定的執行階段版本

如果您想要安裝特定版本的 Moby 和 Azure IoT Edge 執行時間，而不是使用最新版本，您可以直接從 IoT Edge GitHub 存放庫將元件檔案設為目標。 使用下列步驟，將所有 IoT Edge 元件放在您的裝置上： Moby 引擎和 CLI、libiothsm，最後是 IoT Edge 的安全性守護程式。 如果您不想要變更為特定的執行階段版本，請跳到下一節：[設定安全性](#configure-the-security-daemon)背景程式。

1. 流覽至[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)，並尋找您要設為目標的發行版本。

2. 展開該版本的 [**資產**] 區段。

3. 在任何指定的版本中，Moby 引擎可能會有或不會更新。 如果您看到以**moby-engine**和**moby**為開頭的檔案，請使用下列命令來更新這些元件。 如果您沒有看到任何 Moby 檔案，請回到較舊的版本資產，直到您找到最新版本為止。

   1. 尋找符合 IoT Edge 裝置架構的**moby 引擎**檔案。 在 [檔案] 連結上按一下滑鼠右鍵，並複製連結位址。

   2. 在下列命令中使用複製的連結，安裝該版本的 Moby 引擎：

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. 尋找符合 IoT Edge 裝置架構的**moby cli**檔案。 Moby CLI 是選擇性元件，但在開發期間可能會很有説明。 在 [檔案] 連結上按一下滑鼠右鍵，並複製連結位址。

   4. 在下列命令中使用複製的連結，安裝該版本的 Moby CLI：

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. 每個版本都應該要有新的檔案，以供 IoT Edge security daemon 和 hsmlib。 使用下列命令來更新這些元件。

   1. 尋找符合 IoT Edge 裝置架構的**libiothsm 標準**檔案。 在 [檔案] 連結上按一下滑鼠右鍵，並複製連結位址。

   2. 在下列命令中使用複製的連結，安裝該版本的 hsmlib：

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. 尋找符合 IoT Edge 裝置架構的**iotedge**檔案。 在 [檔案] 連結上按一下滑鼠右鍵，並複製連結位址。

   4. 在下列命令中使用複製的連結，安裝該版本的 IoT Edge 安全性守護程式。

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

成功安裝 IoT Edge 之後，輸出將會提示您更新設定檔。 請遵循下一節中的步驟來完成裝置布建。

## <a name="configure-the-security-daemon"></a>設定安全性守護程式

設定 IoT Edge 執行階段，以連結您的實體裝置與 Azure IoT 中樞中存在的裝置身分識別。

您可以使用 `/etc/iotedge/config.yaml` 上的組態檔來設定精靈。 此檔案預設有防寫保護，您可能需要提高的權限才能加以編輯。

單一 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要佈建時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。

### <a name="option-1-manual-provisioning"></a>選項 1：手動佈建

若要手動佈建裝置，您需要提供[裝置連接字串](how-to-register-device.md#register-in-the-azure-portal)，在您的 IoT 中樞註冊新裝置即可建立該字串。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找檔案的布建設定，並取消批註**手動**布建設定一節。 以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。 請確定已將任何其他布建區段標記為批註。請確定布建 **：** 行沒有前面的空白字元，且嵌套的專案會以兩個空格縮排。

   ```yaml
   # Manual provisioning configuration
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   attestation:
   #     method: "tpm"
   #     registration_id: "{registration_id}"
   ```

若要將剪貼簿內容貼入 Nano `Shift+Right Click` 或按 `Shift+Insert`。

儲存並關閉檔案。

   `CTRL + X`、`Y`、`Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>選項 2：自動佈建

若要自動佈建裝置，[請設定裝置佈建服務並擷取您的裝置註冊識別碼](how-to-auto-provision-simulated-device-linux.md)。 使用自動布建時，IoT Edge 支援數種證明機制，但您的硬體需求也會影響您的選擇。 例如，Raspberry Pi 裝置預設不會隨附信賴平臺模組（TPM）晶片。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找檔案的布建設定，並取消批註適用于您證明機制的區段。 例如，在使用 TPM 證明時，請分別使用 IoT 中樞裝置布建服務的值和使用 TPM 的 IoT Edge 裝置來更新**scope_id**和**registration_id**的值。 請確定布建 **：** 行沒有前面的空白字元，且嵌套的專案會以兩個空格縮排。

   ```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
   ```

若要將剪貼簿內容貼入 Nano `Shift+Right Click` 或按 `Shift+Insert`。

儲存並關閉檔案。

   `CTRL + X`、`Y`、`Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

如果您在上一節中使用**手動設定**步驟，IoT Edge 執行階段應會在您的裝置上成功佈建並執行。 如果您使用**自動設定**步驟，則必須完成一些額外的步驟，讓執行階段可代表您向您的 IoT 中樞註冊裝置。 如需後續步驟，請參閱[在 Linux 虛擬機器上建立及布建模擬 TPM IoT Edge 裝置](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

您可以檢查 IoT Edge Daemon 的狀態：

```bash
systemctl status iotedge
```

檢查守護程式記錄檔：

```bash
journalctl -u iotedge --no-pager --no-full
```

針對最常見的設定和網路錯誤執行自動檢查：

```bash
sudo iotedge check
```

在您將第一個模組部署至裝置上的 IoT Edge 之前， **$edgeHub**系統模組不會部署到裝置。 因此，自動檢查會針對 `Edge Hub can bind to ports on host` 連線能力檢查傳回錯誤。 除非將模組部署至裝置之後，才會忽略此錯誤。

最後，列出執行中的模組：

```bash
sudo iotedge list
```

在您的裝置上安裝 IoT Edge 之後，您應該會看到執行的唯一模組是**edgeAgent**。 建立第一個部署之後，另一個系統模組 **$edgeHub**也會在裝置上啟動。 如需詳細資訊，請參閱[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

## <a name="tips-and-troubleshooting"></a>秘訣與疑難排解

您必須要有提高的權限才能執行 `iotedge` 命令。 安裝執行階段之後，請登出您的電腦並重新登入，以自動更新您的權限。 在那之前，請在任何 **命令前面使用**sudo`iotedge`。

在資源受限的裝置上，強烈建議將 OptimizeForPerformance 環境變數設定為 false，如[疑難排解指南](troubleshoot.md)中的指示所述。

如果您的網路具有 Proxy 伺服器，請遵循[設定您的 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中的步驟。

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>確認您的 Linux 核心是否有 Moby 相容性

許多內嵌裝置製造商會在沒有容器執行時間相容性所需功能的情況下，運送包含自訂 Linux 核心的裝置映射。 如果您在安裝建議的 Moby 容器執行時間時遇到問題，您可以使用官方[Moby GitHub 存放庫](https://github.com/moby/moby)中的[檢查-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh)腳本，針對 Linux 核心設定進行疑難排解。 在裝置上執行下列命令，以檢查您的核心設定：

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

此命令提供詳細輸出，其中包含 Moby 執行時間所使用之核心功能的狀態。 您會想要確保 `Generally Necessary` 和 `Network Drivers` 下的所有專案都已啟用，以確保您的核心與 Moby 執行時間完全相容。  如果您已識別出任何遺漏的功能，請從來源重建核心並選取相關聯的模組以包含在適當的 config.xml 中，以啟用它們。 同樣地，如果您使用 defconfig 或 menuconfig 這類核心設定產生器，請尋找並啟用各自的功能，並據以重建您的核心。  一旦您已部署新修改的核心，請再次執行檢查-config 腳本，以確認所有必要的功能都已成功啟用。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Linux 裝置中移除 IoT Edge 安裝，請在命令列中使用下列命令。

移除 IoT Edge 執行階段。

```bash
sudo apt-get remove --purge iotedge
```

當 IoT Edge 執行時間移除時，它所建立的容器會停止，但仍存在於您的裝置上。 檢視所有容器以查看哪些容器保留下來。

```bash
sudo docker ps -a
```

刪除您裝置中的容器，包括兩個執行階段容器。

```bash
sudo docker rm -f <container name>
```

最後，移除裝置中的容器執行階段。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您有 IoT Edge 執行時間正確安裝的問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
