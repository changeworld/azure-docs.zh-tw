---
title: 更新裝置上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何更新 IoT Edge 裝置以執行最新版的安全性精靈和 IoT Edge 執行階段
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ce69593c1df0039d64f89e79124af1150409eff7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113310"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全性精靈和執行階段

當 IoT Edge 服務發表新版本時,您需要更新 IoT Edge 裝置,以便獲得最新的功能和安全改進。 本文提供在新版本發行時如何更新 IoT Edge 裝置的相關資訊。

如果您想要更新至較新的版本，IoT Edge 裝置將有兩個元件需要更新。 第一個元件是在裝置上執行，並在裝置啟動時啟動執行階段模組的安全性精靈。 目前，安全性精靈只能從裝置本身進行更新。 第二個元件是由 IoT Edge 中樞和 IoT Edge 代理程式模組組成的執行階段。 根據您建構部署的方式，執行階段可以從裝置或從遠端更新。

若要尋找最新版的 Azure IoT Edge，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全性精靈

IoT Edge 安全性精靈是一項原生元件，必須在 IoT Edge 裝置上使用套件管理員進行更新。

請使用 `iotedge version` 命令檢查在您的裝置上執行的安全性精靈版本。

### <a name="linux-devices"></a>Linux 裝置

在 Linux x64 裝置上,使用 apt-get 或適當的程式包管理器將安全守護程式更新到最新版本。

```bash
apt-get update
apt-get install libiothsm iotedge
```

如果要更新到特定版本的安全守護程式,請找到要從[IoT Edge版本](https://github.com/Azure/azure-iotedge/releases)中定位的版本。 在該版本中,找到適合您的設備的**Libiothm-std**和**iotedge**檔。 對於每個檔,右鍵單擊檔連結並複製連結位址。 使用連結位址安裝這些元件的特定版本:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows 裝置

在 Windows 設備上,使用 PowerShell 文本更新安全守護程式。 腳本會自動提取最新版本的安全守護程式。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

運行 Update-IoTEdge 命令會從設備中刪除和更新安全守護進程以及兩個運行時容器映射。 config.yaml 檔案儲存在設備上,以及來自 Moby 容器引擎的數據(如果您使用的是 Windows 容器)。 保留配置資訊意味著在更新過程中不必再次為設備提供連接字串或設備預配服務資訊。

如果要更新到特定版本的安全守護程式,請找到要從[IoT Edge版本](https://github.com/Azure/azure-iotedge/releases)中定位的版本。 在該版本中,下載**Microsoft-Azure-IoTEdge.cab**檔。 然後,使用`-OfflineInstallationPath`參數指向本地檔位置。 例如：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>該`-OfflineInstallationPath`參數在提供的目錄中查找名為**Microsoft-Azure-IoTEdge.cab**的檔。 從 IoT Edge 版本 1.0.9-rc4 開始,有兩個 .cab 檔案可供使用,一個用於 AMD64 設備,一個用於 ARM32。 下載裝置的正確檔,然後重新命名檔案以刪除架構結構後綴。

有關更新選項的詳細資訊,請使用指令`Get-Help Update-IoTEdge -full`或參考[所有安裝參數](how-to-install-iot-edge-windows.md#all-installation-parameters)。

## <a name="update-the-runtime-containers"></a>更新執行階段容器

更新 IoT Edge 代理和 IoT Edge 集線器容器的方式取決於部署中是否使用滾動標記(如 1.0)或特定標記(如 1.0.7)。

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令，檢查您裝置上目前的 IoT Edge 代理程式和 IoT Edge 中樞模組版本。

  ![在記錄中尋找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 標記

IoT Edge 代理程式和 IoT Edge 中樞映像會標示與其相關聯的 IoT Edge 版本。 標記可透過兩種不同的使用方式用於執行階段映像上：

* **累積標記** - 僅使用版本號碼的前兩個值來取得符合這些數字的最新映像。 例如，有新版本指向最新的 1.0.x 版時，就會更新 1.0。 如果 IoT Edge 裝置的容器執行階段重新提取映像，執行階段模組就會更新為最新版本。 進行開發時建議使用此方法。 從 Azure 入口網站執行的部署預設為累積標記。

* **特定標記** - 版本號碼的三個值全都會使用，以明確設定映像版本。 例如,1.0.7 在初始發佈後不會更改。 當您準備好要更新時，您可以在部署資訊清單中宣告新的版本號碼。 建議將此方法用於生產用途。

### <a name="update-a-rolling-tag-image"></a>更新累積標記映像

如果您在部署中使用累積標記 (例如 mcr.microsoft.com/azureiotedge-hub:**1.0**)，您就必須在裝置上強制執行容器執行階段以提取映像的最新版本。

從您的 IoT Edge 裝置中刪除映像的本機版本。 在 Windows 機器上，解除安裝安全性精靈也會移除執行階段映像，因此您不需要再採取此步驟。

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

您可能需要使用強制 `-f` 旗標將映像移除。

IoT Edge 服務將會提取執行階段映像的最新版本，並自動在您的裝置上將其重新啟動。

### <a name="update-a-specific-tag-image"></a>更新特定標記映像

如果在部署中使用特定標記(例如 **,mcr.microsoft.com/azureiotedge-hub:1.0.8),** 則只需更新部署清單中的標記並將更改應用於設備。

1. 在 Azure 門戶中的 IoT 中心中,選擇 IoT 邊緣設備,然後選擇 **「設定模組**」。

1. 在**IoT 邊緣模組**部份中,選擇**執行時設定**。

   ![設定執行時設定](./media/how-to-update-iot-edge/configure-runtime.png)

1. 在**執行時設定中**,使用所需的版本更新**邊緣中心****的圖像值。** 不要選擇 **"保存**"。

   ![更新邊緣中心映像版本](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. 摺疊**邊緣中心**設置,或向下滾動,並使用相同的所需版本更新**邊緣代理****的圖像**值。

   ![更新邊緣中心代理版本](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. 選取 [儲存]  。

1. 選擇 **「審閱」 = 創建**、查看部署,然後選擇 **「創建**」 。

## <a name="update-offline-or-to-a-specific-version"></a>離線更新或更新到特定版本

如果要離線更新設備,或更新到IoT Edge的特定版本(而不是最新版本),可以使用`-OfflineInstallationPath`參數執行此操作。

兩個元件用於更新 IoT 邊緣裝置:

* 包含安裝說明的 PowerShell 文稿
* Microsoft Azure IoT 邊緣駕駛室,其中包含 IoT 邊緣安全守護程式(ioedge)、Moby 容器引擎和 Moby CLI

1. 有關最新的 IoT 邊緣安裝檔以及以前的版本,請參閱[Azure IoT 邊緣版本](https://github.com/Azure/azure-iotedge/releases)。

2. 尋找要安裝的版本,並從發行說明的 **「資產**」部分下載以下檔案到 IoT 裝置:

   * 物聯網安全守護程式.ps1
   * 來自版本 1.0.9 或較新的 Microsoft-Azure-IoTEdge-amd64.cab,或版本 1.0.8 及更高版本的 Microsoft-Azure-IoTEdge.cab。

   Microsoft-Azure-IotEdge-arm32.cab 也從 1.0.9 開始提供,僅用於測試目的。 Windows ARM32 設備上當前不支援 IoT 邊緣。

   使用與使用的 .cab 檔案相同的版本中的 PowerShell 腳本非常重要,因為功能會更改以支援每個版本中的功能。

3. 如果下載的 .cab 檔案上具有架構結構後綴,請將該檔重新命名為僅**Microsoft-Azure-IoTEdge.cab**。

4. 要使用脫機組件進行更新,[請點點源](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing)PowerShell 腳本的本地副本。 然後,`-OfflineInstallationPath`將 參數用作命令`Update-IoTEdge`的一部分,並提供檔目錄的絕對路徑。 例如，

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>更新到版本候選版本

Azure IoT Edge 定期發佈 IoT 邊緣服務的新版本。 在每個穩定版本之前,都有一個或多個候選版本 (RC) 版本。 RC 版本包括該版本的所有計畫功能,但仍在進行測試和驗證。 如果要儘早測試新功能,可以安裝RC版本並透過GitHub提供回饋。

候選版本遵循相同的版本編號約定,但末尾附加了 **-rc**加上增量編號。 您可以在 Azure [IoT 邊緣版本](https://github.com/Azure/azure-iotedge/releases)與穩定版本相同的清單中看到發佈候選項。 例如,找到**1.0.7-rc1**和**1.0.7-rc2,** 這兩個釋放候選項出現在**1.0.7**之前。 您還可以看到 RC 版本標有**預先發佈**標籤。

IoT Edge 代理和集線器模組具有使用相同約定標記的 RC 版本。 例如 **,mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**。

作為預覽版,候選版本不作為常規安裝程序目標的最新版本包含在內。 相反,您需要手動定位要測試的 RC 版本的資產。 在大多數情況下,安裝或更新到RC版本與定位任何其他特定版本的IoT Edge相同。

使用本文中的各節瞭解如何將 IoT Edge 設備更新為安全守護程式或運行時模組的特定版本。

如果要在新電腦上安裝 IoT Edge,請使用以下連結瞭解如何根據裝置作業系統安裝特定版本:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>後續步驟

檢視最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持續關注[物聯網部落格](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告
