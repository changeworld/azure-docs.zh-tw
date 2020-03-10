---
title: 在 Windows 上安裝 Azure IoT Edge | Microsoft Docs
description: 在 Windows 10、Windows Server 和 Windows IoT 核心版上安裝 Azure IoT Edge 的指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: e3f55f9be28a8b53f012e111e43ba1f495b1d585
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395704"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。

若要深入了解 IoT Edge 執行階段，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出使用 Windows 容器在 Windows x64 （AMD/Intel）系統上安裝 Azure IoT Edge 執行時間的步驟。

> [!NOTE]
> 當 IoT Edge 模組（進程隔離的 Windows Nano Server 容器）正在執行時，已知的 Windows 作業系統問題會防止轉換成睡眠和休眠電源狀態。 此問題會影響裝置上的電池壽命。
>
> 因應措施是使用命令 `Stop-Service iotedge`，在使用這些電源狀態之前，先停止所有執行中的 IoT Edge 模組。

就 Azure IoT Edge 而言，在 Windows 系統上使用 Linux 容器不是建議或支援的生產環境設定。 不過，這很適合用於開發與測試用途。 若要深入瞭解，請參閱[在 Windows 上使用 IoT Edge 執行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

如需最新 IoT Edge 版本中所包含內容的詳細資訊，請參閱[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>Prerequisites

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。

### <a name="supported-windows-versions"></a>支援的 Windows 版本

在開發和測試案例中，可以在支援 [容器] 功能的任何 Windows 10 或 Windows Server 2019 （組建17763）版本上安裝 Windows 容器 Azure IoT Edge。 如需實際執行案例目前支援哪些作業系統的詳細資訊，請參閱[Azure IoT Edge 支援的系統](support.md#operating-systems)。

IoT 核心版裝置必須包含 IoT 核心-Windows 容器選用功能，以支援 IoT Edge 執行時間。 在[遠端 PowerShell 會話](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)中使用下列命令，以檢查裝置上是否支援 Windows 容器：

```powershell
Get-Service vmcompute
```

如果服務存在，您應該會收到成功的回應，且服務**狀態列為 [** 執行中]。 如果找不到 vmcompute 服務，則您的裝置不符合 IoT Edge 的需求。 請洽詢您的硬體提供者，詢問這項功能的支援。

### <a name="prepare-for-a-container-engine"></a>準備容器引擎

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 對於生產案例，請使用安裝指令碼中包含的 Moby 引擎，在 Windows 裝置上執行 Windows 容器。

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。

>[!TIP]
>針對 IoT 核心版裝置，建議使用 RemotePowerShell 會話來執行安裝命令。 如需詳細資訊，請參閱[使用適用于 Windows IoT 的 PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)。

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 您可以使用 IoT 中樞提供的裝置連接字串，手動布建單一 IoT Edge 裝置。 或者，您可以使用裝置布建服務（DPS）自動布建裝置，這在您有許多裝置要設定時很有説明。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。

下列各節說明新裝置上 IoT Edge 安裝指令碼的常見使用案例和參數。

### <a name="option-1-install-and-manually-provision"></a>選項 1：安裝並手動佈建

在第一個選項中，您會提供 IoT 中樞所產生的**裝置連接字串**來布建裝置。

這個範例示範如何使用 Windows 容器進行手動安裝：

1. 如果您還沒有這麼做，請註冊新的 IoT Edge 裝置，並取出**裝置連接字串**。 複製連接字串，以供本節稍後使用。 您可以使用下列工具來完成此步驟：

   * [Azure 入口網站](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. 以系統管理員身分執行 PowerShell。

   >[!NOTE]
   >使用 PowerShell 的 AMD64 工作階段來安裝 IoT Edge，而不是使用 PowerShell (x86)。 如果您不確定正在使用的工作階段類型，請執行下列命令：
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **IoTEdge**命令會檢查您的 Windows 電腦是否在支援的版本上，開啟 [容器] 功能，然後下載 moby 執行時間和 IoT Edge 執行時間。 命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 此時，IoT 核心版裝置可能會自動重新開機。 其他 Windows 10 或 Windows Server 裝置可能會提示您重新開機。 若是如此，請立即重新開機您的裝置。 一旦您的裝置準備就緒，請再次以系統管理員身分執行 PowerShell。

5. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 此命令預設為 Windows 容器的手動佈建。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 出現提示時，提供您在步驟1中取得的裝置連接字串。 裝置連接字串會將實體裝置與 IoT 中樞中的裝置識別碼產生關聯。

   裝置連接字串採用下列格式，且不應包含引號： `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 使用[驗證成功安裝](#verify-successful-installation)中的步驟來檢查裝置上的 IoT Edge 狀態。

當您手動安裝及佈建裝置時，可以使用其他參數修改安裝，包括：

* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

如需這些安裝選項的詳細資訊，請直接跳至瞭解[所有安裝參數](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>選項 2：安裝並自動佈建

在第二個選項中，使用 IoT 中樞裝置佈建服務來佈建裝置。 提供來自裝置布建服務實例的**範圍識別碼**，以及您慣用[證明機制](../iot-dps/concepts-security.md#attestation-mechanism)特定的任何其他資訊：

* [在 Windows 上使用虛擬 TPM 建立及布建模擬的 IoT Edge 裝置](how-to-auto-provision-simulated-device-windows.md)
* [使用對稱金鑰證明建立和布建 IoT Edge 裝置](how-to-auto-provision-symmetric-keys.md)

當您自動安裝和布建裝置時，您可以使用其他參數來修改安裝，包括：

* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

如需這些安裝選項的詳細資訊，請繼續閱讀本文，或跳過以了解[所有安裝參數](#all-installation-parameters)。

## <a name="offline-or-specific-version-installation"></a>離線或特定版本安裝

在安裝期間，會下載兩個檔案：

* Microsoft Azure IoT Edge cab，其中包含 IoT Edge security daemon （iot）、Moby 容器引擎和 Moby CLI。
* Visual C++可轉散發套件（VC RUNTIME） MSI

如果您的裝置在安裝期間將會離線，或如果您想要安裝特定版本的 IoT Edge，您可以提早將其中一或兩個檔案下載到裝置。 當您安裝時，請將安裝腳本指向包含下載檔案的目錄。 安裝程式會先檢查該目錄，然後才會下載找不到的元件。 如果所有檔案都可離線使用，您可以在沒有網際網路連線的情況下安裝。

如需最新的 IoT Edge 安裝檔案和舊版，請參閱[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

若要使用離線元件安裝，請使用 `-OfflineInstallationPath` 參數做為 IoTEdge 命令的一部分，並提供檔案目錄的絕對路徑。 例如，

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>`-OfflineInstallationPath` 參數會在提供的目錄中尋找名為**Microsoft-Azure-IoTEdge**的檔案。 從 IoT Edge 版本 1.0.9-rc4 開始，有兩個 .cab 檔案可供使用，一個用於 AMD64 裝置，另一個用於 ARM32。 請為您的裝置下載正確的檔案，然後將檔案重新命名，以移除架構尾碼。

`Deploy-IoTEdge` 命令會安裝 IoT Edge 元件，然後您需要繼續進行 `Initialize-IoTEdge` 命令，以使用其 IoT 中樞裝置識別碼和連線來布建裝置。 請直接執行命令，並從 IoT 中樞提供連接字串，或使用上一節中的其中一個連結來瞭解如何自動布建裝置與裝置布建服務。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

您也可以使用離線安裝路徑參數搭配 IoTEdge 命令。

## <a name="verify-successful-installation"></a>確認安裝成功

檢查 IoT Edge 服務的狀態。 它應該會列為 [執行中]。  

```powershell
Get-Service iotedge
```

檢查最後 5 分鐘的服務記錄。 如果您剛完成安裝 IoT Edge 執行時間，可能會在執行**IoTEdge**和**Initialize-IoTEdge**之間看到一份錯誤清單。 這是預期的錯誤，因為服務會在設定之前嘗試啟動。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

執行自動檢查最常見的設定和網路錯誤。

```powershell
iotedge check
```

列出執行中的模組。 在新的安裝之後，您應該會看到執行的唯一模組是**edgeAgent**。 當您第一次[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)之後，另一個系統模組**edgeHub**也會在裝置上啟動。

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>管理模組容器

IoT Edge 服務需要在您的裝置上執行的容器引擎。 當您將模組部署至裝置時，IoT Edge 執行時間會使用容器引擎從雲端中的登錄提取容器映射。 IoT Edge 服務可讓您與模組互動並抓取記錄，但有時您可能會想要使用容器引擎來與容器本身互動。

如需模組概念的詳細資訊，請參閱[瞭解 Azure IoT Edge 模組](iot-edge-modules.md)。

如果您是在 Windows IoT Edge 裝置上執行 Windows 容器，則 IoT Edge 安裝會包含 Moby 容器引擎。 Moby 引擎是以與 Docker 相同的標準為基礎，其設計是在與 Docker Desktop 相同的電腦上平行執行。 基於這個理由，如果您想要將受 Moby 引擎管理的容器作為目標，就必須特別以該引擎為目標，而不是使用 Docker。

例如，若要列出所有 Docker 映射，請使用下列命令：

```powershell
docker images
```

若要列出所有 Moby 的映射，請使用 Moby 引擎的指標來修改相同的命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 會列在安裝腳本的輸出中，或者您可以在 yaml 檔案的 [容器執行時間設定] 區段中找到它。

![yaml 中的 moby_runtime uri](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

如需有關您可以用來與裝置上執行的容器和映射進行互動之命令的詳細資訊，請參閱[Docker 命令列介面](https://docs.docker.com/engine/reference/commandline/docker/)。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Windows 裝置中移除 IoT Edge 安裝，請從管理 PowerShell 視窗中使用下列命令。 此命令會移除 IoT Edge 執行階段，以及您現有的設定和 Moby 引擎資料。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

IoTEdge 命令不適用於 Windows IoT 核心版。 若要從 Windows IoT 核心版裝置移除 IoT Edge，您需要重新部署您的 Windows IoT 核心版映射。

如需卸載選項的詳細資訊，請使用命令 `Get-Help Uninstall-IoTEdge -full`。

## <a name="all-installation-parameters"></a>所有安裝參數

前幾節介紹常見的安裝案例，示範如何使用參數來修改安裝指令碼。 本節提供用來安裝、更新或卸載 IoT Edge 之一般參數的參考資料表。

### <a name="deploy-iotedge"></a>部署-IoTEdge

IoTEdge 命令會下載並部署 IoT Edge 安全性守護程式及其相依性。 [部署] 命令會接受這些一般參數，還有其他參數。 如需完整清單，請使用命令 `Get-Help Deploy-IoTEdge -full`。  

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器作業系統，Windows 就是預設值。<br><br>針對 Windows 容器，IoT Edge 會使用安裝中所包含的 moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式會檢查列出的目錄中是否有安裝所需的 IoT Edge cab 和 VC Runtime MSI 檔案。 在目錄中找不到的任何檔案都會下載。 如果這兩個檔案都在目錄中，您可以在沒有網際網路連線的情況下安裝 IoT Edge。 您也可以使用此參數來使用特定版本。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **RestartIfNeeded** | 無 | 此旗標可讓部署腳本在必要時重新開機電腦，而不需要提示。 |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

IoTEdge 命令會使用您的裝置連接字串和操作詳細資料來設定 IoT Edge。 此命令所產生的大部分資訊都會儲存在 iotedge\config.yaml 檔案中。 初始化命令會接受這些一般參數，還有其他參數。 如需完整清單，請使用命令 `Get-Help Initialize-IoTEdge -full`。

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **手動** | None | **切換參數**。 如果未指定布建類型，則手動為預設值。<br><br>宣告您將提供裝置連接字串以手動佈建裝置 |
| **Dps** | None | **切換參數**。 如果未指定布建類型，則手動為預設值。<br><br>宣告您將提供裝置佈建服務 (DPS) 的範圍識別碼，以及透過 DPS 佈建的裝置註冊識別碼。  |
| **DeviceConnectionString** | 此連接字串來自 IoT 中樞註冊的 IoT Edge 裝置，以單引號括住 | 此為手動安裝的**必要項目**。 如果您未提供指令碼參數中的連接字串，在安裝期間會提示您輸入一個連接字串。 |
| **ScopeId** | 此範圍識別碼來自與您 IoT 中樞相關聯之裝置佈建服務的執行個體。 | 此為 DPS 安裝的**必要項目**。 如果您未提供指令碼參數中的範圍識別碼，在安裝期間會提示您輸入範圍識別碼。 |
| **RegistrationId** | 由您裝置產生的註冊識別碼 | 如果使用 TPM 或對稱金鑰證明，則為 DPS 安裝的**必要**項。 |
| **SymmetricKey** | 使用 DPS 時，用來布建 IoT Edge 裝置身分識別的對稱金鑰 | 如果使用對稱金鑰證明，則為 DPS 安裝的**必要**項。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器作業系統，Windows 就是預設值。<br><br>針對 Windows 容器，IoT Edge 會使用安裝中所包含的 moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理程式映像 URI | 根據預設，新的 IoT Edge 安裝會為 IoT Edge 代理程式映像使用最新的輪替標籤。 使用此參數，為映像版本設定特定標籤，或提供您自己的代理程式映像。 如需詳細資訊，請參閱[了解 IoT Edge 標籤](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **使用者名稱** | 容器登錄使用者名稱 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的使用者名稱。 |
| **密碼** | 安全密碼字串 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的密碼。 |

### <a name="update-iotedge"></a>更新-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器 OS，Windows 就是預設值。 對於 Windows 容器，安裝中將包含容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式會檢查列出的目錄中是否有安裝所需的 IoT Edge cab 和 VC Runtime MSI 檔案。 在目錄中找不到的任何檔案都會下載。 如果這兩個檔案都在目錄中，您可以在沒有網際網路連線的情況下安裝 IoT Edge。 您也可以使用此參數來使用特定版本。 |
| **RestartIfNeeded** | 無 | 此旗標可讓部署腳本在必要時重新開機電腦，而不需要提示。 |

### <a name="uninstall-iotedge"></a>卸載-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **使** | 無 | 如果先前的卸載嘗試失敗，此旗標會強制卸載。
| **RestartIfNeeded** | 無 | 必要時，此旗標可讓卸載腳本重新開機電腦，而不需要提示。 |

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
