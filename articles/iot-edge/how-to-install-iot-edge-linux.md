---
title: 在 Linux 上安裝 Azure IoT Edge | Microsoft Docs
description: 在執行 Ubuntu 或 Raspbian 的 Linux 裝置上 Azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: kgremban
ms.openlocfilehash: 4078d7e6c20571db2387cfd138ecb325fc3469e7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022083"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>在以 Debian 為基礎的 Linux 系統上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。 若要深入瞭解，請參閱 [瞭解 Azure IoT Edge 執行時間及其架構](iot-edge-runtime.md)。

本文列出在 X64、ARM32 或 ARM64 Linux 裝置上安裝 Azure IoT Edge 執行時間的步驟。 我們會為 Ubuntu Server 16.04、Ubuntu Server 18.04 和 Raspbian Stretch 提供安裝套件。 請參閱 [Azure IoT Edge 支援的系統](support.md#operating-systems) ，以取得支援的 Linux 作業系統和架構清單。

> [!NOTE]
> Linux 軟體存放庫中的套件受限於每個套件中的授權條款 (/usr/share/doc/package-name**)。 在使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

## <a name="install-iot-edge-and-container-runtimes"></a>安裝 IoT Edge 和容器執行時間

請使用以下各節，在您的裝置上安裝最新版本的 Azure IoT Edge 執行時間。

>[!NOTE]
>ARM64 裝置的支援處於 [公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

### <a name="register-microsoft-key-and-software-repository-feed"></a>註冊 Microsoft 金鑰和軟體存放庫摘要

為您的裝置做好 IoT Edge 執行時間安裝的準備工作。

安裝存放庫設定。 選擇符合您裝置作業系統的 **16.04** 或 **18.04** 命令：

* **Ubuntu Server 16.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**：

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

複製產生的清單。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

安裝 Microsoft GPG 公開金鑰。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>安裝容器執行階段

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器執行階段。 針對生產案例，建議您使用下面提供的 [Moby 型](https://mobyproject.org/) 引擎。 Moby 引擎是唯一 Azure IoT Edge 正式支援的容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

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

如果您在安裝 Moby 容器執行時間時收到錯誤，請遵循下列步驟來 [驗證您的 Linux 核心是否有 Moby 相容性](#verify-your-linux-kernel-for-moby-compatibility)，如本文稍後所述。

### <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

**IoT Edge 的安全性**背景程式可在 IoT Edge 裝置上提供及維護安全性標準。 此精靈會在每次開機時啟動，並藉由啟動 IoT Edge 執行階段讓裝置進入啟動程序。

更新裝置上的套件清單。

   ```bash
   sudo apt-get update
   ```

檢查以查看有哪些 IoT Edge 版本可供使用。

   ```bash
   apt list -a iotedge
   ```

如果您想要安裝最新版本的安全 daemon，請使用下列命令，該命令也會安裝最新版的 **libiothsm 標準** 套件：

   ```bash
   sudo apt-get install iotedge
   ```

如果您想要安裝特定版本的安全性守護程式，請從 apt 清單輸出中指定版本。 也請為 **libiothsm-std** 封裝指定相同的版本，否則會安裝其最新版本。 例如，下列命令會安裝最新版本的1.0.8 版本：

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

如果未列出您想要安裝的版本，請遵循 [使用發行資產安裝運行](#install-runtime-using-release-assets)時間中的步驟。 該章節將說明如何以任何舊版的 IoT Edge 安全性守護程式或發行候選版本為目標。

成功安裝 IoT Edge 之後 `/etc/iotedge/` ，輸出會提示您更新設定檔案。 繼續進行下一節以完成裝置布建。

## <a name="configure-the-security-daemon"></a>設定安全 daemon

設定 IoT Edge 執行階段，以連結您的實體裝置與 Azure IoT 中樞中存在的裝置身分識別。

您可以使用 `/etc/iotedge/config.yaml` 上的組態檔來設定精靈。 此檔案預設有防寫保護，您可能需要提高的權限才能加以編輯。

單一 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要佈建時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。

### <a name="option-1-manual-provisioning"></a>選項 1：手動佈建

若要手動佈建裝置，您需要提供[裝置連接字串](how-to-register-device.md#register-in-the-azure-portal)，在您的 IoT 中樞註冊新裝置即可建立該字串。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找檔案的布建設定，並取消批註 [ **手動** 布建設定] 區段。 以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。 請確定任何其他布建區段都已批註化。請確定布建 **：** 行沒有先前的空格，而且嵌套的專案是以兩個空格縮排。

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

將剪貼簿內容貼到 Nano `Shift+Right Click` 或按下 `Shift+Insert` 。

儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>選項 2：自動佈建

IoT Edge 的裝置可以使用 [Azure IoT 中樞裝置布建服務 (DPS) ](../iot-dps/index.yml)自動布建。 目前，IoT Edge 在使用自動布建時支援三種證明機制，但您的硬體需求可能會影響您的選擇。 例如，Raspberry Pi 裝置預設不會隨附可信賴平臺模組 (TPM) 晶片。 如需詳細資訊，請參閱下列文章：

* [在 Linux VM 上使用虛擬 TPM 建立和布建 IoT Edge 裝置](how-to-auto-provision-simulated-device-linux.md)
* [使用 x.509 憑證來建立和布建 IoT Edge 裝置](how-to-auto-provision-x509-certs.md)
* [使用對稱金鑰證明來建立和布建 IoT Edge 裝置](how-to-auto-provision-symmetric-keys.md)

這些文章會逐步引導您在 DPS 中設定註冊，以及產生適當的憑證或金鑰以進行證明。 無論您選擇哪一種證明機制，布建資訊都會新增至 IoT Edge 裝置上的 IoT Edge 設定檔。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找檔案的布建設定，並取消批註適合您證明機制的區段。 請確定任何其他布建區段都已批註化。布建 **：** 行上不應該有空白，而嵌套專案應以兩個空格縮排。 將 **scope_id** 的值更新為 IoT 中樞裝置布建服務實例中的值，並為證明欄位提供適當的值。

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

將剪貼簿內容貼到 Nano `Shift+Right Click` 或按下 `Shift+Insert` 。

儲存並關閉檔案。 `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

如果您在上一節中使用**手動設定**步驟，IoT Edge 執行階段應會在您的裝置上成功佈建並執行。 如果您使用**自動設定**步驟，則必須完成一些額外的步驟，讓執行階段可代表您向您的 IoT 中樞註冊裝置。 如需後續步驟，請參閱 [在 Linux 虛擬機器上建立及布建模擬 TPM IoT Edge 裝置](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

您可以檢查 IoT Edge Daemon 的狀態：

```bash
systemctl status iotedge
```

檢查 daemon 記錄：

```bash
journalctl -u iotedge --no-pager --no-full
```

執行 [疑難排解工具](troubleshoot.md#run-the-check-command) ，檢查是否有最常見的設定和網路錯誤：

```bash
sudo iotedge check
```

在您將第一個模組部署至裝置上的 IoT Edge 之前， **$edgeHub** 系統模組將不會部署到裝置。 如此一來，自動檢查就會傳回連線檢查的錯誤 `Edge Hub can bind to ports on host` 。 除非您將模組部署到裝置之後發生此錯誤，否則可能會忽略此錯誤。

最後，列出正在執行的模組：

```bash
sudo iotedge list
```

在裝置上安裝 IoT Edge 之後，您應該會看到執行的唯一模組是 **edgeAgent**。 建立第一個部署之後，其他系統模組 **$edgeHub** 也會在裝置上啟動。 如需詳細資訊，請參閱 [部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

## <a name="tips-and-troubleshooting"></a>提示與疑難排解

您必須要有提高的權限才能執行 `iotedge` 命令。 安裝執行階段之後，請登出您的電腦並重新登入，以自動更新您的權限。 在那之前，請在任何 `iotedge` 命令前面使用 **sudo**。

在資源受限的裝置上，強烈建議將 OptimizeForPerformance** 環境變數設定為 false**，如[疑難排解指南](troubleshoot.md)中的指示所述。

如果您的網路具有 Proxy 伺服器，請遵循[設定您的 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中的步驟。

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>確認 Linux 核心的 Moby 相容性

許多 embedded 裝置製造商都會送出包含自訂 Linux 核心的裝置映射，而不需要容器執行時間相容性所需的功能。 如果您在安裝建議的 Moby 容器執行時間時遇到問題，您可以使用官方[Moby GitHub 存放庫](https://github.com/moby/moby)的[檢查](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh)設定腳本，針對 Linux 核心設定進行疑難排解。 在裝置上執行下列命令，以檢查您的核心設定：

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

此命令會提供詳細的輸出，其中包含 Moby 執行時間所使用之核心功能的狀態。 您會想要確定已啟用和下的所有專案， `Generally Necessary`  `Network Drivers` 以確保您的核心與 Moby 執行時間完全相容。  如果您已識別出缺少的任何功能，請從來源重建您的核心，然後選取要包含在適當 kernel 中的相關聯模組，來加以啟用。 同樣地，如果您使用的是核心設定產生器（例如 `defconfig` 或 `menuconfig` ），請尋找並啟用各自的功能，並據以重建您的核心。  當您部署新修改過的核心之後，請再次執行檢查設定腳本，以確認所有必要的功能都已成功啟用。

## <a name="install-runtime-using-release-assets"></a>使用發行資產安裝執行時間

如果您想要安裝無法透過提供的特定 Azure IoT Edge 執行階段版本，請使用本節中的步驟 `apt-get install` 。 Microsoft 套件清單只包含一組有限的最新版本及其子版本，因此這些步驟適用于想要安裝較舊版本或發行候選版本的任何人。

使用捲曲命令，您可以直接從 IoT Edge GitHub 存放庫將元件檔案設為目標。 使用下列步驟來安裝 libiothsm 和 IoT Edge security daemon。 使用「 [安裝容器運行](#install-a-container-runtime) 時間」一節中的步驟來安裝 Moby 引擎和 CLI。

1. 流覽至 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)，然後尋找您想要作為目標的發行版本。

2. 展開該版本的 [ **資產** ] 區段。

3. 每個版本都應該有 IoT Edge 安全 daemon 和 hsmlib 的新檔案。 使用下列命令來更新這些元件。

   1. 尋找符合 IoT Edge 裝置架構的 **libiothsm 標準** 檔案。 以滑鼠右鍵按一下檔案連結，然後複製連結位址。

   2. 使用下列命令中複製的連結，安裝該版本的 hsmlib：

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. 尋找符合 IoT Edge 裝置架構的 **iotedge** 檔案。 以滑鼠右鍵按一下檔案連結，然後複製連結位址。

   4. 使用下列命令中複製的連結，安裝該版本的 IoT Edge security daemon。

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

成功安裝 IoT Edge 之後 `/etc/iotedge` ，輸出會提示您更新設定檔案。 請依照「 [設定安全性守護](#configure-the-security-daemon) 程式」一節中的步驟完成裝置布建。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Linux 裝置中移除 IoT Edge 安裝，請在命令列中使用下列命令。

移除 IoT Edge 執行階段。

```bash
sudo apt-get remove --purge iotedge
```

移除 IoT Edge 執行時間時，它所建立的容器會停止，但仍會存在於您的裝置上。 檢視所有容器以查看哪些容器保留下來。

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

如果您在 IoT Edge 執行時間安裝正常時遇到問題，請參閱 [疑難排解](troubleshoot.md) 頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
