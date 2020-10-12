---
title: 在 Windows 上安裝 Azure IoT Edge | Microsoft Docs
description: 在 Windows 10、Windows Server 和 Windows IoT 核心版上安裝 Azure IoT Edge 的指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: ba3e8b9d7649d56d1639f7f608d85a2da04ff74a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465553"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。

若要深入了解 IoT Edge 執行階段，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出使用 Windows 容器在 Windows x64 (AMD/Intel) 系統上安裝 Azure IoT Edge 執行時間的步驟。

> [!NOTE]
> 當 IoT Edge 模組 (進程隔離的 Windows Nano Server 容器) 正在執行時，已知的 Windows 作業系統問題會導致轉換為睡眠和休眠電源狀態。 此問題會影響裝置上的電池壽命。
>
> 因應措施是，使用命令 `Stop-Service iotedge` 來停止任何正在執行的 IoT Edge 模組，再使用這些電源狀態。

就 Azure IoT Edge 而言，在 Windows 系統上使用 Linux 容器不是建議或支援的生產環境設定。 不過，這很適合用於開發與測試用途。 若要深入瞭解，請參閱 [使用 Windows 上的 IoT Edge 來執行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

如需 IoT Edge 最新版本中所包含內容的相關資訊，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>Prerequisites

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。

### <a name="supported-windows-versions"></a>支援的 Windows 版本

適用于 Windows 的 IoT Edge 需要 Windows version 1809/build 17763，這是最新的 [windows 長期支援組建](https://docs.microsoft.com/windows/release-information/)。 如需 Windows SKU 支援，請參閱根據您要準備進行生產案例或開發和測試案例所支援的功能：

* **生產**：如需有關生產案例目前支援哪些作業系統的最新資訊，請參閱 [Azure IoT Edge 支援的系統](support.md#operating-systems)。
* **開發與測試**：針對開發和測試案例，可在任何 SKU 上安裝 windows 容器的 Azure IoT Edge， (Pro、Enterprise、Server 等等。支援容器功能的 windows 組建 17763 ) 。

IoT 核心裝置必須包含可支援 IoT Edge 執行時間的 IoT 核心 Windows 容器選用功能。 在 [遠端 PowerShell 會話](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) 中使用下列命令，以檢查您的裝置是否支援 Windows 容器：

```powershell
Get-Service vmcompute
```

如果服務存在，您應該會收到成功的回應，其中的服務 **狀態列為 [** 執行中]。 如果 `vmcompute` 找不到服務，則您的裝置不符合 IoT Edge 的需求。 請洽詢您的硬體提供者，詢問這項功能的支援。

### <a name="prepare-for-a-container-engine"></a>準備容器引擎

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 對於生產案例，請使用安裝指令碼中包含的 Moby 引擎，在 Windows 裝置上執行 Windows 容器。

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。

>[!TIP]
>針對 IoT 核心裝置，我們建議使用 RemotePowerShell 會話來執行安裝命令。 如需詳細資訊，請參閱 [使用適用于 Windows IoT 的 PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)。

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 您可以使用 IoT 中樞所提供的裝置連接字串，以手動方式布建單一 IoT Edge 裝置。 或者，您可以使用裝置布建服務 (DPS) 來自動布建裝置，這在您有許多裝置要設定時很有説明。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。

下列各節說明新裝置上 IoT Edge 安裝指令碼的常見使用案例和參數。

### <a name="option-1-install-and-manually-provision"></a>選項 1：安裝並手動佈建

在第一個選項中，您會提供 IoT 中樞所產生的 **裝置連接字串** 來布建裝置。

此範例示範如何使用 Windows 容器進行手動安裝：

1. 如果您還沒有這麼做，請註冊新的 IoT Edge 裝置，並取得 **裝置連接字串**。 複製連接字串，以便稍後在此區段中使用。 您可以使用下列工具來完成此步驟：

   * [Azure 入口網站](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code) \(英文\)

2. 以系統管理員身分執行 PowerShell。

   >[!NOTE]
   >使用 PowerShell 的 AMD64 工作階段來安裝 IoT Edge，而不是使用 PowerShell (x86)。 如果您不確定正在使用的工作階段類型，請執行下列命令：
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **IoTEdge**命令會檢查您的 Windows 電腦是否位於支援的版本、開啟容器功能，然後下載 moby 執行時間和 IoT Edge 執行時間。 命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 此時，IoT 核心裝置可能會自動重新開機。 其他 Windows 10 或 Windows Server 裝置可能會提示您重新開機。 若是如此，請立即重新開機您的裝置。 當您的裝置準備就緒之後，請再次以系統管理員身分執行 PowerShell。

5. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 此命令預設為 Windows 容器的手動佈建。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 出現提示時，請提供您在步驟1中取出的裝置連接字串。 裝置連接字串會將實體裝置與 IoT 中樞內的裝置識別碼產生關聯。

   裝置連接字串採用下列格式，且不應包含引號： `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 使用 [ [確認安裝成功](#verify-successful-installation) ] 中的步驟來檢查裝置上 IoT Edge 的狀態。

當您手動安裝及佈建裝置時，可以使用其他參數修改安裝，包括：

* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

如需這些安裝選項的詳細資訊，請直接跳到了解 [所有安裝參數](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>選項 2：安裝並自動佈建

在第二個選項中，使用 IoT 中樞裝置佈建服務來佈建裝置。 提供來自裝置布建服務實例的 **範圍識別碼** ，以及您慣用 [證明機制](../iot-dps/concepts-security.md#attestation-mechanism)特定的任何其他資訊：

* [使用虛擬 TPM 在 Windows 上建立及布建模擬的 IoT Edge 裝置](how-to-auto-provision-simulated-device-windows.md)
* [使用 x.509 憑證來建立和布建模擬的 IoT Edge 裝置](how-to-auto-provision-x509-certs.md)
* [使用對稱金鑰證明來建立和布建 IoT Edge 裝置](how-to-auto-provision-symmetric-keys.md)

當您自動安裝和布建裝置時，您可以使用其他參數修改安裝，包括：

* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

如需這些安裝選項的詳細資訊，請繼續閱讀本文，或跳過以了解[所有安裝參數](#all-installation-parameters)。

## <a name="offline-or-specific-version-installation"></a>離線或特定版本安裝

在安裝期間，會下載三個檔案：

* PowerShell 腳本，其中包含安裝指示
* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 的安全 daemon (iotedged) 、Moby 容器引擎和 Moby CLI
* Visual C++ 可轉散發套件 (VC runtime) 安裝程式

如果您的裝置會在安裝期間離線，或您想要安裝特定版本的 IoT Edge，您可以事先將這些檔案下載到裝置。 當您要安裝時，請指向包含所下載檔案的目錄中的安裝腳本。 安裝程式會先檢查該目錄，然後才會下載找不到的元件。 如果所有檔案都可離線使用，您可以不使用網際網路連線來進行安裝。

您也可以使用離線安裝路徑參數來更新 IoT Edge。 如需更多資訊，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

1. 如需最新的 IoT Edge 安裝檔案及舊版，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

2. 尋找您想要安裝的版本，並從版本資訊的 [ **資產** ] 區段中，將下列檔案下載到您的 IoT 裝置：

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab 版本1.0.9 或更新版本，或從1.0.8 和較舊版本中的 Microsoft-Azure-IoTEdge.cab。

   基於測試目的，Microsoft-Azure-IotEdge-arm32.cab 也可以從1.0.9 開始使用。 Windows ARM32 裝置上目前不支援 IoT Edge。

   請務必使用與您所使用之 .cab 檔案相同版本的 PowerShell 腳本，因為功能會變更以支援每個版本中的功能。

3. 如果您下載的 .cab 檔案中有架構尾碼，請將檔案重新命名為只 **Microsoft-Azure-IoTEdge.cab**。

4. （選擇性）下載 Visual C++ 可轉散發套件的安裝程式。 例如，PowerShell 腳本會使用這個版本： [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe)。 將安裝程式儲存在 IoT 裝置上與 IoT Edge 檔案相同的資料夾中。

5. 若要安裝離線元件，請使用 [點來源](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) 作為 PowerShell 腳本的本機複本。 然後，使用 `-OfflineInstallationPath` 參數做為命令的一部分， `Deploy-IoTEdge` 並提供檔案目錄的絕對路徑。 例如，

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Deployment 命令將會使用在提供的本機檔案目錄中找到的任何元件。 如果 .cab 檔或 Visual C++ 安裝程式遺失，則會嘗試下載這些檔案。

6. 執行 `Initialize-IoTEdge` 命令，以使用 IoT 中樞內的身分識別來布建您的裝置。 提供手動布建的裝置連接字串，或選擇先前的 [ [自動](#option-2-install-and-automatically-provision) 布建] 區段中所述的其中一個方法。

   如果您的裝置在執行之後重新開機 `Deploy-IoTEdge` ，請在執行之前，先將 PowerShell 腳本帶到點 `Initialize-IoTEdge` 。

如需離線安裝選項的詳細資訊，請直接跳到了解 [所有安裝參數](#all-installation-parameters)。

## <a name="verify-successful-installation"></a>確認安裝成功

檢查 IoT Edge 服務的狀態。 它應該會列為 [執行中]。  

```powershell
Get-Service iotedge
```

檢查最後 5 分鐘的服務記錄。 如果您剛剛完成安裝 IoT Edge 執行時間，您可能會在執行 **IoTEdge** 與 **IoTEdge**之間的時間內看到錯誤清單。 這是預期的錯誤，因為服務會在設定之前嘗試啟動。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

執行 [疑難排解工具](troubleshoot.md#run-the-check-command) ，檢查是否有最常見的設定和網路錯誤。

```powershell
iotedge check
```

在您將第一個模組部署至裝置上的 IoT Edge 之前， **$edgeHub** 系統模組將不會部署到裝置。 如此一來，自動檢查就會傳回連線檢查的錯誤 `Edge Hub can bind to ports on host` 。 除非您將模組部署到裝置之後發生此錯誤，否則可能會忽略此錯誤。

最後，列出正在執行的模組：

```powershell
iotedge list
```

在新的安裝之後，您應該會看到執行的唯一模組是 **edgeAgent**。 當您第一次 [部署 IoT Edge 模組](how-to-deploy-modules-portal.md) 之後，其他系統模組（ **edgeHub**）也會在裝置上啟動。

## <a name="manage-module-containers"></a>管理模組容器

IoT Edge 服務需要在您的裝置上執行的容器引擎。 當您將模組部署至裝置時，IoT Edge 執行時間會使用容器引擎從雲端中的登錄提取容器映射。 IoT Edge 服務可讓您與模組互動並取出記錄，但有時候您可能會想要使用容器引擎來與容器本身互動。

如需模組概念的詳細資訊，請參閱 [瞭解 Azure IoT Edge 模組](iot-edge-modules.md)。

如果您正在 Windows IoT Edge 裝置上執行 Windows 容器，則 IoT Edge 安裝包含 Moby 容器引擎。 Moby 引擎是以與 Docker 相同的標準為基礎，而且是設計成在與 Docker Desktop 相同的電腦上平行執行。 基於這個理由，如果您想要以 Moby 引擎管理的容器為目標，則必須特別以該引擎為目標，而不是 Docker。

例如，若要列出所有 Docker 映射，請使用下列命令：

```powershell
docker images
```

若要列出所有 Moby 映射，請使用 Moby 引擎的指標來修改相同的命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 會列在安裝腳本的輸出中，您也可以在 yaml 檔案的 [容器執行時間設定] 區段中找到該 URI。

![yaml 中的 moby_runtime uri](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

如需您可以用來與裝置上執行的容器和映射互動的命令的詳細資訊，請參閱 [Docker 命令列介面](https://docs.docker.com/engine/reference/commandline/docker/)。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Windows 裝置中移除 IoT Edge 安裝，請從管理 PowerShell 視窗中使用下列命令。 此命令會移除 IoT Edge 執行階段，以及您現有的設定和 Moby 引擎資料。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Uninstall-IoTEdge 命令無法在 Windows IoT Core 上運作。 若要從 Windows IoT 核心裝置移除 IoT Edge，您需要重新部署 Windows IoT Core 映射。

如需卸載選項的詳細資訊，請使用命令 `Get-Help Uninstall-IoTEdge -full` 。

## <a name="verify-installation-script"></a>確認安裝腳本

本文中提供的安裝命令使用 Invoke-WebRequest Cmdlet 來要求安裝腳本 `aka.ms/iotedge-win` 。 此連結會指向 `IoTEdgeSecurityDaemon.ps1` 最新 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)的腳本。 您也可以從特定版本下載此腳本或腳本的版本，以在 IoT Edge 裝置上執行安裝命令。

提供的腳本已簽署來提高安全性。 您可以藉由將腳本下載到您的裝置，然後執行下列 PowerShell 命令來驗證簽章：

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

如果簽章經過驗證，輸出狀態就會是 **有效** 的。

## <a name="all-installation-parameters"></a>所有安裝參數

前幾節介紹常見的安裝案例，示範如何使用參數來修改安裝指令碼。 本節提供用來安裝、更新或卸載 IoT Edge 之一般參數的參考資料表。

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge 命令會下載並部署 IoT Edge 安全性 Daemon 及其相依性。 部署命令會接受這些一般參數，以及其他參數。 如需完整清單，請使用命令 `Get-Help Deploy-IoTEdge -full` 。  

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定任何容器作業系統，Windows 會是預設值。<br><br>針對 Windows 容器，IoT Edge 使用安裝所包含的 moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式會檢查列出的目錄中是否有安裝所需的 IoT Edge cab 和 VC Runtime MSI 檔案。 在目錄中找不到的任何檔案都會下載。 如果這兩個檔案都在目錄中，您可以在沒有網際網路連線的情況下安裝 IoT Edge。 您也可以使用此參數來使用特定版本。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **RestartIfNeeded** | 無 | 必要時，此旗標可讓部署腳本重新開機電腦，而不需要提示。 |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge 命令會使用您的裝置連接字串和操作詳細資料來設定 IoT Edge。 此命令所產生的大部分資訊都會儲存在 iotedge\config.yaml 檔案中。 初始化命令會接受這些一般參數，以及其他參數。 如需完整清單，請使用命令 `Get-Help Initialize-IoTEdge -full` 。

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **手動** | 無 | **切換參數**。 如果未指定任何布建類型，則會以手動作為預設值。<br><br>宣告您將提供裝置連接字串以手動佈建裝置 |
| **存款 保障 計畫** | 無 | **切換參數**。 如果未指定任何布建類型，則會以手動作為預設值。<br><br>宣告您將提供裝置佈建服務 (DPS) 的範圍識別碼，以及透過 DPS 佈建的裝置註冊識別碼。  |
| **DeviceConnectionString** | 此連接字串來自 IoT 中樞註冊的 IoT Edge 裝置，以單引號括住 | 手動布建**所需**。 如果您未在腳本參數中提供連接字串，系統會提示您輸入一個。 |
| **ScopeId** | 此範圍識別碼來自與您 IoT 中樞相關聯之裝置佈建服務的執行個體。 | DPS 布建**所需**。 如果您未在腳本參數中提供範圍識別碼，系統會提示您輸入一個。 |
| **RegistrationId** | 由您裝置產生的註冊識別碼 | 如果使用 TPM 或對稱金鑰證明，則為 DPS 布建**所需**。 如果使用 x.509 憑證證明，則為**選擇性**。 |
| **X509IdentityCertificate** | 裝置上 x.509 裝置身分識別憑證的 URI 路徑。 | 如果使用 x.509 憑證證明，則**必須**提供 DPS 布建。 |
| **X509IdentityPrivateKey** | 裝置上 x.509 裝置身分識別憑證金鑰的 URI 路徑。 | 如果使用 x.509 憑證證明，則**必須**提供 DPS 布建。 |
| **SymmetricKey** | 使用 DPS 時，用來布建 IoT Edge 裝置身分識別的對稱金鑰 | 如果使用對稱金鑰證明，則為 DPS 布建**所需**。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定任何容器作業系統，Windows 會是預設值。<br><br>針對 Windows 容器，IoT Edge 使用安裝所包含的 moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理程式映像 URI | 根據預設，新的 IoT Edge 安裝會為 IoT Edge 代理程式映像使用最新的輪替標籤。 使用此參數，為映像版本設定特定標籤，或提供您自己的代理程式映像。 如需詳細資訊，請參閱[了解 IoT Edge 標籤](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **使用者名稱** | 容器登錄使用者名稱 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的使用者名稱。 |
| **密碼** | 安全密碼字串 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的密碼。 |

### <a name="update-iotedge"></a>Update-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定任何容器 OS，則 Windows 會是預設值。 對於 Windows 容器，安裝中將包含容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式會檢查列出的目錄中是否有安裝所需的 IoT Edge cab 和 VC Runtime MSI 檔案。 在目錄中找不到的任何檔案都會下載。 如果這兩個檔案都在目錄中，您可以在沒有網際網路連線的情況下安裝 IoT Edge。 您也可以使用此參數來使用特定版本。 |
| **RestartIfNeeded** | 無 | 必要時，此旗標可讓部署腳本重新開機電腦，而不需要提示。 |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **力** | 無 | 此旗標會強制卸載，以防先前嘗試卸載失敗。
| **RestartIfNeeded** | 無 | 必要時，此旗標可讓卸載腳本重新開機電腦，而不需要提示。 |

## <a name="next-steps"></a>接下來的步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
