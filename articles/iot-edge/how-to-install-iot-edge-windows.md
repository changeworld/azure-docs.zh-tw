---
title: 在 Windows 上安裝 Azure IoT Edge | Microsoft Docs
description: 在 Windows 10、Windows Server 和 Windows IoT 核心版上安裝 Azure IoT Edge 的指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: kgremban
ms.openlocfilehash: 80ce962ac6977fcce2455c8e2ef29af448a44075
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133149"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。

若要深入了解 IoT Edge 執行階段，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出了使用 Windows 容器在 Windows x64 （AMD/英特爾） 系統上安裝 Azure IoT 邊緣運行時的步驟。

> [!NOTE]
> 已知的 Windows 作業系統問題可防止在 IoT Edge 模組（進程隔離的 Windows Nano Server 容器）運行時過渡到睡眠和休眠電源狀態。 此問題會影響設備上的電池壽命。
>
> 作為解決方法，在使用這些電源狀態之前`Stop-Service iotedge`，使用 命令停止任何正在運行的 IoT Edge 模組。

就 Azure IoT Edge 而言，在 Windows 系統上使用 Linux 容器不是建議或支援的生產環境設定。 不過，這很適合用於開發與測試用途。 要瞭解更多資訊，請參閱[在 Windows 上使用 IoT 邊緣來運行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

有關最新版本 IoT Edge 中包含的內容的資訊，請參閱[Azure IoT 邊緣版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>Prerequisites

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。

### <a name="supported-windows-versions"></a>支援的 Windows 版本

Windows 的 IoT 邊緣需要 Windows 版本 1809/內部 17763，這是最新的[Windows 長期支援版本](https://docs.microsoft.com/windows/release-information/)。 有關 Windows SKU 支援，請參閱根據是準備生產方案或開發和測試方案支援的內容：

* **生產**：有關當前支援哪些作業系統用於生產方案的最新資訊，請參閱[Azure IoT Edge 支援的系統](support.md#operating-systems)。
* **開發和測試**：對於開發和測試方案，可以使用 Windows 容器的 Azure IoT Edge 安裝在支援容器功能的任何版本的 Windows 10 或 Windows Server 2019 上。

IoT 核心設備必須包括 IoT 核心 Windows 容器可選功能，以支援 IoT 邊緣運行時。 在[遠端 PowerShell 會話](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)中使用以下命令來檢查設備上支援 Windows 容器：

```powershell
Get-Service vmcompute
```

如果服務存在，則應獲得成功的回應，服務狀態列為**正在運行**。 如果未找到`vmcompute`該服務，則您的設備不符合 IoT Edge 的要求。 請與您的硬體供應商聯繫，詢問有關此功能的支援。

### <a name="prepare-for-a-container-engine"></a>準備容器引擎

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 對於生產案例，請使用安裝指令碼中包含的 Moby 引擎，在 Windows 裝置上執行 Windows 容器。

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。

>[!TIP]
>對於 IoT Core 設備，我們建議使用 RemotePowerShell 會話運行安裝命令。 有關詳細資訊，請參閱對[Windows IoT 使用 PowerShell。](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 可以使用 IoT 中心提供的設備連接字串手動預配單個 IoT Edge 設備。 或者，您可以使用設備預配服務 （DPS） 自動預配設備，這在您要設置許多設備時非常有用。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。

下列各節說明新裝置上 IoT Edge 安裝指令碼的常見使用案例和參數。

### <a name="option-1-install-and-manually-provision"></a>選項 1：安裝並手動佈建

在此選項中，提供 IoT 中心生成的**設備連接字串**以預配設備。

此示例演示了使用 Windows 容器進行手動安裝：

1. 如果尚未註冊新的 IoT Edge 設備並檢索**設備連接字串**。 複製連接字串以在本部分的後面部分使用。 您可以使用以下工具完成此步驟：

   * [Azure 門戶](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [視覺工作室代碼](how-to-register-device.md#register-with-visual-studio-code)

2. 以系統管理員身分執行 PowerShell。

   >[!NOTE]
   >使用 PowerShell 的 AMD64 工作階段來安裝 IoT Edge，而不是使用 PowerShell (x86)。 如果您不確定正在使用的工作階段類型，請執行下列命令：
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Deploy-IoTEdge**命令檢查 Windows 電腦是否位於受支援的版本上，打開容器功能，然後下載 moby 運行時和 IoT Edge 運行時。 該命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 此時，IoT Core 設備可能會自動重新開機。 其他 Windows 10 或 Windows 伺服器設備可能會提示您重新開機。 如果是，請立即重新開機設備。 設備準備就緒後，再次以管理員身份運行 PowerShell。

5. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 此命令預設為 Windows 容器的手動佈建。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 當出現提示時，提供在步驟 1 中檢索的設備連接字串。 設備連接字串將物理設備與 IoT 中心中的裝置識別碼 關聯。

   設備連接字串採用以下格式，不應包含引號：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 使用["驗證成功安裝](#verify-successful-installation)"中的步驟檢查設備上的 IoT 邊緣狀態。

當您手動安裝及佈建裝置時，可以使用其他參數修改安裝，包括：

* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

有關這些安裝選項的詳細資訊，請先跳一下以瞭解[所有安裝參數](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>選項 2：安裝並自動佈建

在第二個選項中，使用 IoT 中樞裝置佈建服務來佈建裝置。 提供設備預配服務實例**中的範圍 ID**以及特定于首選[證明機制](../iot-dps/concepts-security.md#attestation-mechanism)的任何其他資訊：

* [在 Windows 上使用虛擬 TPM 創建和預配類比 IoT 邊緣設備](how-to-auto-provision-simulated-device-windows.md)
* [使用 X.509 憑證創建和預配類比 IoT 邊緣設備](how-to-auto-provision-x509-certs.md)
* [使用對稱金鑰認證創建和預配 IoT 邊緣設備](how-to-auto-provision-symmetric-keys.md)

自動安裝和預配設備時，可以使用其他參數修改安裝，包括：

* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

如需這些安裝選項的詳細資訊，請繼續閱讀本文，或跳過以了解[所有安裝參數](#all-installation-parameters)。

## <a name="offline-or-specific-version-installation"></a>離線或特定版本安裝

在安裝過程中下載兩個檔：

* Microsoft Azure IoT Edge 駕駛室，其中包含 IoT 邊緣安全守護程式（ioedge）、Moby 容器引擎和 Moby CLI。
* 可視C++可再發行包 （VC 運行時） MSI

如果設備在安裝期間處於離線狀態，或者要安裝特定版本的 IoT Edge，則可以提前將其中一個或兩個檔下載到設備。 安裝時，將安裝腳本指向包含下載檔案的目錄。 安裝程式首先檢查該目錄，然後僅下載未找到的元件。 如果所有檔都離線可用，則可以在沒有互聯網連接時進行安裝。

有關最新的 IoT 邊緣安裝檔以及以前的版本，請參閱[Azure IoT 邊緣版本](https://github.com/Azure/azure-iotedge/releases)。

要使用離線元件進行安裝，請將`-OfflineInstallationPath`參數用作 Deploy-IoTEdge 命令的一部分，並提供檔目錄的絕對路徑。 例如，

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>該`-OfflineInstallationPath`參數在提供的目錄中查找名為**Microsoft-Azure-IoTEdge.cab**的檔。 從 IoT Edge 版本 1.0.9-rc4 開始，有兩個 .cab 檔可供使用，一個用於 AMD64 設備，一個用於 ARM32。 下載設備的正確檔，然後重命名該檔以刪除體系結構尾碼。

該`Deploy-IoTEdge`命令安裝 IoT Edge 元件，然後需要繼續執行`Initialize-IoTEdge`命令以為其 IoT 中心裝置識別碼 和連接預配設備。 直接運行該命令並提供來自 IoT 中心的連接字串，或者使用上一節中的連結之一瞭解如何使用設備預配服務自動預配設備。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

您還可以使用離線安裝路徑參數與更新 IoTEdge 命令一起。

## <a name="verify-successful-installation"></a>確認安裝成功

檢查 IoT Edge 服務的狀態。 應將其列為正在運行。  

```powershell
Get-Service iotedge
```

檢查最後 5 分鐘的服務記錄。 如果剛剛安裝完 IoT Edge 運行時，您可能會看到從運行**Deploy-IoTEdge**和**初始化-IoTEdge**之間的時間錯誤清單。 這些錯誤是預料之中的，因為服務在配置之前正在嘗試啟動。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

運行自動檢查，檢查最常見的配置和網路錯誤。

```powershell
iotedge check
```

列出執行中的模組。 新安裝後，您應該看到運行的唯一模組是**邊緣代理**。 首次[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)後，其他系統模組**EdgeHub**也將在設備上啟動。

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>管理模組容器

IoT Edge 服務需要在設備上運行的容器引擎。 將模組部署到設備時，IoT Edge 運行時使用容器引擎從雲中的註冊表中提取容器映射。 IoT Edge 服務使您能夠與模組進行交互並檢索日誌，但有時您可能希望使用容器引擎與容器本身進行交互。

有關模組概念的詳細資訊，請參閱[瞭解 Azure IoT 邊緣模組](iot-edge-modules.md)。

如果在 Windows IoT 邊緣設備上運行 Windows 容器，則 IoT Edge 安裝包括 Moby 容器引擎。 Moby 引擎基於與 Docker 相同的標準，設計為與 Docker Desktop 在同一台電腦上並行運行。 因此，如果要定位由 Moby 引擎管理的容器，您必須專門針對該引擎而不是 Docker。

例如，要列出所有 Docker 映射，請使用以下命令：

```powershell
docker images
```

要列出所有 Moby 圖像，請使用指向 Moby 引擎的指標修改同一命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 列在安裝腳本的輸出中，或者您可以在 config.yaml 檔的容器運行時設置部分找到它。

![moby_runtime uri 在 config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

有關可用於與設備上運行的容器和映射交互的命令的詳細資訊，請參閱[Docker 命令列介面](https://docs.docker.com/engine/reference/commandline/docker/)。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Windows 裝置中移除 IoT Edge 安裝，請從管理 PowerShell 視窗中使用下列命令。 此命令會移除 IoT Edge 執行階段，以及您現有的設定和 Moby 引擎資料。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

卸載 IoTEdge 命令在 Windows IoT 酷睿上不起作用。 要從 Windows IoT 核心設備中刪除 IoT 邊緣，需要重新部署 Windows IoT 核心映射。

有關卸載選項的詳細資訊，請使用 命令`Get-Help Uninstall-IoTEdge -full`。

## <a name="verify-installation-script"></a>驗證安裝腳本

本文中提供的安裝命令使用 Invoke-WebRequest Cmdlet 從`aka.ms/iotedge-win`請求安裝腳本。 此連結指向最近`IoTEdgeSecurityDaemon.ps1`[IoT 邊緣版本中](https://github.com/Azure/azure-iotedge/releases)的腳本。 您還可以從特定版本下載此腳本或腳本版本，以在 IoT Edge 設備上運行安裝命令。

對提供的腳本進行簽名以提高安全性。 您可以通過將腳本下載到設備，然後運行以下 PowerShell 命令來驗證簽名：

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

如果驗證了簽名，輸出狀態**為"有效**"。

## <a name="all-installation-parameters"></a>所有安裝參數

前幾節介紹常見的安裝案例，示範如何使用參數來修改安裝指令碼。 本節提供用於安裝、更新或卸載 IoT Edge 的常見參數的參考表。

### <a name="deploy-iotedge"></a>部署-IoTEdge

部署 IoTEdge 命令下載並部署 IoT 邊緣安全守護進程及其依賴項。 部署命令接受這些常見參數，等等。 對於完整清單，請使用 命令`Get-Help Deploy-IoTEdge -full`。  

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **窗戶**或**Linux** | 如果未指定容器作業系統，則 Windows 是預設值。<br><br>對於 Windows 容器，IoT Edge 使用安裝中包含的 moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式將檢查安裝所需的 IoT Edge 駕駛室和 VC 運行時 MSI 檔列出的目錄。 下載目錄中找不到的任何檔。 如果兩個檔都在目錄中，則可以在沒有互聯網連接的情況下安裝 IoT Edge。 您還可以使用此參數使用特定版本。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **如果需要，請重新開機** | 無 | 此標誌允許部署腳本重新開機電腦，而無需提示（如有必要）。 |

### <a name="initialize-iotedge"></a>初始化-IoTEdge

初始化-IoTEdge 命令使用設備連接字串和操作詳細資訊配置 IoT Edge。 然後，此命令生成的大部分資訊存儲在 iotedge_config.yaml 檔中。 初始化命令接受這些常見參數，等等。 對於完整清單，請使用 命令`Get-Help Initialize-IoTEdge -full`。

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **手動** | None | **切換參數**. 如果未指定預配類型，則手動為預設值。<br><br>宣告您將提供裝置連接字串以手動佈建裝置 |
| **Dps** | None | **切換參數**. 如果未指定預配類型，則手動為預設值。<br><br>宣告您將提供裝置佈建服務 (DPS) 的範圍識別碼，以及透過 DPS 佈建的裝置註冊識別碼。  |
| **設備連接字串** | 此連接字串來自 IoT 中樞註冊的 IoT Edge 裝置，以單引號括住 | **手動預配所需的**。 如果在腳本參數中未提供連接字串，系統將提示您輸入連接字串。 |
| **ScopeId** | 此範圍識別碼來自與您 IoT 中樞相關聯之裝置佈建服務的執行個體。 | DPS 預配**是必需的**。 如果在腳本參數中未提供作用域 ID，系統將提示您輸入一個作用域 ID。 |
| **註冊 Id** | 由您裝置產生的註冊識別碼 | 如果使用 TPM 或對稱金鑰證明，**則需要**DPS 預配。 如果使用 X.509 憑證證明，**則可選**。 |
| **X509身份證書** | 設備上 X.509 設備標識證書的 URI 路徑。 | 如果使用 X.509 憑證證明，**則需要**DPS 預配。 |
| **X509身份私密金鑰** | 設備上 X.509 設備標識證書金鑰的 URI 路徑。 | 如果使用 X.509 憑證證明，**則需要**DPS 預配。 |
| **SymmetricKey** | 使用 DPS 時用於預配 IoT Edge 設備標識的對稱鍵 | 如果使用對稱金鑰證明，**則需要**DPS 預配。 |
| **ContainerOs** | **窗戶**或**Linux** | 如果未指定容器作業系統，則 Windows 是預設值。<br><br>對於 Windows 容器，IoT Edge 使用安裝中包含的 moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理程式映像 URI | 根據預設，新的 IoT Edge 安裝會為 IoT Edge 代理程式映像使用最新的輪替標籤。 使用此參數，為映像版本設定特定標籤，或提供您自己的代理程式映像。 如需詳細資訊，請參閱[了解 IoT Edge 標籤](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **使用者** | 容器登錄使用者名稱 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的使用者名稱。 |
| **密碼** | 安全密碼字串 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的密碼。 |

### <a name="update-iotedge"></a>更新-物聯網邊緣

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **窗戶**或**Linux** | 如果未指定容器作業系統，則 Windows 是預設值。 對於 Windows 容器，安裝中將包含容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式將檢查安裝所需的 IoT Edge 駕駛室和 VC 運行時 MSI 檔列出的目錄。 下載目錄中找不到的任何檔。 如果兩個檔都在目錄中，則可以在沒有互聯網連接的情況下安裝 IoT Edge。 您還可以使用此參數使用特定版本。 |
| **如果需要，請重新開機** | 無 | 此標誌允許部署腳本重新開機電腦，而無需提示（如有必要）。 |

### <a name="uninstall-iotedge"></a>卸載 IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **力** | 無 | 此標誌強制卸載，以防上次卸載嘗試失敗。
| **如果需要，請重新開機** | 無 | 如有必要，此標誌允許卸載腳本重新開機電腦，而無需提示。 |

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
