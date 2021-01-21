---
title: 安裝適用于 Windows 的 Azure IoT Edge |Microsoft Docs
description: 在 Windows 裝置上安裝適用于 Windows 容器的 Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: 7857f93e8c767f270041bb6bf041447786ce19ff
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633791"
---
# <a name="install-and-manage-azure-iot-edge-for-windows"></a>安裝和管理 Windows 的 Azure IoT Edge

適用于 Windows 的 Azure IoT Edge 直接在您的主機 Windows 裝置上執行，並使用 Windows 容器在邊緣執行商務邏輯。

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。 若要深入瞭解，請參閱 [瞭解 Azure IoT Edge 執行時間及其架構](iot-edge-runtime.md)。

>[!NOTE]
>從 Azure IoT Edge 的版本1.2.0 開始，將不支援適用于 Windows 的 Azure IoT Edge。
>
>請考慮使用新的方法，在 Windows 裝置上執行 IoT Edge，Azure IoT Edge 適用于 Windows 上的 Linux。

<!-- TODO: link to EFLOW-->

設定 IoT Edge 裝置有兩個步驟。 第一個步驟是安裝執行時間及其相依性。 第二個步驟是將裝置連線至雲端中的身分識別，並設定 IoT 中樞的驗證。

本文列出在 Windows 裝置上安裝 Azure IoT Edge 執行時間的步驟。 當您安裝執行時間時，您可以選擇使用 Linux 容器或 Windows 容器。 目前，在實際執行案例中只支援 Windows 上的 Windows 容器。 Windows 上的 Linux 容器適用于開發和測試案例，特別是如果您是在 Windows 電腦上進行開發，以便部署到 Linux 裝置。

## <a name="prerequisites"></a>必要條件

* Windows 裝置

  使用 Windows 容器 IoT Edge 需要 Windows 版本 1809/組建17762，這是最新的 [windows 長期支援組建](/windows/release-information/)。 針對開發和測試案例，支援容器功能的任何 SKU (Pro、Enterprise、Server 等 ) 都可運作。 不過，在進入生產環境之前，請務必先檢查 [支援的系統清單](support.md#operating-systems) 。

  使用 Linux 容器的 IoT Edge 可以在任何符合 [Docker Desktop 需求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的 Windows 版本上執行。

* 裝置上的容器支援

  Azure IoT Edge 依賴 [OCI 相容](https://www.opencontainers.org/) 的容器引擎。 請確定您的裝置可以支援容器。

  如果您要在虛擬機器上安裝 IoT Edge，請啟用嵌套虛擬化，並配置至少 2 GB 的記憶體。 針對 Hyper-v，第2代虛擬機器預設會啟用嵌套虛擬化。 針對 VMware，有一個切換功能可以在您的虛擬機器上啟用此功能。

  如果您要在 IoT 核心裝置上安裝 IoT Edge，請在 [遠端 PowerShell 會話](/windows/iot-core/connect-your-device/powershell) 中使用下列命令，以檢查您的裝置是否支援 Windows 容器：

  ```powershell
  Get-Service vmcompute
  ```

* [已註冊的裝置識別碼](how-to-register-device.md)

  如果您已向對稱金鑰驗證註冊裝置，請備妥裝置連接字串。

  如果您已向 x.509 自我簽署憑證驗證註冊您的裝置，則至少要有一個身分識別憑證可供您用來註冊裝置及其相符的私人私密金鑰，才能在您的裝置上使用。

## <a name="install-a-container-engine"></a>安裝容器引擎

Azure IoT Edge 會依賴 OCI 相容的容器執行階段。 針對生產案例，建議使用 Moby 型引擎。 Moby 引擎是唯一 Azure IoT Edge 正式支援的容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

針對生產案例，請使用安裝腳本中包含的 Moby 型引擎。 安裝引擎沒有額外的步驟。

針對 IoT Edge 與 Linux 容器，您需要提供您自己的容器執行時間。 在您的裝置上安裝 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) ，並將其設定為 [使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) ，然後再繼續。

## <a name="install-the-iot-edge-security-daemon"></a>安裝 IoT Edge security daemon

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

## <a name="provision-the-device-with-its-cloud-identity"></a>使用雲端身分識別來布建裝置

既然容器引擎和 IoT Edge 執行時間已安裝在您的裝置上，您就可以開始進行下一個步驟，也就是設定裝置的雲端身分識別和驗證資訊。

根據您想要使用的驗證類型，選擇下一節：

* [選項1：使用對稱金鑰進行驗證](#option-1-authenticate-with-symmetric-keys)
* [選項2：使用 x.509 憑證進行驗證](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>選項1：使用對稱金鑰進行驗證

此時，IoT Edge 執行時間會安裝在您的 Windows 裝置上，而您必須使用其雲端身分識別和驗證資訊來布建裝置。

本節將逐步解說使用對稱金鑰驗證布建裝置的步驟。 您應該已在 IoT 中樞註冊您的裝置，並從裝置資訊中取出連接字串。 如果沒有，請遵循在 [IoT 中樞註冊 IoT Edge 裝置](how-to-register-device.md)中的步驟。

1. 在 IoT Edge 裝置上，以系統管理員身分執行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令，在您的電腦上設定 IoT Edge 執行時間。 此命令預設為 Windows 容器的手動佈建。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * 如果您使用 Linux 容器，請將 `-ContainerOs` 參數新增至旗標。 與您先前執行的命令所選擇的容器選項一致 `Deploy-IoTEdge` 。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * 如果您將 IoTEdgeSecurityDaemon.ps1 腳本下載到您的裝置上以進行離線或特定版本安裝，請務必參考腳本的本機複本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 出現提示時，請提供您在上一節中取出的裝置連接字串。 裝置連接字串會將實體裝置與 IoT 中樞內的裝置識別碼產生關聯，並提供驗證資訊。

   裝置連接字串採用下列格式，且不應包含引號： `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

當您手動布建裝置時，您可以使用其他參數來修改進程，包括：

* 將流量導向經過 Proxy 伺服器
* 宣告特定的 edgeAgent 容器映射，並在私人登錄中提供認證

如需這些額外參數的詳細資訊，請參閱 [Windows 上適用于 IoT Edge 的 PowerShell 腳本](reference-windows-scripts.md)。

### <a name="option-2-authenticate-with-x509-certificates"></a>選項2：使用 x.509 憑證進行驗證

此時，IoT Edge 執行時間會安裝在您的 Windows 裝置上，而您必須使用其雲端身分識別和驗證資訊來布建裝置。

本節將逐步解說使用 x.509 憑證驗證布建裝置的步驟。 您應該已在 IoT 中樞註冊您的裝置，並提供符合您 IoT Edge 裝置上的憑證和私密金鑰的指紋。 如果沒有，請遵循在 [IoT 中樞註冊 IoT Edge 裝置](how-to-register-device.md)中的步驟。

1. 在 IoT Edge 裝置上，以系統管理員身分執行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令，在您的電腦上設定 IoT Edge 執行時間。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * 如果您使用 Linux 容器，請將 `-ContainerOs` 參數新增至旗標。 與您先前執行的命令所選擇的容器選項一致 `Deploy-IoTEdge` 。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * 如果您將 IoTEdgeSecurityDaemon.ps1 腳本下載到您的裝置上以進行離線或特定版本安裝，請務必參考腳本的本機複本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 出現提示時，請提供下列資訊：

   * **IotHubHostName**：裝置將連接的 IoT 中樞主機名稱。 例如： `{IoT hub name}.azure-devices.net` 。
   * **DeviceId**：您在註冊裝置時所提供的識別碼。
   * **X509IdentityCertificate**：裝置上身分識別憑證的絕對路徑。 例如： `C:\path\identity_certificate.pem` 。
   * **X509IdentityPrivateKey**：所提供身分識別憑證之私密金鑰檔案的絕對路徑。 例如： `C:\path\identity_key.pem` 。

當您手動布建裝置時，您可以使用其他參數來修改進程，包括：

* 將流量導向經過 Proxy 伺服器
* 宣告特定的 edgeAgent 容器映射，並在私人登錄中提供認證

如需這些額外參數的詳細資訊，請參閱 [Windows 上適用于 IoT Edge 的 PowerShell 腳本](reference-windows-scripts.md)。

## <a name="offline-or-specific-version-installation-optional"></a>離線或特定版本安裝 (選用) 

本節中的步驟適用于標準安裝步驟未涵蓋的案例。 這可能包括：

* 離線時安裝 IoT Edge
* 安裝發行候選版本
* 安裝最新版本以外的版本

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

## <a name="update-iot-edge"></a>更新 IoT Edge

使用 `Update-IoTEdge` 命令來更新安全性守護程式。 腳本會自動提取最新版本的安全 daemon。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

執行 Update-IoTEdge 命令會從您的裝置移除並更新安全性守護程式，以及兩個執行時間容器映射。 Yaml 檔案會保留在裝置上，以及 Moby 容器引擎的資料 (如果您是使用 Windows 容器) 。 保留設定資訊表示您不需要在更新程式期間，再次提供裝置的連接字串或裝置布建服務資訊。

如果您想要更新為特定版本的安全 daemon，請從 [IoT Edge](https://github.com/Azure/azure-iotedge/releases)版本中尋找您想要作為目標的版本。 在該版本中，下載 **Microsoft-Azure-IoTEdge.cab** 檔案。 然後，使用 `-OfflineInstallationPath` 參數指向本機檔案位置。 例如：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath`參數會在提供的目錄中尋找名為 **Microsoft-Azure-IoTEdge.cab** 的檔案。 從 IoT Edge 版本 1.0.9-rc4 開始，有兩個 .cab 檔案可供使用，一個用於 AMD64 裝置，另一個用於 ARM32。 為您的裝置下載正確的檔案，然後將檔案重新命名以移除架構尾碼。

如果您想要離線更新裝置，請從 [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)版本中尋找您想要的目標版本。 在該版本中，下載 *IoTEdgeSecurityDaemon.ps1* 並 *Microsoft-Azure-IoTEdge.cab* 檔案。 請務必使用與您所使用之 .cab 檔案相同版本的 PowerShell 腳本，因為功能會變更以支援每個版本中的功能。

如果您下載的 .cab 檔案中有架構尾碼，請將檔案重新命名為只 **Microsoft-Azure-IoTEdge.cab**。

若要使用離線元件進行更新，請使用 [點來源](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) 作為 PowerShell 腳本的本機複本。 然後，使用 `-OfflineInstallationPath` 參數做為命令的一部分， `Update-IoTEdge` 並提供檔案目錄的絕對路徑。 例如，

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

如需更新選項的詳細資訊，請使用命令 `Get-Help Update-IoTEdge -full` 或參考 [Windows 上 IoT Edge 的 PowerShell 腳本](reference-windows-scripts.md)。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從裝置移除 IoT Edge 安裝，請使用下列命令。

如果您想要從 Windows 裝置移除 IoT Edge 安裝，請使用系統管理 PowerShell 視窗中的 [IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 命令。 此命令會移除 IoT Edge 執行階段，以及您現有的設定和 Moby 引擎資料。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

`Uninstall-IoTEdge`命令無法在 Windows IoT Core 上運作。 若要移除 IoT Edge，您需要重新部署 Windows IoT Core 映射。

如需卸載選項的詳細資訊，請使用命令 `Get-Help Uninstall-IoTEdge -full` 。

## <a name="next-steps"></a>下一步

繼續 [部署 IoT Edge 課程模組](how-to-deploy-modules-portal.md) ，以瞭解如何將模組部署到您的裝置。
