---
title: 更新裝置上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何更新 IoT Edge 裝置以執行最新版的安全性精靈和 IoT Edge 執行階段
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a739736182713b35c3a5e9e25742aa39c5d1122
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633132"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全性精靈和執行階段

當 IoT Edge 服務發行新版本時，您會想要更新您的 IoT Edge 裝置，以取得最新的功能和安全性改善。 本文提供在新版本發行時如何更新 IoT Edge 裝置的相關資訊。

如果您想要更新至較新的版本，IoT Edge 裝置將有兩個元件需要更新。 第一個元件是在裝置上執行，並在裝置啟動時啟動執行階段模組的安全性精靈。 目前，安全性精靈只能從裝置本身進行更新。 第二個元件是由 IoT Edge 中樞和 IoT Edge 代理程式模組組成的執行階段。 根據您建構部署的方式，執行階段可以從裝置或從遠端更新。

若要尋找最新版的 Azure IoT Edge，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全性精靈

IoT Edge 安全性精靈是一項原生元件，必須在 IoT Edge 裝置上使用套件管理員進行更新。

請使用 `iotedge version` 命令檢查在您的裝置上執行的安全性精靈版本。

# <a name="linux"></a>[Linux](#tab/linux)

在 Linux x64 裝置上，使用 apt 取得或您適當的套件管理員，將安全性背景程式更新為最新版本。

從 Microsoft 取得最新的存放庫設定：

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

複製產生的清單。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

安裝 Microsoft GPG 公開金鑰。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

更新 apt。

   ```bash
   sudo apt-get update
   ```

檢查以查看有哪些 IoT Edge 版本可供使用。

   ```bash
   apt list -a iotedge
   ```

如果您想要更新至最新版本的安全 daemon，請使用下列命令，它也會將 **libiothsm-std** 更新為最新版本：

   ```bash
   sudo apt-get install iotedge
   ```

如果您想要更新為特定版本的安全性守護程式，請從 apt 清單輸出中指定版本。 每當更新 **iotedge** 時，它會自動嘗試將 **libiothsm std** 封裝更新為最新版本，而這可能會造成相依性衝突。 如果您不打算使用最新版本，請務必以相同版本的兩個套件為目標。 例如，下列命令會安裝特定版本的1.0.9 版本：

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

如果您想要安裝的版本無法透過 apt 取得，您可以使用捲曲，以 [IoT Edge 發行](https://github.com/Azure/azure-iotedge/releases) 存放庫中的任何版本為目標。 針對您想要安裝的任何版本，找出適合您裝置的 **libiothsm-std** 和 **iotedge** 檔案。 針對每個檔案，以滑鼠右鍵按一下檔案連結，然後複製連結位址。 使用連結位址來安裝這些元件的特定版本：

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

針對 Windows 上的 Linux IoT Edge，IoT Edge 會在 Windows 裝置上裝載的 Linux 虛擬機器中執行。 此虛擬機器已預先安裝 IoT Edge，並使用 Microsoft Update 進行管理，讓元件保持在最新狀態。 目前沒有可用的更新。

::: moniker-end

使用適用于 Windows 的 IoT Edge，IoT Edge 會直接在 Windows 裝置上執行。 如需使用 PowerShell 腳本的更新指示，請參閱 [安裝和管理 Windows 的 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md)。

---

## <a name="update-the-runtime-containers"></a>更新執行階段容器

您更新 IoT Edge 代理程式和 IoT Edge 中樞容器的方式，取決於您使用的是輪流標記 (例如 1.0) 或特定標記 (例如部署中的 1.0.7) 。

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令，檢查您裝置上目前的 IoT Edge 代理程式和 IoT Edge 中樞模組版本。

  ![在記錄中尋找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 標記

IoT Edge 代理程式和 IoT Edge 中樞映像會標示與其相關聯的 IoT Edge 版本。 標記可透過兩種不同的使用方式用於執行階段映像上：

* **累積標記** - 僅使用版本號碼的前兩個值來取得符合這些數字的最新映像。 例如，有新版本指向最新的 1.0.x 版時，就會更新 1.0。 如果 IoT Edge 裝置的容器執行階段重新提取映像，執行階段模組就會更新為最新版本。 進行開發時建議使用此方法。 從 Azure 入口網站執行的部署預設為累積標記。

* **特定標記** - 版本號碼的三個值全都會使用，以明確設定映像版本。 例如，1.0.7 不會在其初始版本之後變更。 當您準備好要更新時，您可以在部署資訊清單中宣告新的版本號碼。 建議將此方法用於生產用途。

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

如果您在部署中使用特定標記 (例如，mcr.microsoft.com/azureiotedge-hub：**1.0.8**) 然後您只需要更新部署資訊清單中的標記，並將變更套用至您的裝置。

1. 在 [Azure 入口網站中的 IoT 中樞，選取您的 IoT Edge 裝置，然後選取 [ **設定模組**]。

1. 在 [ **IoT Edge 模組** ] 區段中，選取 [ **執行時間設定**]。

   ![設定執行時間設定](./media/how-to-update-iot-edge/configure-runtime.png)

1. 在 [**執行時間設定**] 中，以所需的版本更新 **Edge 中樞** 的 **映射** 值。 不要選取 [ **儲存** ]。

   ![更新 Edge 中樞映射版本](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. 折迭 **Edge 中樞** 設定，或向下滾動，然後以相同的所需版本更新 **edge 代理程式** 的 **影像** 值。

   ![更新 Edge 中樞代理程式版本](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. 選取 [儲存]。

1. 選取 [ **審核 + 建立**]、[檢查部署]，然後選取 [ **建立**]。

## <a name="update-to-a-release-candidate-version"></a>更新為發行候選版本

Azure IoT Edge 定期發行新版本的 IoT Edge 服務。 在每個穩定版本之前，有一或多個候選版 (RC) 版本。 RC 版本包含所有已規劃的發行功能，但仍在進行測試和驗證。 如果您想要提早測試新的功能，您可以安裝 RC 版本，並透過 GitHub 提供意見反應。

候選版會遵循相同的發行編號慣例，但會加上後面 **附加的遞增** 數位。 您可以在 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases) 的相同清單中看到發行候選版本，作為穩定版本。 例如，尋找 **1.0.9-rc5** 和 **1.0.9-rc6**，這是 **1.0.9** 之前的兩個候選版。 您也可以看到 RC 版本戳記為 **發行前** 版本標籤。

IoT Edge 代理程式和中樞模組都有以相同慣例標記的 RC 版本。 例如， **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc6**。

若為預覽版本，則不會將發行候選版本包含為一般安裝程式目標的最新版本。 相反地，您必須以手動方式將您想要測試的 RC 版本的資產設為目標。 在大部分的情況下，安裝或更新至 RC 版本與以任何其他特定版本的 IoT Edge 為目標。

使用本文中的各節，瞭解如何將 IoT Edge 裝置更新為特定版本的安全 daemon 或執行時間模組。

如果您要安裝 IoT Edge，而不是升級現有的安裝，請使用 [離線或特定版本安裝](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)中的步驟。

## <a name="next-steps"></a>下一步

檢視最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

在[物聯網的 blog](https://azure.microsoft.com/blog/topics/internet-of-things/)中隨時掌握最新的更新和公告