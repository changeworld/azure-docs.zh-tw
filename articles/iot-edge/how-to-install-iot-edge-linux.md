---
title: 在 Linux 上安裝 Azure IoT Edge | Microsoft Docs
description: 運行 Ubuntu 或 Raspbian 的 Linux 設備上的 Azure IoT 邊緣安裝說明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535896"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>在以 Debian 為基礎的 Linux 系統上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。 要瞭解更多資訊，請參閱[瞭解 Azure IoT 邊緣運行時及其體系結構](iot-edge-runtime.md)。

本文列出了在 X64、ARM32 或 ARM64 Linux 設備上安裝 Azure IoT 邊緣運行時的步驟。 我們為 Ubuntu 伺服器 16.04、Ubuntu 伺服器 18.04 和 Raspbian 拉伸提供安裝包。 有關支援的 Linux 作業系統和體系結構的清單，請參閱[Azure IoT Edge 支援的系統](support.md#operating-systems)。

> [!NOTE]
> Linux 軟體存放庫中的套件受限於每個套件中的授權條款 (/usr/share/doc/package-name**)。 在使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

## <a name="install-the-latest-runtime-version"></a>安裝最新的執行階段版本

使用以下部分將 Azure IoT Edge 運行時的最新版本安裝到您的設備上。

>[!NOTE]
>對 ARM64 設備的支援處於[公共預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中。

### <a name="register-microsoft-key-and-software-repository-feed"></a>註冊 Microsoft 金鑰和軟體存放庫摘要

為 IoT 邊緣運行時安裝準備設備。

安裝存儲庫配置。 選擇與設備作業系統匹配的**16.04**或**18.04**命令：

* **烏本圖伺服器 16.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **烏本圖伺服器 18.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **拉斯普比安拉伸**：

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

複製生成的清單。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

安裝微軟 GPG 公開金鑰

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>安裝容器執行階段

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器執行階段。 對於生產方案，我們建議您使用下面提供的[基於 Moby 的](https://mobyproject.org/)引擎。 Moby 引擎是 Azure IoT Edge 正式支援的唯一容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

更新設備上的包清單。

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

如果在安裝 Moby 容器運行時時遇到錯誤，請按照以下步驟驗證[Linux 內核的 Moby 相容性](#verify-your-linux-kernel-for-moby-compatibility)，本文稍後將提供。

### <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

**IoT Edge 安全守護程式**在 IoT Edge 設備上提供和維護安全標準。 此精靈會在每次開機時啟動，並藉由啟動 IoT Edge 執行階段讓裝置進入啟動程序。

如果不存在，安裝命令還會安裝標準版本的**Libiothm。**

更新設備上的包清單。

   ```bash
   sudo apt-get update
   ```

安裝安全性精靈。 套件會安裝在 `/etc/iotedge/`。

   ```bash
   sudo apt-get install iotedge
   ```

成功安裝 IoT Edge 後，輸出將提示您更新設定檔。 按照["配置安全守護程式](#configure-the-security-daemon)"部分中的步驟完成設備配置。

## <a name="install-a-specific-runtime-version"></a>安裝特定的執行階段版本

如果要安裝特定于版本的 Moby 和 Azure IoT Edge 運行時，而不是使用最新版本，則可以直接從 IoT 邊緣 GitHub 存儲庫定位元件檔。 使用以下步驟將所有 IoT Edge 元件安裝到您的設備上：Moby 引擎和 CLI、Libiothm，最後是 IoT Edge 安全守護進程。 如果不想更改為特定的執行階段版本，請跳到下一節"[配置安全守護進程](#configure-the-security-daemon)"。

1. 導航到[Azure IoT 邊緣版本](https://github.com/Azure/azure-iotedge/releases)，並找到要定位的版本。

2. 展開該版本的 **"資產**"部分。

3. 在任何給定版本中，Moby 引擎的更新可能更新，也可能沒有更新。 如果您看到以**月比引擎**和**月比 cli**開頭的檔，請使用以下命令更新這些元件。 如果您沒有看到任何 Moby 檔，則返回舊版本資產，直到找到最新版本。

   1. 查找與 IoT Edge 設備體系結構相匹配的**moby 引擎**檔。 按右鍵檔連結並複製連結位址。

   2. 使用以下命令中的複製連結安裝該版本的 Moby 引擎：

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. 查找與 IoT Edge 設備體系結構相匹配的**moby-cli**檔。 Moby CLI 是可選元件，但在開發過程中非常有用。 按右鍵檔連結並複製連結位址。

   4. 使用以下命令中的複製連結安裝該版本的 Moby CLI：

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. 每個版本都應具有 IoT 邊緣安全守護進程和 hsmlib 的新檔。 使用以下命令更新這些元件。

   1. 查找與 IoT Edge 設備體系結構相匹配的**Libiothm std**檔。 按右鍵檔連結並複製連結位址。

   2. 使用以下命令中的複製連結安裝該版本的 hsmlib：

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. 查找與 IoT Edge 設備體系結構相匹配的**iotedge**檔。 按右鍵檔連結並複製連結位址。

   4. 使用以下命令中的複製連結安裝該版本的 IoT Edge 安全守護進程。

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

成功安裝 IoT Edge 後，輸出將提示您更新設定檔。 按照下一節中的步驟完成設備配置。

## <a name="configure-the-security-daemon"></a>配置安全守護進程

設定 IoT Edge 執行階段，以連結您的實體裝置與 Azure IoT 中樞中存在的裝置身分識別。

您可以使用 `/etc/iotedge/config.yaml` 上的組態檔來設定精靈。 此檔案預設有防寫保護，您可能需要提高的權限才能加以編輯。

單一 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要佈建時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。

### <a name="option-1-manual-provisioning"></a>選項 1：手動佈建

若要手動佈建裝置，您需要提供[裝置連接字串](how-to-register-device.md#register-in-the-azure-portal)，在您的 IoT 中樞註冊新裝置即可建立該字串。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

查找檔的預配配置，並取消注釋 **"手動預配配置**"部分。 以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。 確保注釋任何其他預配部分。確保**預配：** 行沒有前面的空格，並且嵌套項由兩個空格縮進。

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

要將剪貼簿內容粘貼`Shift+Right Click`到`Shift+Insert`Nano 或 按 。

儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>選項 2：自動佈建

可以使用[Azure IoT 中心設備預配服務 （DPS）](../iot-dps/index.yml)自動預配 IoT 邊緣設備。 目前，IoT Edge 支援使用自動預配時的兩個認證機制，但硬體要求可能會影響您的選擇。 例如，預設情況下，樹莓派設備不附帶可信平臺模組 （TPM） 晶片。 如需詳細資訊，請參閱下列文章：

* [在 Linux VM 上使用虛擬 TPM 創建和預配 IoT 邊緣設備](how-to-auto-provision-simulated-device-linux.md)
* [使用 X.509 憑證創建和預配 IoT 邊緣設備](how-to-auto-provision-x509-certs.md)
* [使用對稱金鑰認證創建和預配 IoT 邊緣設備](how-to-auto-provision-symmetric-keys.md)

這些文章引導您完成在 DPS 中設置註冊，並生成正確的證書或金鑰進行證明。 無論您選擇哪種認證機制，預配資訊都會添加到 IoT Edge 設備上的 IoT Edge 設定檔中。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

查找檔的預配配置，並取消注釋適合您的證明機制的部分。 確保注釋任何其他預配部分。**預配：** 行不應具有前面的空格，嵌套項應縮進兩個空格。 使用 IoT 中心設備預配服務實例中的值更新**scope_id**的值，並為證明欄位提供適當的值。

TPM 證明：

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

X.509 證明：

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

對稱金鑰證明：

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

要將剪貼簿內容粘貼`Shift+Right Click`到`Shift+Insert`Nano 或 按 。

儲存並關閉檔案。 `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

如果您在上一節中使用**手動設定**步驟，IoT Edge 執行階段應會在您的裝置上成功佈建並執行。 如果您使用**自動設定**步驟，則必須完成一些額外的步驟，讓執行階段可代表您向您的 IoT 中樞註冊裝置。 有關後續步驟，請參閱[在 Linux 虛擬機器上創建和預配類比的 TPM IoT 邊緣設備](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

您可以檢查 IoT 邊緣守護進程的狀態：

```bash
systemctl status iotedge
```

檢查守護進程日誌：

```bash
journalctl -u iotedge --no-pager --no-full
```

運行針對最常見的配置和網路錯誤的自動檢查：

```bash
sudo iotedge check
```

在將第一個模組部署到設備上的 IoT Edge 之前 **，$edgeHub**系統模組將不會部署到設備。 因此，自動檢查將返回`Edge Hub can bind to ports on host`連接檢查的錯誤。 除非在將模組部署到設備後發生，否則可以忽略此錯誤。

最後，列出正在運行的模組：

```bash
sudo iotedge list
```

在設備上安裝 IoT Edge 後，您應該看到運行的唯一模組是**邊緣代理**。 創建第一個部署後，$edgeHub的其他**系統模組也將**在設備上啟動。 有關詳細資訊，請參閱部署[IoT 邊緣模組](how-to-deploy-modules-portal.md)。

## <a name="tips-and-troubleshooting"></a>秘訣與疑難排解

您必須要有提高的權限才能執行 `iotedge` 命令。 安裝執行階段之後，請登出您的電腦並重新登入，以自動更新您的權限。 在那之前，請在任何 `iotedge` 命令前面使用 **sudo**。

在資源受限的裝置上，強烈建議將 OptimizeForPerformance** 環境變數設定為 false**，如[疑難排解指南](troubleshoot.md)中的指示所述。

如果您的網路具有 Proxy 伺服器，請遵循[設定您的 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中的步驟。

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>驗證 Linux 內核的 Moby 相容性

許多嵌入式設備製造商提供包含自訂 Linux 內核的設備映射，而不需要容器運行時相容性所需的功能。 如果您在安裝建議的 Moby 容器運行時遇到問題，則可以使用官方[Moby GitHub 存儲庫](https://github.com/moby/moby)中的[檢查配置](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh)腳本解決 Linux 內核配置問題。 在設備上運行以下命令以檢查內核配置：

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

此命令提供詳細的輸出，其中包含 Moby 運行時使用的內核功能的狀態。 您需要確保`Generally Necessary`和`Network Drivers`啟用的所有項，以確保內核與 Moby 運行時完全相容。  如果已識別任何缺失的功能，請通過從源重建內核並選擇關聯的模組包含在相應的內核 .config 中來啟用這些功能。 同樣，如果您使用的是內核配置產生器，如`defconfig`或`menuconfig`，查找並啟用相應的功能，並相應地重建內核。  部署新修改的內核後，請再次運行檢查配置腳本，以驗證所有必需的功能是否已成功啟用。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Linux 裝置中移除 IoT Edge 安裝，請在命令列中使用下列命令。

移除 IoT Edge 執行階段。

```bash
sudo apt-get remove --purge iotedge
```

刪除 IoT Edge 運行時後，它創建的容器將停止，但仍存在於您的設備上。 檢視所有容器以查看哪些容器保留下來。

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

如果 IoT Edge 運行時安裝正確出現問題，請查看[故障排除](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
