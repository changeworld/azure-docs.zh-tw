---
title: 支援的作業系統、容器引擎 - Azure IoT 邊緣
description: 了解哪些作業系統可以執行 Azure IoT Edge 精靈和執行階段，以及針對您生產環境裝置支援的容器引擎
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536933"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支援的系統

本文詳細介紹了 IoT Edge 支援哪些系統和元件（無論是官方還是預覽版）。

如果在使用 Azure IoT 邊緣服務時遇到問題，則有幾種方法可以尋求支援。 請嘗試以下管道之一獲得支援：

**報告 Bug** – Azure IoT Edge 產品大部分的開發都是在 IoT Edge 開放原始碼專案中進行。 您可以在專案的[問題頁面](https://github.com/azure/iotedge/issues)上報告錯誤。 我們也會快速地從專案中找出修正方式，並加入產品更新中。

**Microsoft 客戶支援小組** – 具有[支援方案](https://azure.microsoft.com/support/plans/)的任何使用者都可以接從 [Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)建立支援票證，與 Microsoft 客戶支援小組互動。

**功能請求**– Azure IoT 邊緣產品通過產品的["使用者語音"頁](https://feedback.azure.com/forums/907045-azure-iot-edge)跟蹤功能請求。

## <a name="container-engines"></a>容器引擎

Azure IoT 邊緣模組作為容器實現，因此 IoT Edge 需要容器引擎來啟動它們。 Microsoft 提供了容器引擎 moby-engine，以滿足此需求。 此容器引擎基於 Moby 開源專案。 Docker CE 和 Docker EE 是其他常用的容器引擎。 它們還基於 Moby 開源專案，並且與 Azure IoT 邊緣相容。 微軟為使用這些容器引擎的系統提供最大的努力支援;但是，微軟無法發佈針對其中問題的修補程式。 基於這個理由，Microsoft 建議在生產系統上使用 moby-engine。

<br>
<center>

![作為容器運行時的 Moby 引擎](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>作業系統

Azure IoT Edge 在大多數可以運行容器的作業系統上運行;但是，並非所有這些系統都得到平等支援。 以下將作業系統分組為各種階層，代表使用者可預期的支援程度。

* 支援第 1 層系統。 對於第 1 層系統，Microsoft：
  * 在自動化的測試中有此作業系統
  * 有為它們提供安裝套件
* 第 2 層系統與 Azure IoT 邊緣相容，並且使用相對容易。 對於第 2 層系統：
  * Microsoft 已在平臺上執行非正式測試，或者知道合作夥伴在平臺上成功運行 Azure IoT Edge
  * 其他平台的安裝套件或許能在這些平台上運作

主機 OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 換句話說，您只能在 Linux 上使用 Linux 容器和在 Windows 上使用 Windows 容器。 使用 Windows 時，僅支援處理隔離容器，不支援 Hyper-V 隔離容器。  

<br>
<center>

![主機 OS 符合客體 OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>第 1 層

下表中列出的系統由 Microsoft 支援，通常可用或在公共預覽版中，並隨每個新版本進行測試。 

| 作業系統 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![拉普比亞拉伸 + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 伺服器 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公開預覽  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 伺服器 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 公開預覽 |
| [Windows 10 IoT 核心](https://docs.microsoft.com/windows/iot-core/windows-iot-core)， 生成 17763 | ![視窗物聯網核心 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 物聯網企業企業](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise)， 生成 17763 | ![視窗 10 物聯網企業 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 伺服器 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19)， 版本 17763 | ![視窗伺服器 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 伺服器 IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server)， 版本 17763 | ![視窗伺服器物聯網 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

上面列出的 Windows 作業系統是在 Windows 上運行 Windows 容器的設備的要求，這是唯一受支援的生產配置。 Windows 的 Azure IoT 邊緣安裝包允許在 Windows 上使用 Linux 容器;但是，此配置僅用於開發和測試。 有關詳細資訊，請參閱在[Windows 上使用 IoT 邊緣來運行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

### <a name="tier-2"></a>第 2 層

下表中列出的系統被視為與 Azure IoT Edge 相容，但 Microsoft 不會主動測試或維護系統。

| 作業系統 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS = AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS = ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS = ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 = ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 = ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 = AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 = ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 = ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 = AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 = ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 = ARM64](./media/tutorial-c-module/green-check.png) |
| [導師嵌入式LinuxFlex作業系統](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![導師嵌入式Linux Flex作業系統 + AMD64](./media/tutorial-c-module/green-check.png) | ![導師嵌入式Linux Flex作業系統 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![導師嵌入式Linux Flex作業系統 + ARM64](./media/tutorial-c-module/green-check.png) |
| [導師嵌入式Linux全能作業系統](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![導師嵌入式Linux全功能作業系統 + AMD64](./media/tutorial-c-module/green-check.png) |  | ![導師嵌入式Linux全功能作業系統 + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 = AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 = ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 = ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![烏本圖 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![烏本圖 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![烏本圖 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![烏本圖 18.04 = ARM32v7](./media/tutorial-c-module/green-check.png) | ![烏本圖 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![風河 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![約科托 + AMD64](./media/tutorial-c-module/green-check.png) | ![約卡托 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![約克托 + ARM64](./media/tutorial-c-module/green-check.png) |
| 拉斯普比安·布斯特<sup>1</sup> |  | ![拉斯比安·布斯特 » ARM32v7](./media/tutorial-c-module/green-check.png) | ![拉斯比安·布斯特 » ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 系統（包括 Raspian Buster）使用 IoT 邊緣不支援的 OpenSSL 版本。 在安裝 IoT 邊緣之前，使用以下命令安裝早期版本：

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>版次

IoT Edge 發佈資產和版本資訊可在[azure iotedge 發佈](https://github.com/Azure/azure-iotedge/releases)頁面上提供。 本節反映來自這些版本資訊的資訊，以説明您更輕鬆地視覺化每個版本的元件。

IoT Edge 元件可以單獨安裝或更新，並且向後相容舊版本的元件。 下表列出了每個版本中包含的元件：

| 版本   | 安全守護進程  | 邊緣中心<br>邊緣代理 | 利比奧斯姆 | 莫比  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 （ARMv7hl， CentOS） |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

IoT 邊緣使用 Microsoft.Azure.Device.用戶端 SDK。 有關詳細資訊，請參閱 Azure [IoT C++ SDK GitHub 存儲庫](https://github.com/Azure/azure-iot-sdk-csharp)或[Azure SDK 中的 .NET 參考內容](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet)。 下面的清單顯示了每個版本的用戶端 SDK 版本，該版本都針對以下版本進行測試：

* **IoT 邊緣 1.0.9**： 用戶端 SDK 1.21.1
* **IoT 邊緣 1.0.8**： 用戶端 SDK 1.20.3
* **IoT 邊緣 1.0.7**： 用戶端 SDK 1.20.1
* **IoT 邊緣 1.0.6**： 用戶端 SDK 1.17.1
* **IoT 邊緣 1.0.5**： 用戶端 SDK 1.17.1

## <a name="virtual-machines"></a>虛擬機器

Azure IoT Edge 可以在虛擬機器中執行。 當客戶希望使用邊緣智慧增強現有基礎架構時，將虛擬機器用作 IoT Edge 設備很常見。 主機 VM OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 此要求與直接在設備上運行 Azure IoT Edge 時相同。 Azure IoT Edge 與基礎虛擬化技術無關，且可在 Hyper-V 和 vSphere 等平台所提供的 VM 中運作。

<br>
<center>

![VM 中的 Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最低系統需求

Azure IoT Edge 在小如 Raspberry Pi3 至伺服器等級的硬體上都能順利執行。 為方案選擇合適的硬體取決於要運行的工作負荷。 決定最終的裝置可能很複雜，不過您可以在傳統膝上型電腦或桌上型電腦上輕鬆開始建立原型解決方案。

在建立原型的同時進行體驗，有助於指引您選擇最終裝置。 您應該考慮的問題包括：

* 您的工作負載中有多少個模組？
* 模組的容器共用多少層？
* 您的模組以什麼語言編寫？
* 您的模組將處理多少資料？
* 您的模組是否需要任何專門的硬體來加速其工作負載？
* 解決方案所需的性能特徵是什麼？
* 您的硬體預算是多少？
