---
title: 安裝 Azure IoT Edge |Microsoft Docs
description: 在 Windows 或 Linux 裝置上 Azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: a7794bcdfa4f82698fdc5875bc94dcf52b70166e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185091"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>安裝或卸載 Azure IoT Edge 執行時間

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。 若要深入瞭解，請參閱 [瞭解 Azure IoT Edge 執行時間及其架構](iot-edge-runtime.md)。

設定 IoT Edge 裝置有兩個步驟。 第一個步驟是安裝執行時間及其相依性，這會在本文中討論。 第二個步驟是將裝置連線至雲端中的身分識別，並設定 IoT 中樞的驗證。 這些步驟位於下一篇文章中。

本文列出在 Linux 或 Windows 裝置上安裝 Azure IoT Edge 執行時間的步驟。 針對 Windows 裝置，您有其他使用 Linux 容器或 Windows 容器的選項。 目前，Windows 上的 Windows 容器建議用於生產案例。 Windows 上的 Linux 容器適用于開發和測試案例，特別是如果您是在 Windows 電腦上進行開發，以便部署到 Linux 裝置。

## <a name="prerequisites"></a>Prerequisites

如需有關生產案例目前支援哪些作業系統的最新資訊，請參閱 [Azure IoT Edge 支援的系統](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

擁有 X64、ARM32 或 ARM64 Linux 裝置。 Microsoft 為 Ubuntu Server 16.04、Ubuntu Server 18.04 和 Raspbian 延展作業系統提供安裝套件。

>[!NOTE]
>ARM64 裝置的支援處於 [公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

準備您的裝置以存取 Microsoft 安裝套件。

1. 安裝符合您裝置作業系統的存放庫設定。

   * **Ubuntu Server 16.04**：

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18.04**：

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry PI 作業系統延展**：

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. 將產生的清單複製到來源. d 目錄。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. 安裝 Microsoft GPG 公開金鑰。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows 版本

使用 Windows 容器 IoT Edge 需要 Windows 版本 1809/組建17762，這是最新的 [windows 長期支援組建](/windows/release-information/)。 針對開發和測試案例，支援容器功能的任何 SKU (Pro、Enterprise、Server 等 ) 都可運作。 不過，在進入生產環境之前，請務必先檢查支援的系統清單。

使用 Linux 容器的 IoT Edge 可以在任何符合 [Docker Desktop 需求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的 Windows 版本上執行。

### <a name="container-engine-requirements"></a>容器引擎需求

Azure IoT Edge 依賴 [OCI 相容](https://www.opencontainers.org/) 的容器引擎。 請確定您的裝置可以支援容器。

如果您要在虛擬機器上安裝 IoT Edge，請啟用嵌套虛擬化，並配置至少 2 GB 的記憶體。 針對 Hyper-v，第2代虛擬機器預設會啟用嵌套虛擬化。 針對 VMware，有一個切換功能可以在您的虛擬機器上啟用此功能。

如果您要在 IoT 核心裝置上安裝 IoT Edge，請在 [遠端 PowerShell 會話](/windows/iot-core/connect-your-device/powershell) 中使用下列命令，以檢查您的裝置是否支援 Windows 容器：

```powershell
Get-Service vmcompute
```

---

Azure IoT Edge 軟體套件受限於每個套件 (`usr/share/doc/{package-name}` 或目錄) 的授權條款 `LICENSE` 。 使用套件之前，請先閱讀授權條款。 您的安裝和使用套件會構成您接受這些條款的規範。 如果您不同意授權條款，請不要使用該套件。

## <a name="install-a-container-engine"></a>安裝容器引擎

Azure IoT Edge 會依賴 OCI 相容的容器執行階段。 針對生產案例，建議使用 Moby 型引擎。 Moby 引擎是唯一 Azure IoT Edge 正式支援的容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="windows"></a>[Windows](#tab/windows)

針對生產案例，請使用安裝腳本中包含的 Moby 型引擎。 安裝引擎沒有額外的步驟。

針對 IoT Edge 與 Linux 容器，您需要提供您自己的容器執行時間。 在您的裝置上安裝 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) ，並將其設定為 [使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) ，然後再繼續。

---

## <a name="install-the-iot-edge-security-daemon"></a>安裝 IoT Edge security daemon

IoT Edge 的安全性背景程式可在 IoT Edge 裝置上提供及維護安全性標準。 此精靈會在每次開機時啟動，並藉由啟動 IoT Edge 執行階段讓裝置進入啟動程序。

這些章節中的步驟代表在具有網際網路連線的裝置上安裝最新版本的一般程式。 如果您需要安裝特定版本，例如發行前版本，或需要離線安裝，請遵循下一節中的 [離線或特定版本安裝](#offline-or-specific-version-installation) 步驟。

# <a name="linux"></a>[Linux](#tab/linux)

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

如果您想要安裝特定版本的安全性守護程式，請從 apt 清單輸出中指定版本。 也請為 **libiothsm-std** 封裝指定相同的版本，否則會安裝其最新版本。 例如，下列命令會安裝最新版本的1.0.8 版本：

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

如果未列出您想要安裝的版本，請遵循下一節中的 [離線或特定版本安裝](#offline-or-specific-version-installation) 步驟。 該章節將說明如何以任何舊版的 IoT Edge 安全性守護程式或發行候選版本為目標。

# <a name="windows"></a>[Windows](#tab/windows)

>[!TIP]
>針對 IoT 核心裝置，我們建議使用遠端 PowerShell 會話來執行安裝命令。 如需詳細資訊，請參閱 [使用適用于 Windows IoT 的 PowerShell](/windows/iot-core/connect-your-device/powershell)。

1. 以系統管理員身分執行 PowerShell。

   使用 PowerShell 的 AMD64 會話，而不是 PowerShell (x86) 。 如果您不確定所使用的是哪一種會話類型，請執行下列命令：

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 執行 [IoTEdge](reference-windows-scripts.md#deploy-iotedge) 命令，此命令會執行下列工作：

   * 檢查您的 Windows 電腦是否在支援的版本上。
   * 開啟 [容器] 功能。
   * 下載 moby 引擎和 IoT Edge 執行時間。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge`命令預設為使用 Windows 容器。 如果您想要使用 Linux 容器，請新增 `ContainerOs` 參數：

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. 此時，IoT 核心裝置可能會自動重新開機。 Windows 10 或 Windows Server 裝置可能會提示您重新開機。 若是如此，請立即重新開機您的裝置。

當您在裝置上安裝 IoT Edge 時，您可以使用其他參數來修改進程，包括：

* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向本機目錄以進行離線安裝。

如需這些額外參數的詳細資訊，請參閱 [Windows 上適用于 IoT Edge 的 PowerShell 腳本](reference-windows-scripts.md)。

---

既然容器引擎和 IoT Edge 執行時間已安裝在您的裝置上，您就可以開始進行下一個步驟，也就是向 IoT 中樞註冊您的裝置，並使用其雲端身分識別和驗證資訊來設定裝置。

根據您想要使用的驗證類型，選擇下一篇文章：

* 從[對稱金鑰驗證](how-to-manual-provision-symmetric-key.md)開始更快。
* 在生產案例中， [x.509 憑證驗證](how-to-manual-provision-x509.md)更安全。

## <a name="offline-or-specific-version-installation"></a>離線或特定版本安裝

本節中的步驟適用于標準安裝步驟未涵蓋的案例。 這可能包括：

* 離線時安裝 IoT Edge
* 安裝發行候選版本
* 在 Windows 上，安裝最新版本以外的版本

# <a name="linux"></a>[Linux](#tab/linux)

如果您想要安裝無法透過提供的特定 Azure IoT Edge 執行階段版本，請使用本節中的步驟 `apt-get install` 。 Microsoft 套件清單只包含一組有限的最新版本及其子版本，因此這些步驟適用于想要安裝較舊版本或發行候選版本的任何人。

使用捲曲命令，您可以直接從 IoT Edge GitHub 存放庫將元件檔案設為目標。

<!-- TODO: Offline installation? -->

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

# <a name="windows"></a>[Windows](#tab/windows)

在安裝期間，會下載三個檔案：

* PowerShell 腳本，其中包含安裝指示
* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 的安全 daemon (iotedged) 、Moby 容器引擎和 Moby CLI
* Visual C++ 可轉散發套件 (VC runtime) 安裝程式

如果您的裝置會在安裝期間離線，或您想要安裝特定版本的 IoT Edge，您可以事先將這些檔案下載到裝置。 當您要安裝時，請指向包含所下載檔案的目錄中的安裝腳本。 安裝程式會先檢查該目錄，然後才會下載找不到的元件。 如果所有檔案都可離線使用，您可以不使用網際網路連線來進行安裝。

1. 如需最新的 IoT Edge 安裝檔案及舊版，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

2. 尋找您想要安裝的版本，並從版本資訊的 [ **資產** ] 區段中，將下列檔案下載到您的 IoT 裝置：

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab 版本1.0.9 或更新版本，或從1.0.8 和較舊版本中的 Microsoft-Azure-IoTEdge.cab。

   基於測試目的，Microsoft-Azure-IotEdge-arm32.cab 也可以從1.0.9 開始使用。 Windows ARM32 裝置上目前不支援 IoT Edge。

   請務必使用與您所使用之 .cab 檔案相同版本的 PowerShell 腳本，因為功能會變更以支援每個版本中的功能。

3. 如果您下載的 .cab 檔案中有架構尾碼，請將檔案重新命名為只 **Microsoft-Azure-IoTEdge.cab**。

4. （選擇性）下載 Visual C++ 可轉散發套件的安裝程式。 例如，PowerShell 腳本會使用這個版本： [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe)。 將安裝程式儲存在 IoT 裝置上與 IoT Edge 檔案相同的資料夾中。

5. 若要安裝離線元件，請使用 [點來源](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) 作為 PowerShell 腳本的本機複本。 

6. 使用參數執行 [IoTEdge](reference-windows-scripts.md#deploy-iotedge) 命令 `-OfflineInstallationPath` 。 提供檔案目錄的絕對路徑。 例如，

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Deployment 命令將會使用在提供的本機檔案目錄中找到的任何元件。 如果 .cab 檔或 Visual C++ 安裝程式遺失，則會嘗試下載這些檔案。

---

既然容器引擎和 IoT Edge 執行時間已安裝在您的裝置上，您就可以開始進行下一個步驟，也就是 [在 IoT 中樞內驗證 IoT Edge 裝置](how-to-manual-provision-symmetric-key.md)。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從裝置移除 IoT Edge 安裝，請使用下列命令。

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="windows"></a>[Windows](#tab/windows)

如果您想要從 Windows 裝置移除 IoT Edge 安裝，請使用系統管理 PowerShell 視窗中的 [IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 命令。 此命令會移除 IoT Edge 執行階段，以及您現有的設定和 Moby 引擎資料。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

`Uninstall-IoTEdge`命令無法在 Windows IoT Core 上運作。 若要移除 IoT Edge，您需要重新部署 Windows IoT Core 映射。

如需卸載選項的詳細資訊，請使用命令 `Get-Help Uninstall-IoTEdge -full` 。

---

## <a name="next-steps"></a>後續步驟

安裝 IoT Edge 執行時間之後，請將您的裝置設定為與 IoT 中樞連線。 下列文章將逐步解說如何在雲端中註冊新的裝置，然後提供裝置的身分識別和驗證資訊。

根據您想要使用的驗證類型，選擇下一篇文章：

* **對稱金鑰**： IoT 中樞和 IoT Edge 裝置都有安全金鑰的複本。 當裝置連線到 IoT 中樞時，它們會檢查金鑰是否相符。 這種驗證方法會更快速地開始使用，但不是安全的。

  [使用對稱金鑰驗證設定 Azure IoT Edge 裝置](how-to-manual-provision-symmetric-key.md)

* **X.509 自我簽署**： IoT Edge 裝置具有 x.509 身分識別憑證，而 IoT 中樞則會取得憑證的指紋。 當裝置連線到 IoT 中樞時，它們會比較憑證與其指紋。 這種驗證方法較為安全，建議用於生產案例。

  [使用 x.509 憑證驗證設定 Azure IoT Edge 裝置](how-to-manual-provision-x509.md)