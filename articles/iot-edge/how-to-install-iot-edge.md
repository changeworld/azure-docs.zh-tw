---
title: 安裝 Azure IoT Edge |Microsoft Docs
description: 在 Windows 或 Linux 裝置上 Azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
ms.openlocfilehash: ab783d6cb20f1c2fe31e8556dc57999df20d5637
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629805"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>安裝或卸載 Linux Azure IoT Edge

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。 若要深入瞭解，請參閱 [瞭解 Azure IoT Edge 執行時間及其架構](iot-edge-runtime.md)。

本文列出在 Linux 裝置上安裝 Azure IoT Edge 執行時間的步驟。

## <a name="prerequisites"></a>必要條件

* [已註冊的裝置識別碼](how-to-register-device.md)

  如果您已向對稱金鑰驗證註冊裝置，請備妥裝置連接字串。

  如果您已向 x.509 自我簽署憑證驗證註冊您的裝置，則至少要有一個身分識別憑證可供您用來在裝置上註冊裝置及其對應的私密金鑰。

* Linux 裝置

  擁有 X64、ARM32 或 ARM64 Linux 裝置。 Microsoft 為 Ubuntu Server 16.04、Ubuntu Server 18.04 和 Raspberry Pi OS 延展作業系統提供安裝套件。

  如需有關生產案例目前支援哪些作業系統的最新資訊，請參閱 [Azure IoT Edge 支援的系統](support.md#operating-systems)

  >[!NOTE]
  >ARM64 裝置的支援處於 [公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

* 準備您的裝置以存取 Microsoft 安裝套件。

  安裝符合您裝置作業系統的存放庫組態。

  * **Ubuntu Server 16.04**：

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Ubuntu Server 18.04**：

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**：

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  將產生的清單複製到 sources.list.d 目錄。

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  安裝 Microsoft GPG 公開金鑰。

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge 軟體套件受限於每個套件 (`usr/share/doc/{package-name}` 或目錄) 的授權條款 `LICENSE` 。 使用套件之前，請先閱讀授權條款。 您的安裝和使用套件會構成您接受這些條款的規範。 如果您不同意授權條款，請不要使用該套件。

## <a name="install-a-container-engine"></a>安裝容器引擎

Azure IoT Edge 會依賴 OCI 相容的容器執行階段。 針對生產案例，建議使用 Moby 引擎。 Moby 引擎是唯一 Azure IoT Edge 正式支援的容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

更新裝置上的套件清單。

   ```bash
   sudo apt-get update
   ```

安裝 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

如果您在安裝 Moby 容器引擎時收到錯誤，請確認您的 Linux 核心是否有 Moby 相容性。 某些 embedded 裝置製造商會提供包含自訂 Linux 核心的裝置映射，而不需要容器引擎相容性所需的功能。 執行下列命令，此命令會使用 Moby 所提供的 [檢查設定腳本](https://github.com/moby/moby/blob/master/contrib/check-config.sh) 來檢查您的核心設定：

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

在腳本的輸出中，檢查 `Generally Necessary` 是否已啟用和下的所有專案 `Network Drivers` 。 如果您遺漏了功能，請從來源重建您的核心，然後選取相關聯的模組以納入適當的 applicationhost.config 中，來啟用這些功能。同樣地，如果您使用的是核心設定產生器（例如 `defconfig` 或 `menuconfig` ），請尋找並啟用各自的功能，並據以重建您的核心。 當您部署新修改過的核心之後，請再次執行檢查設定腳本，以確認所有必要的功能都已成功啟用。

## <a name="install-the-iot-edge-security-daemon"></a>安裝 IoT Edge security daemon

IoT Edge 的安全性背景程式可在 IoT Edge 裝置上提供及維護安全性標準。 此精靈會在每次開機時啟動，並藉由啟動 IoT Edge 執行階段讓裝置進入啟動程序。

本節中的步驟代表在具有網際網路連線的裝置上安裝最新版本的一般程式。 如果您需要安裝特定版本，例如發行前版本，或需要離線安裝，請遵循下一節中的 [離線或特定版本安裝](#offline-or-specific-version-installation-optional) 步驟。

更新裝置上的套件清單。

   ```bash
   sudo apt-get update
   ```

檢查以查看有哪些 IoT Edge 版本可供使用。

   ```bash
   apt list -a iotedge
   ```

如果您想要安裝最新版本的安全 daemon，請使用下列命令，該命令也會安裝最新版的 **libiothsm-std** 封裝：

   ```bash
   sudo apt-get install iotedge
   ```

或者，如果您想要安裝特定版本的安全性守護程式，請從 apt 清單輸出中指定版本。 也請為 **libiothsm-std** 封裝指定相同的版本，否則會安裝其最新版本。 例如，下列命令會安裝最新版本的1.0.10 版本：

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

如果未列出您想要安裝的版本，請遵循本文稍後的 [離線或特定版本安裝](#offline-or-specific-version-installation-optional) 步驟。 該章節將說明如何以任何舊版的 IoT Edge 安全性守護程式或發行候選版本為目標。

## <a name="provision-the-device-with-its-cloud-identity"></a>使用雲端身分識別來布建裝置

既然容器引擎和 IoT Edge 執行時間已安裝在您的裝置上，您就可以開始進行下一個步驟，也就是設定裝置的雲端身分識別和驗證資訊。

根據您想要使用的驗證類型，選擇下一節：

* [選項1：使用對稱金鑰進行驗證](#option-1-authenticate-with-symmetric-keys)
* [選項2：使用 x.509 憑證進行驗證](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>選項1：使用對稱金鑰進行驗證

此時，IoT Edge 執行時間會安裝在您的 Linux 裝置上，而您必須使用其雲端身分識別和驗證資訊來布建裝置。

本節將逐步解說使用對稱金鑰驗證布建裝置的步驟。 您應該已在 IoT 中樞註冊您的裝置，並從裝置資訊中取出連接字串。 如果沒有，請遵循在 [IoT 中樞註冊 IoT Edge 裝置](how-to-register-device.md)中的步驟。

在 IoT Edge 裝置上，開啟設定檔。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

尋找檔案的佈建組態，然後將 **使用連接字串手動佈建組態** 區段取消註解。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。 請確定已將任何其他布建區段標記為批註。請確定布建 **：** 行沒有先前的空格，而且嵌套的專案是以兩個空格縮排。

將剪貼簿內容貼入 Nano `Shift+Right Click` 或按下 `Shift+Insert`。

儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="option-2-authenticate-with-x509-certificates"></a>選項2：使用 x.509 憑證進行驗證

此時，IoT Edge 執行時間會安裝在您的 Linux 裝置上，而您必須使用其雲端身分識別和驗證資訊來布建裝置。

本節將逐步解說使用 x.509 憑證驗證布建裝置的步驟。 您應該已在 IoT 中樞註冊您的裝置，並提供符合您 IoT Edge 裝置上的憑證和私密金鑰的指紋。 如果沒有，請遵循在 [IoT 中樞註冊 IoT Edge 裝置](how-to-register-device.md)中的步驟。

在 IoT Edge 裝置上，開啟設定檔。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

尋找檔案的布建設定區段，並 **使用 x.509 身分識別憑證** 取消批註手動布建設定一節。 請確定已將任何其他布建區段標記為批註。請確定布建 **：** 行沒有先前的空格，而且嵌套的專案是以兩個空格縮排。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

更新下欄欄位：

* **iothub_hostname**：裝置將連接的 IoT 中樞主機名稱。 例如： `{IoT hub name}.azure-devices.net` 。
* **device_id**：您在註冊裝置時所提供的識別碼。
* **identity_cert**：裝置上身分識別憑證的 URI。 例如： `file:///path/identity_certificate.pem` 。
* **identity_pk**：提供的身分識別憑證之私密金鑰檔案的 URI。 例如： `file:///path/identity_key.pem` 。

儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-successful-configuration"></a>確認設定成功

確認執行階段已在您的 IoT Edge 裝置上成功安裝並設定。

1. 確認 IoT Edge 安全性精靈正以系統服務的形式執行。

   ```bash
   sudo systemctl status iotedge
   ```

   >[!TIP]
   >您必須要有提高的權限才能執行 `iotedge` 命令。 當您在安裝 IoT Edge 執行階段之後登出機器，並第一次重新登入時，您的權限將會自動更新。 在那之前，請在這些命令前面使用 `sudo`。

2. 如果您需要對服務進行疑難排解，請擷取服務記錄。

   ```bash
   journalctl -u iotedge
   ```

3. 您可以使用此 `check` 工具來確認裝置的設定和連接狀態。

   ```bash
   sudo iotedge check
   ```

   >[!TIP]
   >一律使用 `sudo` 來執行檢查工具，即使在您的許可權更新之後也一樣。 此工具需要較高的許可權才能存取 **yaml** 檔案，以驗證設定狀態。

4. 檢視在 IoT Edge 裝置上執行的所有模組。 當服務第一次啟動時，您應該只會看到 **edgeAgent** 模組正在執行。 EdgeAgent 模組預設會執行，且有助於安裝及啟動您部署至裝置的任何其他模組。

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>離線或特定版本安裝 (選用) 

本節中的步驟適用于標準安裝步驟未涵蓋的案例。 這可能包括：

* 離線時安裝 IoT Edge
* 安裝發行候選版本

如果您想要安裝無法透過提供的特定 Azure IoT Edge 執行階段版本，請使用本節中的步驟 `apt-get install` 。 Microsoft 套件清單只包含一組有限的最新版本及其子版本，因此這些步驟適用于想要安裝較舊版本或發行候選版本的任何人。

使用捲曲命令，您可以直接從 IoT Edge GitHub 存放庫將元件檔案設為目標。

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

既然容器引擎和 IoT Edge 執行時間已安裝在您的裝置上，您就可以開始進行下一個步驟，也就是 [使用其雲端身分識別](#provision-the-device-with-its-cloud-identity)來布建裝置。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從裝置移除 IoT Edge 安裝，請使用下列命令。

移除 IoT Edge 執行階段。

```bash
sudo apt-get remove --purge iotedge
```

移除 IoT Edge 執行時間時，它所建立的任何容器都會停止，但仍會存在於您的裝置上。 檢視所有容器以查看哪些容器保留下來。

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

## <a name="next-steps"></a>下一步

繼續 [部署 IoT Edge 課程模組](how-to-deploy-modules-portal.md) ，以瞭解如何將模組部署到您的裝置。
