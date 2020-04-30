---
title: Azure 背書的散發套件
description: 了解在 Azure 背書散發套件上的 Linux，包括 Ubuntu、CentOS、Oracle 和 SUSE 的準則。
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: ed7755251feb04a5f811d6ed96b00a347fba8994
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605915"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure 上背書的 Linux 散發套件
合作夥伴會在 Azure Marketplace 中提供 Linux 映像。 我們與各個 Linux 社群合作，以便為經背書的發佈清單新增更多版本選項。 在此同時，針對 Marketplace 未提供的發佈，您一律可以將自己的 Linux 帶入，方法是遵循[建立和上傳包含 Linux 作業系統的虛擬硬碟](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)中的指導方針。

## <a name="supported-distributions-and-versions"></a>支援的發佈和版本
下表列出 Azure 上支援的 Linux 散發套件和版本。 如需 Azure 中 Linux 和開放原始碼技術支援的詳細資訊，請參閱[Microsoft Azure 中的 linux 映射支援](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)。

適用於 Hyper-V 和 Azure 的 Linux Integration Services (LIS) 驅動程式是核心模組，Microsoft 會直接提供給上游 Linux Kernel。  根據預設，有些 LIS 驅動程式會建置到發佈的核心。 以 Red Hat Enterprise (RHEL)/CentOS 作為基礎的較舊分佈可用在[適用於 Hyper-V 和 Azure 的 Linux 整合服務 4.2 版](https://www.microsoft.com/download/details.aspx?id=55106)作為個別下載。 如需關於 LIS 驅動程式的詳細資訊，請參閱 [Linux 核心需求](create-upload-generic.md#linux-kernel-requirements)。

Azure Linux 代理程式已預先安裝於 Azure Marketplace 映像上，而且通常可透過發佈的套件存放庫來取得。 您可以在 [GitHub](https://github.com/azure/walinuxagent)上找到原始程式碼。


| 散發 | 版本 | 驅動程式 | 代理程式 |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +、7.0 +、8.0 + |CentOS 6.3： [.lis 下載](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +：在核心中 |封裝[：在 "WALinuxAgent" 下的](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)存放庫中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |在核心中 |原始程式碼：[GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +、8.2 +、9、10 |在核心中 |套件：在「waagent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+、7.0+ |在核心中 |套件：在「WALinuxAgent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 7.1 +、8.0 + |在核心中 |套件：在「WALinuxAgent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES for SAP<br>11 SP4<br>12 SP1+<br>15|在核心中 |套件：<p> 適用於 11：在 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 儲存機制中<br>適用於 12：包含在 "Public Cloud" 模組中的 "python-azure-agent" 底下<br/>原始程式碼：[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |在核心中 |封裝：在 "python-azure-agent" 下的[雲端：工具](https://build.opensuse.org/project/show/Cloud:Tools)儲存機制中 <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |在核心中 |套件：在「WALinuxAgent」下的儲存機制中  <br/>原始程式碼：[GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** ubuntu 12.04 和14.04 擴充支援的相關資訊可在以下位置找到： [Ubuntu 擴充安全性維護](https://www.ubuntu.com/esm)。


## <a name="image-update-cadence"></a>映射更新步調
Azure 要求經背書的 Linux 散發套件發行者定期以最新的修補程式和安全性修正程式更新其映射 Azure Marketplace，每季或更快的步調。 Azure Marketplace 中的已更新映射會自動提供給客戶，做為映射 SKU 的新版本。 有關如何尋找 Linux 映射的詳細資訊：[在 Azure Marketplace 中尋找 LINUX VM 映射](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage)。

### <a name="additional-links"></a>其他連結
 - [SUSE 公用雲端映射生命週期](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Azure 微調的核心

Azure 與各種經背書的 Linux 散發套件密切合作，將其發佈至 Azure Marketplace 的映射優化。 此共同作業的其中一個層面是開發已針對 Azure 平臺優化的「微調」 Linux 核心，並以 Linux 散發套件的完整支援元件來提供。 Azure 微調的核心結合了新功能和效能改進，而且相較于散發所提供的預設或一般核心，速度更快（通常是每季）。

在大部分情況下，您會發現這些核心已預先安裝在 Azure Marketplace 的預設映射上，因此 Azure 客戶將立即獲得這些優化核心的優點。 這些 Azure 微調核心的詳細資訊可在下列連結中找到：

 - CentOS 透過 CentOS 虛擬化 SIG 提供的 Azure 微調核心-[詳細資訊](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian 雲端核心-在 Azure 上提供 Debian 10 和 Debian 9 "反向移植" 映射-[詳細資訊](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure 微調的核心-[詳細資訊](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure 微調的核心-[詳細資訊](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>合作夥伴

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

從 CoreOS 網站：

*CoreOS 是專為安全性、一致性和可靠性所設計。CoreOS 不會透過 yum 或 apt 安裝封裝，而是在較高層級的抽象概念中，使用 Linux 容器來管理您的服務。單一服務的程式碼和所有相依性都封裝於容器內，可在一或多部 CoreOS 機器上執行。*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ 是一家獨立的顧問暨服務公司，專長為使用免費的軟體來開發和實作專業解決方案。 作為業界領先的開放原始碼專家，Credativ 的支援受到許多 IT 部門所採用，從而在國際上享有認可。 Credativ 目前與 Microsoft 合作，正準備適用於 Debian 8 (Jessie) 與 7 之前的 Debian (Wheezy) 的 Debian 映像。 這兩個映像都是專為在 Azure 上執行並可透過平台輕鬆管理所設計。 Credativ 也透過其開放原始碼支援中心，長期支援維護與更新適用於 Azure 的 Debian 映像。

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是為公用和私用雲端提供廣泛的解決方案組合。 策略可讓客戶在 Oracle 雲端和其他雲端中部署 Oracle 軟體時能有所選擇且更有彈性。 Oracle 與 Microsoft 的合作關係，可讓客戶在 Microsoft 公用和私用雲端部署 Oracle 軟體，並安心提供 Oracle 的認證與支援。  Oracle 對於 Oracle 公用和私用雲端解決方案的承諾和投資不變。

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat 是開放原始碼解決方案的全球領導提供者，協助超過 90% 的 Fortune 500 大企業解決業務挑戰、調整其 IT 和商務策略，並做好技術的準備。 Red Hat 藉由透過開放的商務模型及可預測且價格實惠的訂閱模型，提供安全的解決方案來做到這點。

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

Azure 上的 SUSE Linux Enterprise Server 是一個經證實可為雲端運算提供優異可靠性與安全性的平台。 SUSE 彈性的 Linux 平台可與 Azure 雲端服務緊密整合，提供容易管理的雲端環境。 隨著 SUSE Linux Enterprise Server 有超過 1,800 家獨立軟體廠商提供 9,200 多個認證應用程式，SUSE 可確保資料中心內所執行受支援的工作負載可以安心部署於 Azure 上。

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical 對工程與開放社群的治理，促使 Ubuntu 在用戶端、伺服器和雲端運算 (包括消費者的個人雲端服務) 方面獲致成功。 Canonical 的願景是在 Ubuntu 中從手機到雲端的統一、可用平台，能提供電話、平板電腦、電視和桌上型電腦的一致介面系列。 這個願景使得 Ubuntu 成為從公用雲端提供者到消費性電子產品製造商等各大機構的優先選擇，並且是個別技術專家的最愛。

Canonical 在全球各地皆有開發人員和工程中心，此獨特的地位使其能與硬體製造商、內容供應商和軟體開發商合作，將 Ubuntu 解決方案引進電腦、伺服器到掌上型裝置等市場。
