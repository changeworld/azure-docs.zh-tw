---
title: 支援的作業系統、容器引擎 - Azure IoT Edge
description: 了解哪些作業系統可以執行 Azure IoT Edge 精靈和執行階段，以及針對您生產環境裝置支援的容器引擎
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ccfd85c5976642d30b3f21d2d2afeb868ba49bf9
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924609"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支援的系統

此文章提供 IoT Edge (不論是正式或預覽狀態) 所支援系統和元件的詳細資料。

如果您在使用 Azure IoT Edge 服務時遇到問題，有幾種方式可以尋求支援。 請嘗試下列其中一個支援管道：

**報告 Bug** – Azure IoT Edge 產品大部分的開發都是在 IoT Edge 開放原始碼專案中進行。 您可以在專案的[問題頁面](https://github.com/azure/iotedge/issues)上報告錯誤。 我們也會快速地從專案中找出修正方式，並加入產品更新中。

**Microsoft 客戶支援小組** – 具有[支援方案](https://azure.microsoft.com/support/plans/)的任何使用者都可以接從 [Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)建立支援票證，與 Microsoft 客戶支援小組互動。

**功能要求** – Azure IoT Edge 產品會透過產品的 [User Voice 頁面](https://feedback.azure.com/forums/907045-azure-iot-edge)來追蹤功能要求。

## <a name="container-engines"></a>容器引擎

Azure IoT Edge 模組是當作容器實作的，因此 IoT Edge 需要容器引擎才能啟動模組。 Microsoft 提供了容器引擎 moby-engine，以滿足此需求。 此容器引擎是以 Moby 開放原始碼專案為基礎。 Docker CE 和 Docker EE 是其他常用的容器引擎。 這些引擎也是以 Moby 開放原始碼專案為基礎，而且與 Azure IoT Edge 相容。 Microsoft 盡最大努力為使用那些容器引擎的系統提供支援，不過，Microsoft 無法為系統內的問題提供修正。 基於這個理由，Microsoft 建議在生產系統上使用 moby-engine。

<br>
<center>

![當作容器執行階段的 Moby 引擎](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>作業系統

Azure IoT Edge 可以在能夠執行容器的大部分作業系統上執行，不過並非所有這些作業系統都受到相同程度的支援。 以下將作業系統分組為各種階層，代表使用者可預期的支援程度。

* 支援第 1 層系統。 針對第 1 層系統，Microsoft：
  * 在自動化的測試中有此作業系統
  * 有為它們提供安裝套件
* 第 2 層系統與 Azure IoT Edge 相容，而且使用上相對容易。 針對第 2 層系統：
  * Microsoft 已在平台上完成正式的測試，或者知道合作夥伴已在平台上成功執行過 Azure IoT Edge
  * 其他平台的安裝套件或許能在這些平台上運作

主機 OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 換句話說，您只能在 Linux 上使用 Linux 容器和在 Windows 上使用 Windows 容器。 使用 Windows 時，只支援處理程序隔離的容器，不支援 Hyper-V 隔離的容器。  

<br>
<center>

![主機 OS 符合客體 OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>第 1 層

下表所列的系統受到 Microsoft 的支援，其為正式推出或處於公開預覽狀態，並已針對每個新版本進行測試。 

| 作業系統 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公開預覽  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公開預覽 |
| [Windows 10 IoT 核心版](https://docs.microsoft.com/windows/iot-core/windows-iot-core)，組建 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT 企業版](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise)，組建 17763 | ![Windows 10 IoT 企業版 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19)，組建 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server)，組建 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

以上所列的 Windows 作業系統是在 Windows 上執行 Windows 容器的裝置需求，這是唯一支援的生產環境設定。 適用於 Windows 的 Azure IoT Edge 安裝套件可讓您在 Windows 上使用 Linux 容器，但此設定僅適用於開發及測試。 如需詳細資訊，請參閱[在 Windows 上使用 IoT Edge 執行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

### <a name="tier-2"></a>第 2 層

下表所列的系統會被視為與 Azure IoT Edge 相容，但不是由 Microsoft 主動進行測試或維護。

| 作業系統 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 系統 (包括 Raspian Buster) 使用 IoT Edge 不支援的 OpenSSL 版本。 在安裝 IoT Edge 之前，請使用下列命令安裝舊版：

```bash
sudo apt-get install libssl1.0.2
```

<sup>2</sup> [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)存放庫中的 Debian 9 套件，在 Ubuntu 20.04 中應可供使用。

## <a name="releases"></a>版次

IoT Edge 版本資產和版本資訊可在 [azure-iotedge 版本](https://github.com/Azure/azure-iotedge/releases) \(英文\) 頁面上取得。 此節會反映那些版本資訊中的資訊，以協助您更輕鬆地將每個版本的元件視覺化。

IoT Edge 元件可以個別安裝或更新，並與舊版元件回溯相容。 下表列出每個版本中所包含的元件：

| 版本 | 安全性精靈 | Edge 中樞<br>Edge 代理程式 | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.9** | 1.0.9.4<br>1.0.9.3<br>1.0.9.2 版<br>1.0.9.1<br>1.0.9 | 1.0.9.4<br>1.0.9.3<br>1.0.9.2 版<br>1.0.9.1<br>1.0.9 | 1.0.9.4<br>1.0.9.3<br>1.0.9.2 版<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl、CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge 使用 Microsoft.Azure.Device.Client SDK。 如需詳細資訊，請參閱 [Azure IoT C# SDK GitHub 存放庫](https://github.com/Azure/azure-iot-sdk-csharp) \(英文\) 或 [Azure SDK for .NET 參考內容](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) \(英文\)。 下列清單顯示測試每個版本所對照的用戶端 SDK 版本：

* **IoT Edge 1.0.9**：用戶端 SDK 1.21.1
* **IoT Edge 1.0.8**：用戶端 SDK 1.20.3
* **IoT Edge 1.0.7**：用戶端 SDK 1.20.1
* **IoT Edge 1.0.6**：用戶端 SDK 1.17.1
* **IoT Edge 1.0.5**：用戶端 SDK 1.17.1

## <a name="virtual-machines"></a>虛擬機器

Azure IoT Edge 可以在虛擬機器中執行。 當客戶想要使用邊緣智慧增強現有的基礎結構時，通常會使用虛擬機器作為 IoT Edge 裝置。 主機 VM OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 此需求與 Azure IoT Edge 直接在裝置上執行時的需求相同。 Azure IoT Edge 與基礎虛擬化技術無關，且可在 Hyper-V 和 vSphere 等平台所提供的 VM 中運作。

<br>
<center>

![VM 中的 Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最低系統需求

Azure IoT Edge 在小如 Raspberry Pi3 至伺服器等級的硬體上都能順利執行。 針對您的情節選擇正確的硬體會與您要執行的工作負載相關。 決定最終的裝置可能很複雜，不過您可以在傳統膝上型電腦或桌上型電腦上輕鬆開始建立原型解決方案。

在建立原型的同時進行體驗，有助於指引您選擇最終裝置。 您應該考慮的問題包括：

* 您的工作負載中有多少個模組？
* 您模組的容器共用有多少層？
* 您的模組是以何種語言撰寫？
* 您的模組將處理多少資料？
* 您的模組是否需要任何特殊硬體來加速其工作負載？
* 您的解決方案所需的效能特性為何？
* 您的硬體預算是多少？
