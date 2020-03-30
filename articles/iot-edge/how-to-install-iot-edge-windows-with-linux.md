---
title: 在 Windows 上安裝適用于 Linux 的 Azure IoT 邊緣 |微軟文檔
description: Windows 10、Windows 伺服器和 Windows IoT 核心上的 Linux 容器的 Azure IoT 邊緣安裝說明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133167"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>在 Windows 上使用 IoT 邊緣運行 Linux 容器

使用 Windows 電腦測試 Linux 設備的 IoT 邊緣模組。

在生產方案中，Windows 設備應僅運行 Windows 容器。 但是，一個常見的開發方案是使用 Windows 電腦為 Linux 設備構建 IoT Edge 模組。 Windows 的 IoT 邊緣運行時允許您運行 Linux 容器以**進行測試和開發**。

本文列出了在 Windows x64 （AMD/英特爾） 系統上使用 Linux 容器安裝 Azure IoT 邊緣運行時的步驟。 要瞭解有關 IoT Edge 運行時安裝程式的詳細資訊（包括有關所有安裝參數的詳細資訊），請參閱在[Windows 上安裝 Azure IoT 邊緣運行時](how-to-install-iot-edge-windows.md)。

有關最新版本 IoT Edge 中包含的內容的資訊，請參閱[Azure IoT 邊緣版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>Prerequisites

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。

### <a name="supported-windows-versions"></a>支援的 Windows 版本

具有 Linux 容器的 Azure IoT 邊緣可以在滿足[Docker 桌面要求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的任何版本的 Windows 上運行

如果要在虛擬機器上安裝 IoT Edge，請啟用嵌套虛擬化並分配至少 2 GB 記憶體。 啟用嵌套虛擬化的方式因使用虛擬機器管理程式而異。 對於 Hyper-V，預設情況下，第 2 代虛擬機器啟用嵌套虛擬化。 對於 VMWare，有一個切換功能來在虛擬機器上啟用該功能。

### <a name="prepare-the-container-engine"></a>準備容器引擎

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 在 Windows 電腦上運行 Windows 和 Linux 容器的最大配置區別是 IoT Edge 安裝包括 Windows 容器運行時，但在安裝 IoT Edge 之前，您需要為 Linux 容器提供您自己的運行時。

要設置 Windows 電腦以開發和測試 Linux 設備的容器，可以使用[Docker Desktop](https://www.docker.com/docker-windows)作為容器引擎。 您需要安裝 Docker 並將其配置為在安裝 IoT 邊緣之前[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。  

如果您的 IoT Edge 裝置是 Windows 電腦，請檢查其是否符合 Hyper-V 的[系統需求](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)。

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 可以使用 IoT 中心提供的設備連接字串手動預配單個 IoT Edge 設備。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要設定時將很有幫助。

您可以在"[在 Windows 上安裝 Azure IoT 邊緣運行時](how-to-install-iot-edge-windows.md)"一文中瞭解有關不同安裝選項和參數的詳細資訊。 安裝並配置了用於 Linux 容器的 Docker Desktop 後，主要安裝區別在於使用 **-ContainerOs**參數聲明 Linux。 例如：

1. 如果尚未註冊新的 IoT Edge 設備並檢索設備連接字串。 複製連接字串以在本部分的後面部分使用。 您可以使用以下工具完成此步驟：

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

3. **Deploy-IoTEdge**命令檢查 Windows 電腦是否位於受支援的版本上，打開容器功能，然後下載 moby 運行時（不適用於 Linux 容器）和 IoT Edge 運行時。 該命令預設為 Windows 容器，因此將 Linux 聲明為所需的容器作業系統。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. 此時，IoT Core 設備可能會自動重新開機。 其他 Windows 10 或 Windows 伺服器設備可能會提示您重新開機。 如果是，請立即重新開機設備。 設備準備就緒後，再次以管理員身份運行 PowerShell。

5. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 該命令預設為使用設備連接字串手動預配。 再次將 Linux 聲明為所需的容器作業系統。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. 當出現提示時，提供在步驟 1 中檢索的設備連接字串。 設備連接字串將物理設備與 IoT 中心中的裝置識別碼 關聯。

   設備連接字串採用以下格式，不應包含引號：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>確認安裝成功

檢查 IoT 邊緣服務的狀態：

```powershell
Get-Service iotedge
```

檢查過去 5 分鐘的服務日誌：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

運行針對最常見的配置和網路錯誤的自動檢查：

```powershell
iotedge check
```

列出執行中的模組。 新安裝後，您應該看到運行的唯一模組是**邊緣代理**。 首次[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)後，其他系統模組**EdgeHub**也將在設備上啟動。

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
