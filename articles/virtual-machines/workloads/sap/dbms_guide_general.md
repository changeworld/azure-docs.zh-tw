---
title: 適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量 | Microsoft Docs
description: 適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "70101355"
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


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

本指南是有關如何在 Microsoft Azure 上執行和部署 SAP 軟體之檔的一部分。 閱讀本指南之前，請先閱讀[規劃與實施指南][planning-guide]。 本檔涵蓋使用 Azure 基礎結構即服務（IaaS）功能，Microsoft Azure 虛擬機器（Vm）上 SAP 相關 DBMS 系統的一般部署層面。

本檔會補充 SAP 安裝檔和 SAP 附注，這代表在指定平臺上安裝和部署 SAP 軟體的主要資源。

本文件說明在 Azure VM 中執行 SAP 相關 DBMS 系統的考量。 有數個對於本章中特定 DBMS 系統的參考資料。 相反地，在本檔之後，會在本文中處理特定的 DBMS 系統。

## <a name="definitions"></a>定義
在整份檔中，會使用這些詞彙：

* **IaaS**：基礎結構即服務。
* **PaaS**：平臺即服務。
* **SaaS**：軟體即服務。
* **Sap 元件**：個別的 sap 應用程式，例如 ERP Central COMPONENT （ECC）、Business 倉儲（BW）、Solution Manager 或企業版入口網站（EP）。 SAP 元件可以根據傳統的 ABAP 或 JAVA 技術，或是在非 NetWeaver 架構的應用程式（例如商務物件）上。
* **Sap 環境**：一或多個以邏輯方式分組的 SAP 元件，可執行開發、品質保證、訓練、嚴重損壞修復或生產等商務功能。
* **SAP 環境**：此詞彙是指客戶的 IT 環境中的整個 SAP 資產。 SAP 環境包含所有生產和非生產環境。
* **Sap 系統**： DBMS 層和應用層的組合，例如 sap ERP 開發系統、Sap Business 倉儲測試系統或 sap CRM 生產系統。 在 Azure 部署中，不支援在內部部署與 Azure 之間分割這兩個層級。 因此，SAP 系統會部署在內部部署環境或部署于 Azure 中。 您可以將 SAP 環境的不同系統部署到 Azure 或內部部署中。 例如，您可以在 Azure 中部署 SAP CRM 開發和測試系統，但在內部部署環境部署 SAP CRM 生產系統。
* **跨**單位：描述將 vm 部署到 azure 訂用帳戶的案例，該訂用帳戶在內部部署資料中心與 azure 之間具有站對站、多網站或 azure ExpressRoute 連線能力。 在一般 Azure 文件中，這類部署也會描述為跨單位案例。 

    連線的原因是為了將內部部署網域、內部部署 Active Directory 和內部部署 DNS 擴充到 Azure。 內部部署的架構會擴充到訂用帳戶的 Azure 資產。 透過此擴充，VM 可以是內部部署網域的一部分。 內部部署網域的網域使用者可以存取伺服器，並在這些 Vm 上執行服務（例如 DBMS 服務）。 您可以在內部部署的 VM 與 Azure 中部署的 VM 之間進行通訊與名稱解析。 此案例是在 Azure 上部署 SAP 資產最常見的案例。 如需詳細資訊，請參閱[規劃與設計 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。

> [!NOTE]
> SAP 系統的跨單位部署是指執行 SAP 系統的 Azure 虛擬機器是內部部署網域的成員，並且支援生產環境 SAP 系統。 跨單位組態可將部分或完整 SAP 環境部署到 Azure。 即使在 Azure 中執行完整的 SAP 環境，這些 Vm 還是必須是內部部署網域的一部分，而且 Active Directory/LDAP。 
>
> 在先前版本的檔中，已提到混合式 IT 案例。 「*混合*式」一詞是指內部部署與 Azure 之間有跨單位連線能力的根源。 在此情況下，混合式也表示 Azure 中的 Vm 是內部部署 Active Directory 的一部分。
>
>

有些 Microsoft 檔會以不同的方式來描述跨單位案例，特別是針對 DBMS 高可用性設定。 在 SAP 相關的檔中，跨單位案例會細分為站對站或私人[ExpressRoute](https://azure.microsoft.com/services/expressroute/)連線，以及在內部部署與 Azure 之間散發的 SAP 環境。

## <a name="resources"></a>資源
Azure 上的 SAP 工作負載有其他可用的文章。 開始使用[Azure 上的 SAP 工作負載：](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)開始著手，然後選擇您感關注的區域。

下列 SAP 附注與本檔涵蓋的區域有關 Azure 上的 SAP。

| 附註編號 | 標題 |
| --- | --- |
| [1928533] |Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型 |
| [2015553] |SAP on Microsoft Azure：支援必要條件 |
| [1999351] |對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2178632] |Microsoft Azure 上適用於 SAP 的主要監視度量 |
| [1409604] |Windows 上的虛擬化：增強型監視 |
| [2191498] |Linux 上使用 Azure 的 SAP：增強型監視 |
| [2039619] |在 Microsoft Azure 上使用 Oracle database 的 SAP 應用程式：支援的產品和版本 |
| [2233094] |DB6： Azure 上使用 IBM DB2 for Linux、UNIX 和 Windows 的 SAP 應用程式：其他資訊 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 授權問題 |
| [1984787] |SUSE LINUX Enterprise Server 12：安裝注意事項 |
| [2002167] |Red Hat Enterprise Linux 7.x：安裝和升級 |
| [2069760] |Oracle Linux 7.x SAP 安裝和升級 |
| [1597355] |適用於 Linux 的交換空間建議 |
| [2171857] |Oracle Database 12c： Linux 上的檔案系統支援 |
| [1114181] |Oracle Database 11g： Linux 上的檔案系統支援 |


如需所有適用于 Linux 的 SAP 附注的詳細資訊，請參閱[sap 社區 wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

您需要 Microsoft Azure 架構的實用知識，以及如何部署和操作 Microsoft Azure 虛擬機器。 如需詳細資訊，請參閱[Azure 檔](https://docs.microsoft.com/azure/)。

一般而言，Windows、Linux 和 DBMS 的安裝和設定基本上與您在內部部署環境中安裝的任何虛擬機器或裸機機器相同。 當您使用 Azure IaaS 時，會有不同的架構和系統管理執行決策。 本檔說明當您使用 Azure IaaS 時，所要備妥的特定架構和系統管理差異。


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 部署的 VM 儲存體結構
若要遵循本章的指示，請閱讀並瞭解[部署指南][deployment-guide]的[這一章][deployment-guide-3]所提供的資訊。 閱讀本章之前，您必須先瞭解並瞭解不同的 VM 系列和標準和 premium 儲存體之間的差異。 

若要瞭解 Azure Vm 的 Azure 儲存體，請參閱：

- [Azure Windows vm 的受控磁片簡介](../../windows/managed-disks-overview.md)。
- [Azure Linux vm 的受控磁片簡介](../../linux/managed-disks-overview.md)。

在基本設定中，我們通常會建議部署結構，其中作業系統、DBMS 和最終的 SAP 二進位檔會與資料庫檔案分開。 我們建議在 Azure 虛擬機器中執行的 SAP 系統，具有與作業系統、資料庫管理系統可執行檔和 SAP 可執行檔一起安裝的基底 VHD 或磁片。 

DBMS 資料和記錄檔會儲存在標準儲存體或 premium 儲存體中。 它們會儲存在不同的磁片中，並以邏輯磁片形式連接到原始的 Azure 作業系統映射 VM。 針對 Linux 部署，會記錄不同的建議，特別是針對 SAP Hana。

當您規劃磁片配置時，請找出這些專案之間的最佳平衡：

* 資料檔案數量。
* 包含檔案的磁碟數目。
* 單一磁碟的 IOPS 配額。
* 每個磁碟的資料輸送量。
* 每個 VM 大小可能的額外資料磁碟數目。
* VM 可提供的整體儲存體輸送量。
* 不同的 Azure 儲存體類型可以提供的延遲。
* VM Sla。

Azure 會強制執行每個資料磁碟的 IOPS 配額。 針對標準儲存體和 premium 儲存體上裝載的磁片，這些配額會不同。 I/O 延遲在兩個儲存體類型之間也有所不同。 Premium 儲存體提供更佳的 i/o 延遲。 

每個不同的 VM 類型都有您可以連接的資料磁片數目有限。 另一個限制是只有特定的 VM 類型可以使用 premium 儲存體。 一般來說，您決定根據 CPU 和記憶體需求來使用特定的 VM 類型。 您也可以考慮 IOPS、延遲及磁片輸送量需求，通常會以磁片數目或高階儲存體磁片的類型來調整。 每個磁片所要達到的 IOPS 數目和輸送量，可能會規定磁片大小，特別是高階儲存體。

> [!NOTE]
> 針對 DBMS 部署，建議您針對任何資料、交易記錄或重做檔案使用 premium 儲存體。 無論您是否想要部署生產或非生產系統，都不會有任何影響。

> [!NOTE]
> 若要受益于 Azure 唯一的[單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，所有附加的磁片都必須是高階儲存體類型，其中包括基底 VHD。

> [!NOTE]
> 不支援在儲存體硬體上裝載 SAP 資料庫的主要資料庫檔案（例如資料和記錄檔）位於與 Azure 資料中心連續的共置協力廠商資料中心。 針對 SAP 工作負載，SAP 資料庫的資料和交易記錄檔僅支援以原生 Azure 服務表示的儲存體。

資料庫檔案和記錄檔和重做檔案的位置，以及您使用的 Azure 儲存體類型是由 IOPS、延遲和輸送量需求所定義。 若要有足夠的 IOPS，您可能會被迫使用多個磁片，或使用較大的 premium 儲存體磁片。 如果您使用多個磁片，請在包含資料檔案或記錄檔和重做檔案的磁片上建立軟體 stripe。 在這種情況下，基礎 premium 儲存體磁片的 IOPS 和磁片輸送量 Sla，或標準儲存體磁片的最大可達成 IOPS，會針對產生的 stripe 集累加。

如前所述，如果您的 IOPS 需求超過單一 VHD 可以提供的數目，請將資料庫檔案在多個 Vhd 上所需的 IOPS 數量進行平衡。 將 IOPS 負載分散到多個磁片的最簡單方式，是在不同的磁片上建立一個軟體 stripe。 然後將 SAP DBMS 的多個資料檔案放在劃分超出軟體 stripe 的 Lun 上。 Stripe 中的磁片數目是由 IOPs 需求、磁片輸送量需求和大量需求所驅動。


---
> ![Windows][Logo_Windows] Windows
>
> 建議您使用 Windows 儲存空間，在多個 Azure Vhd 上建立等量集合。 至少使用 Windows Server 2012 R2 或 Windows Server 2016。
>
> ![Linux][Logo_Linux] Linux
>
> 僅支援 MDADM 和邏輯磁片區管理員（LVM）在 Linux 上建立軟體 RAID。 如需詳細資訊，請參閱：
>
> - 使用 MDADM[在 Linux 上設定軟體 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - 使用 LVM [在 Azure 中的 Linux VM 上設定 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

---

> [!NOTE]
> 因為 Azure 儲存體會保留三個 Vhd 映射，所以當您使用等量時，設定冗余並不合理。 您只需要設定等量分割，就可以將 i/o 分散到不同的 Vhd 上。
>

### <a name="managed-or-nonmanaged-disks"></a>受控或非受管理磁片
Azure 儲存體帳戶是系統管理結構，也是限制的主體。 標準儲存體帳戶和 premium 儲存體帳戶之間的限制有所不同。 如需功能和限制的相關資訊，請參閱[Azure 儲存體擴充性和效能目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

針對標準儲存體，請記住每個儲存體帳戶的 IOPS 有限制。 請參閱[Azure 儲存體擴充性和效能目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)一文中包含**總要求率**的資料列。 此外，每個 Azure 訂用帳戶的儲存體帳戶數目也有初始限制。 在不同儲存體帳戶之間平衡較大 SAP 環境的 Vhd，以避免達到這些儲存體帳戶的限制。 當您談論的數百部虛擬機器有超過一千個 Vhd 時，這是一件乏味的工作。

因為不建議使用適用于 DBMS 部署的標準儲存體搭配 SAP 工作負載，所以標準儲存體的參考和建議僅限於這[篇簡短文章](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

為了避免在不同的 Azure 儲存體帳戶之間規劃及部署 Vhd 的系統管理工作，Microsoft 在2017中引進了[Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 受控磁片適用于標準儲存體和 premium 儲存體。 相較于非受管理磁片，受控磁片的主要優點如下：

- 針對受控磁片，Azure 會在部署期間自動將不同的 Vhd 散發到不同的儲存體帳戶。 如此一來，就不會達到資料量、i/o 輸送量和 IOPS 的儲存體帳戶限制。
- 使用受控磁片，Azure 儲存體會接受 Azure 可用性設定組的概念。 如果 VM 是 Azure 可用性設定組的一部分，VM 的基底 VHD 和連結的磁片會部署至不同的容錯和更新網域。


> [!IMPORTANT]
> 基於 Azure 受控磁碟的優點，我們建議您在一般情況下使用 Azure 受控磁碟來進行 DBMS 部署和 SAP 部署。
>

若要從非受控磁片進行轉換，請參閱：

- [將 Windows 虛擬機器從非受控磁片轉換為受控磁片](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)。
- [將 Linux 虛擬機器從非受控磁片轉換為受控磁片](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)。


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和資料磁碟的快取
當您將磁片掛接到 Vm 時，您可以選擇是否要快取 VM 和位於 Azure 儲存體之磁片之間的 i/o 流量。 標準和高階儲存體會針對這種類型的快取使用兩種不同的技術。

下列建議假設標準 DBMS 的這些 i/o 特性：

- 它大多是對資料庫資料檔案的讀取工作負載。 這些讀取是 DBMS 系統的效能關鍵。
- 根據檢查點或常數資料流程，對資料檔案的寫入會發生在高載中。 平均超過一天，寫入次數比讀取少。 相對於從資料檔案讀取，這些寫入是非同步，而且不會保存任何使用者交易。
- 幾乎不會從交易記錄或重做檔案進行任何讀取。 當您執行交易記錄備份時，例外狀況是大型 i/o。
- 交易或重做記錄檔的主要負載是寫入。 視工作負載的本質而定，您可以將 i/o 的大小設為 4 KB，或在其他情況下使用 1 MB 或更多的 i/o 大小。
- 所有寫入都必須以可靠的方式保存在磁片上。

針對標準儲存體，可能的快取類型為：

* None
* 讀取
* 讀取/寫入

若要取得一致且具決定性的效能，請針對包含 DBMS 相關資料檔、記錄檔和重做檔案的所有磁片，將快取設定在標準儲存體上，並將資料表空間設為**NONE**。 基底 VHD 的快取可以保留預設值。

針對高階儲存體，會有下列快取選項：

* None
* 讀取
* 讀取/寫入
* 無 + 寫入加速器，僅適用于 Azure M 系列 Vm
* 讀取 + 寫入加速器，僅適用于 Azure M 系列 Vm

針對高階儲存體，建議您針對 SAP 資料庫的**資料檔案使用讀取**快取，並針對**記錄檔的磁片**選擇 [無快取]。

針對 M 系列部署，建議您將 Azure 寫入加速器用於 DBMS 部署。 如需 Azure 寫入加速器的詳細資訊、限制和部署，請參閱[啟用寫入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


### <a name="azure-nonpersistent-disks"></a>Azure 非持續性磁片
Azure Vm 會在部署 VM 之後提供非持續性磁片。 在 VM 重新開機的情況下，會抹除這些磁片磁碟機上的所有內容。這是為了讓資料庫的資料檔案和記錄檔和重做檔案不應位於這些非保存磁片磁碟機上。 某些資料庫可能會有例外狀況，而這些非保存磁片磁碟機可能適用于 tempdb 和臨時表空間。 請避免將這些磁片磁碟機用於 A 系列的 Vm，因為這些非保存磁片磁碟機在輸送量上會受到該 VM 系列的限制。 

如需詳細資訊，請參閱[瞭解 Azure 中 Windows vm 上的暫存磁片磁碟機](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)。

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM 中的 D 磁片磁碟機是非保存磁片磁碟機，它是由 Azure 計算節點上的一些本機磁片所支援。 因為它是非保存，所以當 VM 重新開機時，對 D 磁片磁碟機上的內容所做的任何變更都會遺失。 變更包括已儲存的檔案、已建立的目錄，以及已安裝的應用程式。
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure Vm 會在/mnt/resource 上自動掛接磁片磁碟機，這是 Azure 計算節點上的本機磁片所支援的非保存磁片磁碟機。 因為它是非保存，所以當 VM 重新開機時，對/mnt/resource 中的內容所做的任何變更都會遺失。 變更包括已儲存的檔案、已建立的目錄，以及已安裝的應用程式。
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 儲存體復原
Microsoft Azure 儲存體會將基底 VHD （包含 OS 和已連接的磁片或 blob）儲存在至少三個不同的儲存體節點上。 這種類型的存放裝置稱為「本機多餘的儲存體」（LRS）。 LRS 是 Azure 中所有儲存體類型的預設值。

還有其他的重複方法。 如需詳細資訊，請參閱[Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)複寫。

> [!NOTE]
>Premium storage 是建議的儲存體類型，適用于 DBMS Vm 和儲存資料庫和記錄和重做檔案的磁片。 Premium 儲存體唯一可用的冗余方法是 LRS。 因此，您必須設定資料庫方法，讓資料庫資料複寫到另一個 Azure 區域或可用性區域。 資料庫方法包括 SQL Server Always On、Oracle Data Guard 和 HANA 系統複寫。


> [!NOTE]
> 針對 DBMS 部署，不建議針對標準儲存體使用異地多餘儲存體（GRS）。 GRS 會嚴重影響效能，並不會在連接至 VM 的不同 Vhd 上接受寫入順序。 不接受跨不同 Vhd 的寫入順序，可能會導致複寫目標端的資料庫不一致。 如果資料庫和記錄檔和重做檔案分散到多個 Vhd （在來源 VM 端上通常是如此），就會發生這種情況。



## <a name="vm-node-resiliency"></a>VM 節點復原
Azure 提供數個不同的 Vm Sla。 如需詳細資訊，請參閱最新版本的[虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 因為 DBMS 層通常對 SAP 系統中的可用性很重要，所以您必須瞭解可用性設定組、可用性區域和維護事件。 如需這些概念的詳細資訊，請參閱[在 azure 中管理 Windows 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)和[管理 azure 中 Linux 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

SAP 工作負載的生產 DBMS 案例的最低建議事項為：

- 在相同 Azure 區域中的個別可用性設定組中部署兩個 Vm。
- 在相同的 Azure 虛擬網路中執行這兩個 Vm，並將 Nic 連接到相同的子網。
- 使用資料庫方法讓第二部 VM 保持熱待命。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA System Replication。

您也可以在另一個 Azure 區域中部署第三個 VM，並使用相同的資料庫方法，在另一個 Azure 區域中提供非同步複本。

如需有關如何設定 Azure 可用性設定組的詳細資訊，請參閱[此教學](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)課程。



## <a name="azure-network-considerations"></a>Azure 網路考慮
在大規模的 SAP 部署中，請使用[Azure 虛擬資料中心](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)的藍圖。 將它用於您的虛擬網路設定、許可權和角色指派給組織的不同部分。

這些最佳作法是數百個客戶部署的結果：

- 部署 SAP 應用程式的虛擬網路無法存取網際網路。
- 資料庫 Vm 會在與應用層相同的虛擬網路中執行。
- 虛擬網路內的 Vm 具有私人 IP 位址的靜態配置。 如需詳細資訊，請參閱[Azure 中的 IP 位址類型和配置方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。
- DBMS Vm 的路由限制*不*會使用安裝在本機 DBMS vm 上的防火牆進行設定。 相反地，流量路由是使用[網路安全性群組（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)來定義。
- 若要將流量分開並隔離到 DBMS VM，請將不同的 Nic 指派給 VM。 每個 NIC 都會取得不同的 IP 位址，而每個 NIC 會指派給不同的虛擬網路子網。 每個子網都有不同的 NSG 規則。 隔離或分隔網路流量是路由的量值。 它不會用來設定網路輸送量的配額。

> [!NOTE]
> 透過 Azure 指派靜態 IP 位址，表示將它們指派給個別的虛擬 Nic。 請勿將來賓 OS 內的靜態 IP 位址指派給虛擬 NIC。 某些 Azure 服務（例如 Azure 備份）會依賴至少主要虛擬 NIC 設定為 DHCP 的事實，而不是靜態 IP 位址。 如需詳細資訊，請參閱針對[Azure 虛擬機器備份進行疑難排解](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 若要將多個靜態 IP 位址指派給 VM，請將多個虛擬 Nic 指派給 VM。
>


> [!IMPORTANT]
> 不支援在 sap NetWeaver、Hybris 或 S/4HANA 型 SAP 系統的 SAP 應用程式與 DBMS 層之間的通訊路徑中設定[網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)。 此限制適用于功能和效能的原因。 SAP 應用層與 DBMS 層之間的通訊路徑必須是直接的。 如果這些 ASG 和 NSG 規則允許直接通訊路徑，則限制不會包含[應用程式安全性群組（ASG）和 NSG 規則](https://docs.microsoft.com/azure/virtual-network/security-overview)。 
>
> 不支援網路虛擬裝置的其他案例位於：
>
> * Azure Vm （代表 Linux Pacemaker 叢集節點和 SBD 裝置）之間的通訊路徑，如[Azure vm 上的 Sap NetWeaver 的高可用性（適用于 Sap 應用程式的 SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)）中所述。
> * Azure Vm 與 Windows Server 向外延展檔案伺服器（SOFS）之間的通訊路徑，如在[azure 中使用檔案共用在 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)叢集處理中所述。 
>
> 通訊路徑中的網路虛擬裝置可以輕鬆地將兩個通訊合作夥伴之間的網路延遲加倍。 它們也可以限制 SAP 應用層與 DBMS 層之間重要路徑中的輸送量。 在某些客戶案例中，網路虛擬裝置可能會導致 Pacemaker Linux 叢集失敗。 這些是 Linux Pacemaker 叢集節點之間的通訊透過網路虛擬裝置與其 SBD 裝置通訊的情況。
>

> [!IMPORTANT]
> *不*支援的另一種設計是將 SAP 應用層和 DBMS 層隔離到不同的 Azure 虛擬網路，而不會彼此[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 我們建議您在 Azure 虛擬網路內使用子網來隔離 SAP 應用層和 DBMS 層，而不是使用不同的 Azure 虛擬網路。 
>
> 如果您決定不要遵循建議，而改為將兩層劃分成不同的虛擬網路，則必須[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)這兩個虛擬網路。 
>
> 請注意，兩個[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虛擬網路之間的網路流量受限於傳輸成本。 SAP 應用層和 DBMS 層之間會交換大量資料量，其中包含許多 tb。 如果 SAP 應用層和 DBMS 層分別在兩個對等互連的 Azure 虛擬網路之間進行隔離，您就可以累積大量成本。

針對 Azure 可用性設定組內的生產 DBMS 部署使用兩個 Vm。 也請針對 SAP 應用層使用不同的路由，並將管理和作業流量用於兩個 DBMS Vm。 請見下圖：

![兩個子網路中兩部 VM 的圖表](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure Load Balancer 來重新導向流量
使用 SQL Server Always On 或 HANA 系統複寫等功能中使用的私人虛擬 IP 位址，需要設定 Azure 負載平衡器。 負載平衡器會使用探查埠來判斷作用中的 DBMS 節點，並以獨佔方式將流量路由傳送至該作用中的資料庫節點。 

如果資料庫節點發生容錯移轉，則不需要重新設定 SAP 應用程式。 相反地，最常見的 SAP 應用程式架構會重新連線到私人虛擬 IP 位址。 同時，負載平衡器會將流量與私人虛擬 IP 位址重新導向至第二個節點，以回應節點容錯移轉。

Azure 提供兩個不同的[負載平衡器 sku](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)：基本 sku 和標準 sku。 除非您想要跨 Azure 可用性區域進行部署，否則基本負載平衡器 SKU 會正常運作。

DBMS Vm 與 SAP 應用層之間的流量一律會一直透過負載平衡器進行路由嗎？ 答案取決於您設定負載平衡器的方式。 

此時，DBMS VM 的連入流量一律會透過負載平衡器路由傳送。 從 DBMS VM 到應用層 VM 的傳出流量路由取決於負載平衡器的設定。 

負載平衡器會提供 DirectServerReturn 選項。 如果已設定該選項，從 DBMS VM 導向至 SAP 應用層的流量*不*會透過負載平衡器來路由傳送。 相反地，它會直接進入應用層。 如果未設定 DirectServerReturn，則會透過負載平衡器來路由傳送至 SAP 應用層的流量。

我們建議您將 DirectServerReturn 與位於 SAP 應用層與 DBMS 層之間的負載平衡器搭配使用。 此設定可減少兩個層級之間的網路延遲。

如需如何使用 SQL Server Always On 設定此設定的範例，請參閱設定[Azure 中 Always On 可用性群組的 ILB](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)接聽程式。

如果您使用已發佈的 GitHub JSON 範本作為 Azure 中 SAP 基礎結構部署的參考，請研究此[適用于 sap 3 層系統的範本](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)。 在此範本中，您也可以看到負載平衡器的正確設定。

### <a name="azure-accelerated-networking"></a>Azure 加速網路
若要進一步減少 Azure Vm 之間的網路延遲，我們建議您選擇 [ [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)]。 當您針對 SAP 工作負載部署 Azure Vm 時使用，特別是針對 SAP 應用層和 SAP DBMS 層。

> [!NOTE]
> 並非所有 VM 類型都支援加速網路。 前一篇文章列出支援加速網路的 VM 類型。
>

---
> ![Windows][Logo_Windows] Windows
>
> 若要瞭解如何使用 Windows 的加速網路部署 Vm，請參閱[建立具有加速網路的 windows 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。
>
> ![Linux][Logo_Linux] Linux
>
> 如需 Linux 散發套件的詳細資訊，請參閱[使用加速網路建立 linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。
>
>

---

> [!NOTE]
> 如果是 SUSE、Red Hat 和 Oracle Linux，最近版本都支援加速網路。 SLES 12 SP2 或 RHEL 7.2 等較舊的版本不支援 Azure 加速網路。
>


## <a name="deployment-of-host-monitoring"></a>主機監視的部署
針對在 Azure 虛擬機器中使用 SAP 應用程式的生產環境，SAP 需要能夠從執行 Azure 虛擬機器的實體主機取得主機監視資料。 需要有特定的 SAP HostAgent 修補程式等級，才能在 SAPOSCOL 和 SAP HostAgent 中啟用此功能。 確切的修補程式等級記載於 SAP 附註 [1409604]。

如需有關將主機資料傳遞至 SAPOSCOL 和 SAP 主機代理程式，以及這些元件之生命週期管理之元件部署的詳細資訊，請參閱[部署指南][deployment-guide]。


## <a name="next-steps"></a>後續步驟
如需特定 DBMS 的詳細資訊，請參閱：

- [適用於 SAP 工作負載的 SQL Server Azure 虛擬機器 DBMS 部署](dbms_guide_sqlserver.md)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](dbms_guide_oracle.md)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](dbms_guide_ibm.md)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](dbms_guide_sapase.md)
- [Azure 上的 SAP maxDB、Live Cache 和 Content Server 部署](dbms_guide_maxdb.md)
- [Azure 上的 SAP Hana 作業指南](hana-vm-operations.md)
- [Azure 虛擬機器的 SAP Hana 高可用性](sap-hana-availability-overview.md)
- [Azure 虛擬機器上的 SAP Hana 備份指南](sap-hana-backup-guide.md)

