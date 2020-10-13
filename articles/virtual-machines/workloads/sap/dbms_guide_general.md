---
title: 適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量 | Microsoft Docs
description: 適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP，DBMS，儲存體，Ultra 磁片，Premium 儲存體
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c0158e4bdaff5400404b290e27837bfb3b95419
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974816"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png



本指南是說明如何在 Microsoft Azure 上實作和部署 SAP 軟體的部分文件。 閱讀本指南之前，請閱讀規劃指南所指向的 [規劃和執行指南][planning-guide] 和文章。 本文件說明如何使用 Azure 基礎結構即服務 (IaaS) 功能，在 Microsoft Azure 虛擬機器 (VM) 上部署 SAP 相關 DBMS 系統的一般內容。

本報告會補充說明 SAP 安裝文件和 SAP 附註，這兩份文件是在指定平台上安裝和部署 SAP 軟體的主要資源。

本文件說明在 Azure VM 中執行 SAP 相關 DBMS 系統的考量。 有數個對於本章中特定 DBMS 系統的參考資料。 本報告改在本文件後，討論特定的 DBMS 系統。

## <a name="definitions"></a>定義
本文件使用下列字詞︰

* **IaaS**：基礎結構即服務。
* **PaaS**：平台即服務。
* **SaaS**：軟體即服務。
* **SAP 元件**：個別的 SAP 應用程式，例如 ERP Central Component (ECC)、Business Warehouse (BW)、Solution Manager 或企業版入口網站 (EP)。 SAP 元件可以傳統 ABAP 或 Java 技術為基礎，或以非 NetWeaver 應用程式 (例如商務物件) 為基礎。
* **SAP 環境**：一或多個以邏輯方式分組的 SAP 元件，可執行如開發、品質保證、訓練、災害復原或生產等商務功能。
* **SAP 環境**︰此詞彙是指客戶 IP 環境中的整個 SAP 資產。 SAP 環境包括所有生產和非生產環境。
* **SAP 系統**︰DBMS 層與應用程式層的組合，例如 SAP ERP 開發系統、SAP Business Warehouse 測試系統，或 SAP CRM 生產系統。 Azure 部署不支援將這兩層區分為內部部署和 Azure。 因此，SAP 系統可為內部部署，也可部署在 Azure 中。 您可以不同的 SAP 環境系統部署於 Azure 或內部部署。 例如，您可將 SAP CRM 開發和測試系統部署在 Azure 中，但將 SAP CRM 生產系統部署在內部部署。
* **跨單位**：描述將 VM 部署到在內部部署資料中心與 Azure 之間具有站對站、多網站或 Azure ExpressRoute 連線能力的 Azure 訂閱的案例。 在一般 Azure 文件中，這類部署也會描述為跨單位案例。 

    連線的原因是為了將內部部署網域、內部部署 Active Directory 和內部部署 DNS 擴充到 Azure。 內部部署的架構會擴充到訂用帳戶的 Azure 資產。 透過此擴充，VM 可以是內部部署網域的一部分。 內部部署網域的網域使用者可存取伺服器，並可在這些 VM 上執行服務，例如 DBMS 服務。 您可以在內部部署的 VM 與 Azure 中部署的 VM 之間進行通訊與名稱解析。 在 Azure 上部署 SAP 資產時最常使用這種案例。 如需詳細資訊，請參閱[規劃與設計 VPN 閘道](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)。

> [!NOTE]
> 跨單位 SAP 系統部署是 Azure 虛擬機器執行 SAP 系統的位置，這些 VM 是內部部署網域的成員，且可生產 SAP 系統。 跨單位組態可將部分或完整 SAP 環境部署到 Azure。 即使在 Azure 中執行完整的 SAP 環境，這些 VM 也必須隸屬於內部部署網域和 Active Directory/LDAP。 
>
> 舊版文件中已提及混合式 IT 案例。 「混合式」一詞的根源為內部部署與 Azure 間有跨單位的連線能力。 在這種情況下，混合式也表示 Azure 中 VM 是內部部署 Active Directory 的一部分。
>
>

有些 Microsoft 文件在描述跨單位案例時會略有不同，特別是針對 DBMS 高可用性設定。 在 SAP 相關的文件中，跨單位案例會縮減為站對站或私人 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 連線能力，以及分散至內部部署與 Azure 之間的 SAP 環境。

## <a name="resources"></a>資源
Azure 提供其他有關 SAP 工作負載的文章。 開始[在 Azure 上 使用 SAP 工作負載：](./get-started.md)，然後選擇感興趣的區域。

下列 SAP 附註在本文件所討論的領域上與 Azure 上的 SAP 有關。

| 附註編號 | Title |
| --- | --- |
| [1928533] |Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型 |
| [2015553] |Microsoft Azure 上的 SAP：支援的必要條件 |
| [1999351] |對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2178632] |Microsoft Azure 上適用於 SAP 的主要監視度量 |
| [1409604] |Windows 上的虛擬化：增強型監視 |
| [2191498] |Linux 上搭配 Azure 的 SAP：增強型監視 |
| [2039619] |Microsoft Azure 上使用 Oracle Database 的 SAP 應用程式︰支援的產品和版本 |
| [2233094] |DB6：Azure 上使用 Linux、UNIX 和 Windows 版 IBM DB2 的 SAP 應用程式：其他資訊 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 授權問題 |
| [1984787] |SUSE LINUX Enterprise Server 12：安裝注意事項 |
| [2002167] |Red Hat Enterprise Linux 7.x：安裝與升級 |
| [2069760] |Oracle Linux 7.x SAP 安裝和升級 |
| [1597355] |適用於 Linux 的交換空間建議 |
| [2171857] |Oracle Database 12c：Linux 上的檔案系統支援 |
| [1114181] |Oracle Database 11g：Linux 上的檔案系統支援 |


如需所有適用於 Linux 的 SAP 附註相關資訊，請參閱 [SAP 社區 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

您需要有關 Microsoft Azure 架構以及如何部署與操作 Microsoft Azure 虛擬機器的有效知識。 如需詳細資訊，請參閱 [Azure 文件](../../../index.yml)。

一般而言，Windows、Linux 和 DBMS 的安裝和設定基本上與要安裝內部部署的任何虛擬機器或裸機機器相同。 但有一些架構和系統管理實作決策，與使用 Azure IaaS 時不同。 本文件會說明使用 Azure IaaS 時，您必須準備面對的特定架構和系統管理差異。


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>用於 RDBMS 部署的 VM 儲存體結構
若要遵循本章節，請閱讀並瞭解中所提供的資訊：

- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](./planning-guide.md)
- [SAP 工作負載的 Azure 儲存體類型](./planning-guide-storage.md)
- [Azure 部署支援哪些 SAP 軟體](./sap-supported-product-on-azure.md)
- [Azure 虛擬機器支援案例上的 SAP 工作負載](./sap-planning-supported-configurations.md) \(部分機器翻譯\) 

閱讀本章之前，您必須先了解及認識不同 VM 系列以及標準和進階儲存體之間的差異。 

針對 Azure 區塊儲存體，強烈建議使用 Azure 受控磁片。 如需 Azure 受控磁片的詳細資訊，請參閱 [Azure vm 的受控磁片簡介](../../managed-disks-overview.md)文章。

基本設定通常建議以下的部署結構：作業系統、DBMS 和與資料庫檔案分隔開的最終 SAP 二進位檔。 變更先前的建議時，建議您針對下列各有不同的 Azure 磁片：

- 作業系統 (基礎 VHD 或 OS VHD) 
- 資料庫管理系統可執行檔
- SAP 可執行檔（例如/usr/sap）

將這些元件分隔在三個不同 Azure 磁片中的設定可能會導致更高的復原能力，因為 DBMS 或 SAP 可執行檔的大量寫入記錄或傾印寫入不會干擾 OS 磁片的磁片配額。 

DBMS 資料和交易/重做記錄檔會儲存在 Azure 支援的區塊儲存體或 Azure NetApp Files 中。 其會儲存在不同的磁碟中，並以邏輯磁碟形式連接到原始的 Azure 作業系統映像 VM。 若為 Linux 部署，則會記錄不同的建議，尤其是 SAP Hana。 請參閱這篇 Azure 儲存體文章，以瞭解適用于這些功能的 [SAP 工作負載類型](./planning-guide-storage.md) ，以及您案例的不同儲存體類型支援。 

當規劃磁碟配置時，請找出這些項目之間的最佳平衡：

* 資料檔案數量。
* 包含檔案的磁碟數目。
* 單一磁片或 NFS 共用的 IOPS 配額。
* 每個磁片或 NFS 共用的資料輸送量。
* 每個 VM 大小可能的額外資料磁碟數目。
* VM 可以提供的整體儲存體或網路輸送量。
* 不同的 Azure 儲存體類型可以提供的延遲。
* VM SLA。

Azure 會針對每個資料磁片或 NFS 共用強制執行 IOPS 配額。 這些配額不同于裝載于不同 Azure 區塊儲存體解決方案或共用的磁片。 在這些不同的儲存體類型之間，i/o 延遲也會不同。 

VM 類型不同，可連接的資料磁碟數目限制也各不相同。 另一項限制是只有特定 VM 類型可以使用，例如高階儲存體。 一般是根據 CPU 和記憶體需求來決定使用某種 VM 類型。 您也可以考慮 IOPS、延遲及磁碟輸送量需求，這些通常會隨磁碟數目或進階儲存體磁碟類型而調整。 每個磁碟所要達到的 IOPS 數目和輸送量可能會指定磁碟大小，特別是使用進階儲存體時。

> [!NOTE]
> 針對 DBMS 部署，我們建議 Azure premium 儲存體、Ultra 磁片或 Azure NetApp Files 型 NFS 共用 (僅適用于 SAP Hana) 任何資料、交易記錄或重做檔案。 無論想要部署生產或非生產系統都沒有差別。

> [!NOTE]
> 為了受益于 Azure 的 [單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，所有連接的磁片都必須是 azure premium 儲存體或 azure Ultra 磁片類型，其中包含 azure premium 儲存體)  (的基底 VHD。

> [!NOTE]
> 不支援在與 Azure 資料中心相鄰其共置第三方資料中心的儲存體硬體上託管 SAP 資料庫主要資料庫檔案，例如資料和記錄檔。 此使用案例也不支援透過裝載于 Azure Vm 中的軟體設備所提供的儲存體。 針對 SAP DBMS 工作負載，通常只支援 SAP 資料庫的資料和交易記錄檔所表示的儲存體作為原生 Azure 服務。 不同的 DBMS 可能支援不同的 Azure 儲存體類型。 如需詳細資料，請參閱[AZURE 儲存體 SAP 工作負載類型](./planning-guide-storage.md)的文章

資料庫檔案和記錄檔和重做檔案的位置，以及您使用的 Azure 儲存體類型是由 IOPS、延遲和輸送量需求所定義。 特別是為了讓 Azure premium 儲存體達到足夠的 IOPS，您可能會強制使用多個磁片，或使用較大的高階儲存體磁片。 如果使用多個磁碟，請建置跨多個磁碟的軟體等量磁碟區，其包含資料檔案或記錄/重做檔案。 在這類情況下，基礎進階儲存體磁碟的 IOPS 和磁碟輸送量，或標準儲存體磁碟的最大可達成 IOPS，都會針對所產生的等量磁碟區組累加。

如果您的 IOPS 需求超過單一 VHD 可提供的數目，請在多個 Vhd 上平衡資料庫檔案所需的 IOPS 數目。 將 IOPS 負載分散於各磁碟的最簡單方式，就是在不同的磁碟上建置軟體等量磁碟區。 然後在劃分出軟體等量磁碟區的 LUN 上放置多個 SAP DBMS 的資料檔。 Stripe 中的磁片數目是由 IOPS 需求、磁片輸送量需求和磁片區需求所驅動。


---
> ![Windows 儲存體分割][Logo_Windows] Windows
>
> 建議使用 Windows 儲存空間來建立跨多個 Azure VHD 的等量磁碟區組。 版本至少為 Windows Server 2012 R2 或 Windows Server 2016。
>
> ![Linux 儲存體分割][Logo_Linux] Linux
>
> 只支援使用 MDADM 和邏輯磁碟區管理員 (LVM) 在 Linux 上建立軟體 RAID。 如需詳細資訊，請參閱
>
> - 使用 MDADM [在 Linux 上設定軟體 RAID](../../linux/configure-raid.md) (機器翻譯)
> - 使用 LVM [在 Azure 中的 Linux VM 上設定 LVM](../../linux/configure-lvm.md)
>
>

---

針對 Azure Ultra 磁片，不需要等量，因為您可以定義與磁片大小無關的 IOPS 和磁片輸送量。


> [!NOTE]
> 因為 Azure 儲存體會保留三個 VHD 映像，所以在建立等量磁碟區時沒必要設定備援。 您只需要設定等量磁碟區，讓 I/O 分散到不同的 VHD 即可。
>

### <a name="managed-or-nonmanaged-disks"></a>受控或非受控磁碟
Azure 儲存體帳戶是系統管理建構，也是限制的緣由。 標準儲存體帳戶和進階儲存體帳戶之間的限制有所不同。 如需功能和限制的相關資訊，請參閱[Azure 儲存體的可擴縮性和效能目標](../../../storage/common/scalability-targets-standard-account.md) (機器翻譯)。

請記住標準儲存體對每個儲存體帳戶都有 IOPS 限制。 請參閱 [Azure 儲存體的可擴縮性和效能目標](../../../storage/common/scalability-targets-standard-account.md) (機器翻譯) 一文中包含**總要求率**的資料列。 每個 Azure 訂閱也有儲存體帳戶數目的初始限制。 將較大 SAP 環境的 VHD 平均分散到不同儲存體帳戶，可避免達到這些儲存體帳戶的限制。 討論包含上千個 VHD 的數百部虛擬機器，是很乏味的工作。

不建議將適用于 DBMS 部署的標準儲存體與 SAP 工作負載搭配使用。 因此，對標準儲存體的參考和建議僅限於這篇簡短的 [文章](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)

為避免跨不同 Azure 儲存體帳戶的 VHD 規劃及部署系統管理工作，Microsoft 在 2017 年引進了 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 標準儲存體和進階儲存體皆可使用受控磁碟。 相較於非受控磁碟，受控磁碟的主要優點如下：

- 針對受控磁片，Azure 會在部署階段自動將不同的 VHD 分散到不同儲存體帳戶。 如此一來，就不會達到儲存體帳戶的資料量、I/O 輸送量和 IOPS 限制。
- 使用受控磁片，Azure 儲存體會接受 Azure 可用性設定組的概念。 如果 VM 屬於 Azure 可用性設定組，則 VM 的基底 VHD 和連接磁碟就會部署到不同容錯和更新網域。


> [!IMPORTANT]
> 基於 Azure 受控磁碟的優點，我們強烈建議您在一般情況下使用 Azure 受控磁碟來進行 DBMS 部署和 SAP 部署。
>

若要從非受控磁碟轉換為受控磁碟，請參閱：

- [將 Windows 虛擬機器從非受控磁碟轉換成受控磁碟](../../windows/convert-unmanaged-to-managed-disks.md) (機器翻譯)。
- [將 Linux 虛擬機器從非受控磁碟轉換成受控磁碟](../../linux/convert-unmanaged-to-managed-disks.md) (機器翻譯)。


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和資料磁碟的快取
當將磁碟掛接到 VM 時，可選擇是否快取在 VM 與位於 Azure 儲存體磁碟間的 I/O 流量。

下列建議假設標準 DBMS 的 I/O 具有以下特性：

- 主要是針對資料庫資料檔案的讀取工作負載。 這些讀取對 DBMS 系統的效能有重大影響。
- 根據檢查點或持續串流，以針對資料檔案的寫入會發生高載。 平均超過一天，寫入次數就會少於讀取次數。 相對於來自資料檔案的讀取，這些寫入為非同步，且不會造成任何使用者交易延遲。
- 幾乎不會從交易記錄或重做檔案進行任何讀取。 例外狀況是在執行交易記錄備份時會有大量 I/O。
- 交易或重做記錄檔的主要負載是寫入。 根據工作負載的性質而定， I/O 可小到 4 KB，但在其他情況下，I/O 大小也可以大到 1 MB 或更大。
- 所有寫入都必須以可靠的方式保存在磁碟上。

標準儲存體可能有以下快取類型︰

* None
* 讀取
* 讀取/寫入

為取得具決定性的一致效能，請將包含 DBMS 相關資料檔、記錄/重做檔和資料表空間的所有磁碟標準儲存體快取設定為 [無]。 基底 VHD 的快取可以保留預設值。

針對 Azure premium 儲存體，有下列快取選項：

* None
* 讀取
* 讀取/寫入
* 無 + 寫入加速器，僅適用於 Azure M 系列 VM
* 讀取 + 寫入加速器，僅適用於 Azure M 系列 VM

針對進階儲存體，建議使用 SAP 資料庫的 [資料檔案讀取快取]，並選擇 [No caching for the disks of log file] \(不快取記錄檔磁碟\)。

針對 M 系列部署，建議對 DBMS 部署使用 Azure 寫入加速器。 如需 Azure 寫入加速器的詳細資料、限制和部署，請參閱[啟用寫入加速器](../../how-to-enable-write-accelerator.md) (機器翻譯)。

若為 Ultra 磁片和 Azure NetApp Files，則不會提供任何快取選項。


### <a name="azure-nonpersistent-disks"></a>Azure 非永久性磁碟
Azure VM 會在部署 VM 之後提供非永久性磁碟。 如果 VM 重新開機，就會抹除這些磁碟機上的所有內容。假設在任何情況下，資料庫的資料檔和記錄/重做檔都不得位於這些非永久性磁碟機上。 有些資料庫可能會出現一些非永久性磁碟機適用於 tempdb 和暫存資料表空間例外狀況。 請避免將這些磁碟機使用於 A 系列 VM，因為這些非永久性磁碟機對該 VM 系列的輸送量有所限制。 

如需詳細資訊，請參閱[了解 Windows Azure VM 上的暫存磁碟機](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines) (英文)。

---
> ![Windows 非保存磁片][Logo_Windows] Windows
>
> Azure VM 的 D 磁碟機是非永久性磁碟機，其支援 Azure 計算節點上的一些本機磁碟。 因為是非永久性的磁碟機，所以當 VM 重新開機時，D 磁碟機會遺失所有的內容變更。 這些變更包括曾經儲存的檔案、曾經建立的目錄，以及曾經安裝的應用程式。
>
> ![Linuxnonpersisted 磁片][Logo_Linux] Linux
>
> Linux Azure VM 會在 /mnt/resource 自動掛接磁碟機，Azure 計算節點上的許多本機磁碟支援這個非永久性的磁碟機。 因為是非永久性的磁碟機，所以當 VM 重新開機時，/mnt/resource 中的所有內容變更都會遺失。 這些變更包括曾經儲存的檔案、曾經建立的目錄，以及曾經安裝的應用程式。
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 儲存體復原
Microsoft Azure 儲存體會將基底 VHD 和 OS 以及連接的磁碟或 BLOB 一起儲存在至少 3 個不同儲存體節點上。 這種類型的儲存體稱為本機備援儲存體 (LRS)。 LRS 是 Azure 中所有儲存體類型的預設值。

還有其他的備援方法。 如需詳細資訊，請參閱 [Azure 儲存體複寫](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

> [!NOTE]
> Azure premium 儲存體、Ultra 磁片和 Azure NetApp Files (專用於 SAP Hana) 是儲存資料庫和記錄檔和重做檔案的 DBMS Vm 和磁片的建議儲存體類型。 這些儲存體類型唯一可用的重複方法是 LRS。 因此，您必須設定資料庫方法，讓資料庫資料能複寫到另一個 Azure 區域或可用性區域。 資料庫方法包括 SQL Server Always On、Oracle Data Guard 及 HANA System Replication。


> [!NOTE]
> 針對 DBMS 部署，不建議對標準儲存體使用異地備援儲存體 (GRS)。 GRS 會嚴重影響效能，也不接受連接至 VM 的不同 VHD 寫入順序。 不接受不同 VHD 的寫入順序，可能會導致複寫目標端的資料庫不一致。 如果資料庫和記錄檔及重做檔分散到來源 VM 端的多個 VHD (通常是如此)，就會發生這種情況。



## <a name="vm-node-resiliency"></a>VM 節點復原
Azure 會為 VM 提供數個不同的 SLA。 如需詳細資訊，請參閱最新版的[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 因為 DBMS 層對於 SAP 系統中的可用性而言很重要，所以您必須瞭解可用性設定組、可用性區域和維護事件。 如需這些概念的詳細資訊，請參閱[在 Azure 中管理 Windows 虛擬機器的可用性](../../manage-availability.md) (機器翻譯) 以及[在 Azure 中管理 Linux 虛擬機器的可用性](../../manage-availability.md) (機器翻譯)。

針對包含 SAP 工作負載的生產 DBMS 案例，最低建議如下：

- 在相同 Azure 區域中，將兩部 VM 部署在不同的可用性設定組中。
- 在相同 Azure 虛擬網路中執行這兩部 VM，並連接出自同一子網路的 NIC。
- 使用資料庫方法讓第二部 VM 保持熱待命。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA System Replication。

您也可以在另一個 Azure 區域中部署第三部 VM，使用相同的資料庫方法在另一個 Azure 區域中提供非同步複本。

如需如何設定 Azure 可用性設定組的相關資訊，請參閱[本教學課程](../../windows/tutorial-availability-sets.md)。



## <a name="azure-network-considerations"></a>Azure 網路考量事項
在大規模的 SAP 部署中，請使用 [Azure 虛擬資料中心](/azure/architecture/vdc/networking-virtual-datacenter)藍圖。 將此藍圖用於虛擬網路設定以及組織不同單位的權限和角色指派。

這些最佳做法是數百個客戶部署的結果：

- SAP 應用程式部署所在的虛擬網路無法存取網際網路。
- 資料庫 Vm 會在與應用層相同的虛擬網路中執行，並與 SAP 應用層不同的子網分開。
- 虛擬網路內 VM 具有私人 IP 位址的靜態配置。 如需詳細資訊，請參閱 [Azure 中的 IP 位址類型及配置方法](../../../virtual-network/public-ip-addresses.md)。
- DBMS VM 的往返路由限制「不會」與安裝在本機 DBMS VM 上的防火牆一起設定。 流量路由反而會與 [網路安全性群組 (NSG)](../../../virtual-network/network-security-groups-overview.md) (機器翻譯) 一起定義。
- 若要將流量分開並隔離到 DBMS VM，請將不同的 NIC 指派給 VM。 每個 NIC 都會取得不同 IP 位址，且每個 NIC 會指派給不同的虛擬網路子網路。 每個子網路都有不同的 NSG 規則。 隔離或分開網路流量是一種測量路由的方法。 不會用來設定網路輸送量配額。

> [!NOTE]
> 透過 Azure 指派靜態 IP 位址，表示將這些位址指派給個別的虛擬 NIC。 請不要將客體 OS 內的靜態 IP 位址指派給虛擬 NIC。 有些像 Azure 備份這樣的 Azure 服務，依賴主要虛擬 NIC 至少會設為 DHCP，而不是設為靜態 IP 位址的事實。 如需詳細資訊，請參閱[針對 Azure 虛擬機器的備份進行疑難排解](../../../backup/backup-azure-vms-troubleshoot.md#networking)。 若要將多個靜態 IP 位址指派給 VM，請將多個虛擬 NIC 指派給 VM。
>


> [!WARNING]
> 不支援在 SAP 應用程式與 SAP NetWeaver、Hybris 或 S/4HANA 型 SAP 系統的 DBMS 層之間的通訊路徑中設定[網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)。 因功能和效能原因而有此限制。 SAP 應用程式層和 DBMS 層之間必須有直接的通訊路徑。 如果 ASG 和 NSG 規則允許直接通訊路徑，此限制即不包括這些[應用程式安全性群組 (ASG) 和 NSG 規則](../../../virtual-network/network-security-groups-overview.md)。 
>
> 其他不支援網路虛擬設備的案例出現在：
>
> * Azure VM 之間的通訊路徑，這些 VM 代表 Linux Pacemaker 叢集節點與 SBD 裝置，如 [SAP NetWeaver 在適用於 SAP 應用程式的 SUSE Linux Enterprise Server 上 Azure VM 高可用性](./high-availability-guide-suse.md)中所述。
> * Azure VM 與 Windows Server 向外延展檔案伺服器 (SOFS) 之間的通訊路徑，其設定如[在 Azure 中使用檔案共用於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-file-share.md)中所述。 
>
> 通訊路徑中網路虛擬設備可輕鬆加倍兩個通訊合作夥伴之間的網路延遲。 也可以限制 SAP 應用程式層與 DBMS 層間重要路徑中的輸送量。 有些客戶案例中的網路虛擬設備會導致 Pacemaker Linux 叢集失敗。 這些案例都是 Linux Pacemaker 叢集節點與其 SBD 裝置透過網路虛擬設備通訊的情況。
>

> [!IMPORTANT]
> 另一種「不」支援的設計，是將 SAP 應用程式層與 DBMS 層隔離到彼此之間不[對等互連](../../../virtual-network/virtual-network-peering-overview.md)的不同 Azure 虛擬網路。 建議使用 Azure 虛擬網路內的子網路來隔離 SAP 應用程式層和 DBMS 層，不要使用不同的 Azure 虛擬網路。 
>
> 如果決定不遵循建議，且堅持要使用不同的虛擬網路來隔離兩個層，則這兩個虛擬網路必須要[對等互連](../../../virtual-network/virtual-network-peering-overview.md)。 
>
> 請注意，兩個[對等互連](../../../virtual-network/virtual-network-peering-overview.md)的 Azure 虛擬網路間網路流量會產生傳輸成本。 SAP 應用程式層和 DBMS 層之間會交換多達數 TB 的龐大資料量。 如果 SAP 應用程式層和 DBMS 層分隔在兩個對等互連的 Azure 虛擬網路，則會累積大量的費用。

在 Azure 可用性設定組內或兩個 Azure 可用性區域之間，使用兩個 Vm 進行生產 DBMS 部署。 另請為 SAP 應用程式層以及兩部 DBMS VM 的管理和作業流量使用不同路由。 請見下圖：

![兩個子網路中兩部 VM 的圖表](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure Load Balancer 重新導向流量
您必須設定 Azure Load Balancer，才能使用在 SQL Server Always On 或 HANA System Replication 等功能中使用的私人虛擬 IP 位址。 負載平衡器使用探查連接埠來判斷作用中 DBMS 節點，並以獨佔方式將流量路由至該作用中的資料庫節點。 

如果資料庫節點有容錯移轉，您就不需要重新設定 SAP 應用程式。 然而，最常見的 SAP 應用程式架構會重新連線私人虛擬 IP 位址。 同時，負載平衡器會將私人虛擬 IP 位址的流量重新導向至第二個節點，藉此回應節點的容錯移轉。

Azure 提供兩個不同的[負載平衡器 SKU](../../../load-balancer/load-balancer-overview.md)：基本 SKU 和標準 SKU。 根據設定和功能的優點，您應該使用 Azure 負載平衡器的標準 SKU。 負載平衡器標準版本的其中一個優點是，資料流量不會透過負載平衡器本身路由傳送。

如需如何設定內部負載平衡器的範例，請參閱 [教學課程：在 Azure 虛擬機器上手動設定 SQL Server 可用性群組](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial.md#create-an-azure-load-balancer)

> [!NOTE]
> 與公用 IP 位址的存取相關的基本和標準 SKU 行為有所差異。 [在 SAP 高可用性案例中使用 Azure Standard Load Balancer 的虛擬機器檔公用端點](./high-availability-guide-standard-load-balancer-outbound-connections.md)連線中，說明如何解決標準 SKU 存取公用 IP 位址的限制。


### <a name="azure-accelerated-networking"></a>Azure 加速網路
若要進一步降低 Azure VM 之間的網路延遲，建議選擇 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 請在針對 SAP 工作負載部署 Azure VM 時使用，特別是針對 SAP 應用程式層和 SAP DBMS 層。

> [!NOTE]
> 並非所有的 VM 類型都支援加速網路。 上一篇文章會列出支援加速網路的 VM 類型。
>

---
> ![Windows 加速網路][Logo_Windows] Windows
>
> 若要了解如何部署使用加速網路的 Windows VM，請參閱[建立使用加速網路的 Windows 虛擬機器](../../../virtual-network/create-vm-accelerated-networking-powershell.md)。
>
> ![Linux 加速網路][Logo_Linux] Linux
>
> 如需 Linux 散發套件的詳細資訊，請參閱[建立使用加速網路的 Linux 虛擬機器](../../../virtual-network/create-vm-accelerated-networking-cli.md)。
>
>

---

> [!NOTE]
> 如果是 SUSE、Red Hat 和 Oracle Linux，最近版本都支援加速網路。 SLES 12 SP2 或 RHEL 7.2 等較舊版本不支援 Azure 加速網路。
>


## <a name="deployment-of-host-monitoring"></a>部署主機監視功能
若要使 Azure 虛擬機器中的 SAP 應用程式發揮產能，則 SAP 需要能夠從執行 Azure 虛擬機器的實際主機取得主機監視資料。 需要有特定的 SAP HostAgent 修補程式等級，才能在 SAPOSCOL 和 SAP HostAgent 中啟用此功能。 確切的修補程式等級記載於 SAP 附註 [1409604]。

如需部署會傳遞主機資料給 SAPOSCOL 和 SAP 主機代理程式的元件，以及其生命週期管理的詳細資訊，請參閱[部署指南][deployment-guide]。


## <a name="next-steps"></a>後續步驟
如需特定 DBMS 的詳細資訊，請參閱：

- [適用於 SAP 工作負載的 SQL Server Azure 虛擬機器 DBMS 部署](dbms_guide_sqlserver.md)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](dbms_guide_oracle.md)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](dbms_guide_ibm.md)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](dbms_guide_sapase.md)
- [Azure 上的 SAP maxDB、Live Cache 和 Content Server 部署](dbms_guide_maxdb.md)
- [Azure 上的 SAP HANA 作業指南](hana-vm-operations.md)
- [Azure 虛擬機器的 SAP Hana 高可用性](sap-hana-availability-overview.md)
- [Azure 虛擬機器上的 SAP HANA 備份指南](sap-hana-backup-guide.md)