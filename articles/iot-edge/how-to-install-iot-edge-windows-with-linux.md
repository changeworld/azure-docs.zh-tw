---
title: 在 Windows 上安裝適用于 Linux 的 Azure IoT Edge |Microsoft Docs
description: Azure IoT Edge Windows 10、Windows Server 和 Windows IoT 核心版上的 Linux 容器安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80133167"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>使用 Windows 上的 IoT Edge 執行 Linux 容器

使用 Windows 機器測試適用于 Linux 裝置的 IoT Edge 模組。

在生產案例中，Windows 裝置應該只執行 Windows 容器。 不過，常見的開發案例是使用 Windows 電腦來建立適用于 Linux 裝置的 IoT Edge 模組。 適用于 Windows 的 IoT Edge 執行時間可讓您執行 Linux 容器，以進行**測試和開發**之用。

本文列出在 Windows x64 （AMD/Intel）系統上使用 Linux 容器安裝 Azure IoT Edge 執行時間的步驟。 若要深入瞭解 IoT Edge 執行時間安裝程式，包括所有安裝參數的詳細資料，請參閱[在 Windows 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-windows.md)。

如需最新 IoT Edge 版本中所包含內容的詳細資訊，請參閱[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>必要條件

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。

### <a name="supported-windows-versions"></a>支援的 Windows 版本

Linux 容器的 Azure IoT Edge 可在任何符合[Docker Desktop 需求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的 Windows 版本上執行

如果您想要在虛擬機器上安裝 IoT Edge，請啟用 [嵌套虛擬化]，並配置至少 2 GB 的記憶體。 根據您使用的虛擬機器，如何啟用嵌套虛擬化會有所不同。 針對 Hyper-v，第2代虛擬機器預設會啟用嵌套虛擬化。 針對 VMWare，有一個可在虛擬機器上啟用功能的切換。

### <a name="prepare-the-container-engine"></a>準備容器引擎

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 在 Windows 電腦上執行 Windows 和 Linux 容器的最大設定差異在於，IoT Edge 安裝包含 Windows 容器執行時間，但您必須先提供 Linux 容器的執行時間，然後再安裝 IoT Edge。

若要設定 Windows 電腦以開發和測試 Linux 裝置的容器，您可以使用[Docker Desktop](https://www.docker.com/docker-windows)作為容器引擎。 您必須先安裝 Docker，並將它設定為[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)，然後再安裝 IoT Edge。  

如果您的 IoT Edge 裝置是 Windows 電腦，請檢查其是否符合 Hyper-V 的[系統需求](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)。

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 您可以使用 IoT 中樞所提供的裝置連接字串，手動布建單一 IoT Edge 裝置。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要設定時將很有幫助。

您可以在在[Windows 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-windows.md)一文中，閱讀有關不同安裝選項和參數的詳細資訊。 在您安裝並設定適用于 Linux 容器的 Docker Desktop 之後，主要的安裝差異在於使用 **-ContainerOs**參數宣告 Linux。 例如：

1. 如果您還沒有這麼做，請註冊新的 IoT Edge 裝置，並取出裝置連接字串。 複製連接字串，以供本節稍後使用。 您可以使用下列工具來完成此步驟：

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

3. **IoTEdge**命令會檢查您的 Windows 電腦是否在支援的版本上，開啟 [容器] 功能，然後下載 moby 執行時間（不適用於 Linux 容器）和 IoT Edge 執行時間。 命令預設為 Windows 容器，因此請將 Linux 宣告為所需的容器作業系統。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. 此時，IoT 核心版裝置可能會自動重新開機。 其他 Windows 10 或 Windows Server 裝置可能會提示您重新開機。 若是如此，請立即重新開機您的裝置。 一旦您的裝置準備就緒，請再次以系統管理員身分執行 PowerShell。

5. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 命令預設為使用裝置連接字串進行手動布建。 再次將 Linux 宣告為所需的容器作業系統。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. 出現提示時，提供您在步驟1中取得的裝置連接字串。 裝置連接字串會將實體裝置與 IoT 中樞中的裝置識別碼產生關聯。

   裝置連接字串採用下列格式，且不應包含引號：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>確認安裝成功

檢查 IoT Edge 服務的狀態：

```powershell
Get-Service iotedge
```

檢查過去5分鐘內的服務記錄：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

針對最常見的設定和網路錯誤執行自動檢查：

```powershell
iotedge check
```

列出執行中的模組。 在新的安裝之後，您應該會看到執行的唯一模組是**edgeAgent**。 當您第一次[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)之後，另一個系統模組**edgeHub**也會在裝置上啟動。

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
