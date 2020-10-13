---
title: Azure 背書的散發套件
description: 了解在 Azure 背書散發套件上的 Linux，包括 Ubuntu、CentOS、Oracle 和 SUSE 的準則。
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: guybo
ms.openlocfilehash: 0236644930af699180cf26b4baee7bb591d8bbaa
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978929"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure 上經背書的 Linux 發行版本

合作夥伴會在 Azure Marketplace 中提供 Linux 映射。 Microsoft 與各種 Linux 團體合作，在背書的通訊群組清單中新增更多的類別。 針對 Marketplace 中無法使用的散發套件，您可以遵循 [建立和上傳包含 linux 作業系統的虛擬硬碟](./create-upload-generic.md)上的指導方針，來自備您自己的 linux。

## <a name="supported-distributions-and-versions"></a>支援的發佈和版本

下表列出 Azure 上支援的 Linux 散發套件和版本。 如需詳細資訊，請參閱 [Microsoft Azure 中的 Linux 映射支援](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)。

適用於 Hyper-V 和 Azure 的 Linux Integration Services (LIS) 驅動程式是核心模組，Microsoft 會直接提供給上游 Linux Kernel。 根據預設，有些 LIS 驅動程式會建置到發佈的核心。 以 Red Hat Enterprise (RHEL)/CentOS 作為基礎的較舊分佈可用在[適用於 Hyper-V 和 Azure 的 Linux 整合服務 4.2 版](https://www.microsoft.com/download/details.aspx?id=55106)作為個別下載。 如需詳細資訊，請參閱 [Linux 核心需求](create-upload-generic.md#linux-kernel-requirements)。

Azure Linux 代理程式已預先安裝在 Azure Marketplace 映射上，而且通常可從散發套件的套件存放庫中取得。 您可以在 [GitHub](https://github.com/azure/walinuxagent)上找到原始程式碼。

| 散發 | 版本 | 驅動程式 | 代理程式 |
| --- | --- | --- | --- |
| 由 Rogue Wave 軟體 CentOS |CentOS 6.x、7.x、8。x |CentOS 6.3： [.lis 版下載](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +：在核心中 |封裝 [：在 "WALinuxAgent" 下的](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) 存放庫中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS 的存留 [期即將結束](https://coreos.com/os/eol/) ，從2020月26日開始。 |無法再使用 | | |
| Credativ 的 Debian |8.x、9.x |在核心中 |套件：在「waagent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
|依 Kinvolk Flatcar 容器 Linux| Pro、穩定、搶鮮版（Beta）| 在核心中 | wa-linux-代理程式已安裝在/usr/share/oem/bin/waagent 中 |
| 由 Oracle Oracle Linux |6.x、7.x、8.x |在核心中 |套件：在「WALinuxAgent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat Red Hat Enterprise Linux](../workloads/redhat/overview.md) |6.x、7.x、8.x |在核心中 |套件：在「WALinuxAgent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| Suse Linux Enterprise by SUSE |SLES/SLES for SAP 11. x、12. x、6。x <br/> [SUSE Public Cloud 映射生命週期](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |在核心中 |套件：<p> 適用於 11：在 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 儲存機制中<br>適用於 12：包含在 "Public Cloud" 模組中的 "python-azure-agent" 底下<br/>原始程式碼：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| 由 SUSE openSUSE |openSUSE Leap 15.x |在核心中 |封裝：在 "python-azure-agent" 下的[雲端：工具](https://build.opensuse.org/project/show/Cloud:Tools)儲存機制中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| 依標準的 Ubuntu |Ubuntu Server 和 Pro。 16. x、18. x、20. x<p>您可以在這裡找到 Ubuntu 12.04 和14.04 延伸支援的相關資訊： [Ubuntu 擴充安全性維護](https://www.ubuntu.com/esm)。 |在核心中 |套件：在「WALinuxAgent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>映射更新步調

Azure 需要經背書之 Linux 散發套件的發行者定期以每季或更快的頻率，以最新的修補程式和安全性修正程式更新其 Azure Marketplace 的映射。 新版本的映射 SKU 會自動將 Marketplace 中的更新映射提供給客戶。 有關如何尋找 Linux 映射的詳細資訊： [在 Azure Marketplace 中尋找 LINUX VM 映射](./cli-ps-findimage.md)。

## <a name="azure-tuned-kernels"></a>Azure 調整的核心

Azure 與各種背書的 Linux 散發套件密切合作，以將其發佈至 Azure Marketplace 的映射優化。 這項共同作業的其中一個層面是開發已針對 Azure 平臺優化的「微調」 Linux 核心，並以 Linux 散發套件的完整支援元件形式提供。 Azure-Tuned 核心併入了新功能和效能改進，相較于可從散發套件取得的預設或一般核心，通常會有每季) 步調較快的 (。

在大多數情況下，您會發現這些核心已預先安裝在 Azure Marketplace 的預設映射中，因此客戶會立即獲得這些優化核心的優點。 您可以在下列連結中找到這些 Azure-Tuned 核心的詳細資訊：

- [CentOS Azure-Tuned 核心-可透過 CentOS Virtualization SIG 取得](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian Cloud Kernel-可在 Azure 上使用 Debian 10 和 Debian 9 "反向移植" 映射](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure-Tuned 核心](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure-Tuned 核心](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Flatcar 容器 Linux Pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>合作夥伴

### <a name="coreos"></a>CoreOS

CoreOS 已排程為 [生命週期的生命週期](https://coreos.com/os/eol/) ，2020 5 月26日結束。
針對 CoreOS 使用者，Microsoft 有兩個 (2) 的遷移通道。

- 依 Kinvolk (的 Flatcar，請參閱「Flatcar 容器 Linux by Kinvolk」專案。 ) 
- [Fedora 核心作業系統](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (客戶必須上傳自己的映射。 以下是) 的 [遷移檔](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/) 。

### <a name="credativ"></a>Credativ

[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ 是一家獨立的顧問暨服務公司，專長為使用免費的軟體來開發和實作專業解決方案。 作為業界領先的開放原始碼專家，Credativ 的支援受到許多 IT 部門所採用，從而在國際上享有認可。 Credativ 目前與 Microsoft 合作，正準備適用於 Debian 8 (Jessie) 與 7 之前的 Debian (Wheezy) 的 Debian 映像。 這兩個映像都是專為在 Azure 上執行並可透過平台輕鬆管理所設計。 Credativ 也透過其開放原始碼支援中心，長期支援維護與更新適用於 Azure 的 Debian 映像。

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk 是 Flatcar 容器 Linux 後方的公司，針對容器化應用程式，繼續進行原始 CoreOS 願景，以獲得最基本、不可變和自動更新的基礎。 在基本發行版本中，Flatcar 只包含部署容器所需的套件。 它不可變的檔案系統可保證一致性和安全性，同時也可讓您隨時掌握最新的安全性修正程式，以保持最新狀態。 

Flatcar Container Linux 是由 Kinvolk 的全球 Linux 和容器技術專家團隊所備份，這些專家會提供選擇性的商業支援訂用帳戶，其中包括全天候回應、安全性和技術警示，以及專屬的 Azure 優化映射（包括長期支援通道）。


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是為公用和私用雲端提供廣泛的解決方案組合。 策略可讓客戶在 Oracle 雲端和其他雲端中部署 Oracle 軟體時能有所選擇且更有彈性。 Oracle 與 Microsoft 的合作關係，可讓客戶在 Microsoft 公用和私人雲端中部署 Oracle 軟體，並信賴 Oracle 的認證和支援。  Oracle 對 Oracle 公用和私用雲端解決方案的承諾和投資沒有改變。

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat 是世界頂尖的開放原始碼解決方案提供者，可協助超過90% 的財富500公司解決商務挑戰、使 IT 和商務策略更一致，以及為技術未來做好準備。 Red Hat 透過開放式商務模型和經濟實惠且可預測的訂用帳戶模型，提供安全的解決方案來達成此結果。

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

Azure 上的 SUSE Linux Enterprise Server 是一個經證實可為雲端運算提供優異可靠性與安全性的平台。 SUSE 彈性的 Linux 平台可與 Azure 雲端服務緊密整合，提供容易管理的雲端環境。 隨著 SUSE Linux Enterprise Server 有超過 1,800 家獨立軟體廠商提供 9,200 多個認證應用程式，SUSE 可確保資料中心內所執行受支援的工作負載可以安心部署於 Azure 上。

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical 對工程與開放社群的治理，促使 Ubuntu 在用戶端、伺服器和雲端運算 (包括消費者的個人雲端服務) 方面獲致成功。 Canonical 的願景是在 Ubuntu 中從手機到雲端的統一、可用平台，能提供電話、平板電腦、電視和桌上型電腦的一致介面系列。 這個願景使得 Ubuntu 成為從公用雲端提供者到消費性電子產品製造商等各大機構的優先選擇，並且是個別技術專家的最愛。

Canonical 在全球各地皆有開發人員和工程中心，此獨特的地位使其能與硬體製造商、內容供應商和軟體開發商合作，將 Ubuntu 解決方案引進電腦、伺服器到掌上型裝置等市場。