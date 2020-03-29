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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
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

本指南是有關如何在 Microsoft Azure 上實現和部署 SAP 軟體的文檔的一部分。 在閱讀本指南之前，請閱讀[規劃和實施指南][planning-guide]。 本文檔使用 Azure 基礎結構作為服務 （IaaS） 功能，介紹 Microsoft Azure 虛擬機器 （VM） 上與 SAP 相關的 DBMS 系統的通用部署方面。

本文補充了 SAP 安裝文檔和 SAP 說明，它們代表了在給定平臺上安裝和部署 SAP 軟體的主要資源。

本文件說明在 Azure VM 中執行 SAP 相關 DBMS 系統的考量。 有數個對於本章中特定 DBMS 系統的參考資料。 相反，在本文之後，在本文中處理特定的 DBMS 系統。

## <a name="definitions"></a>定義
在整個文檔中，使用這些術語：

* **IaaS**：基礎設施即服務。
* **PaaS**： 平臺即服務。
* **SaaS**： 軟體即服務.
* **SAP 元件**：單個 SAP 應用程式，如 ERP 中央元件 （ECC）、業務倉庫 （BW）、解決方案管理器或企業門戶 （EP）。 SAP 元件可以基於傳統的 ABAP 或 JAVA 技術，也可以基於基於 NetWeaver 的非應用程式（如業務物件）。
* **SAP 環境**：一個或多個 SAP 元件邏輯分組以執行業務功能，如開發、品質保證、培訓、災害復原或生產。
* **SAP 橫向：** 此術語是指客戶 IT 環境中的整個 SAP 資產。 SAP 環境包括所有生產和非生產環境。
* **SAP 系統**：DBMS 層和應用程式層的組合，例如 SAP ERP 開發系統、SAP 業務倉庫測試系統或 SAP CRM 生產系統。 在 Azure 部署中，不支援在本地和 Azure 之間劃分這兩個圖層。 因此，SAP 系統要麼部署在本地，要麼部署在 Azure 中。 您可以在 Azure 或本地部署 SAP 橫向的不同系統。 例如，您可以在 Azure 中部署 SAP CRM 開發和測試系統，但在本地部署 SAP CRM 生產系統。
* **跨界**：描述將 VM 部署到在本地資料中心和 Azure 之間具有網站到網站、多網站或 Azure 快速路由連接的 Azure 訂閱的方案。 在一般 Azure 文件中，這類部署也會描述為跨單位案例。 

    連線的原因是為了將內部部署網域、內部部署 Active Directory 和內部部署 DNS 擴充到 Azure。 內部部署的架構會擴充到訂用帳戶的 Azure 資產。 透過此擴充，VM 可以是內部部署網域的一部分。 本地域的域使用者可以訪問伺服器並在這些 VM 上運行服務，如 DBMS 服務。 您可以在內部部署的 VM 與 Azure 中部署的 VM 之間進行通訊與名稱解析。 此方案是用於在 Azure 上部署 SAP 資產的最常見方案。 如需詳細資訊，請參閱[規劃與設計 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。

> [!NOTE]
> SAP 系統的跨界部署是運行 SAP 系統的 Azure 虛擬機器是本地域的成員，並且支援生產 SAP 系統。 跨單位組態可將部分或完整 SAP 環境部署到 Azure。 即使在 Azure 中運行完整的 SAP 環境，也要求這些 VM 成為本地域和活動目錄/LDAP 的一部分。 
>
> 在文檔的早期版本中，提到了混合 IT 方案。 術語*混合*植根于本地和 Azure 之間存在跨界連接這一事實。 在這種情況下，混合還意味著 Azure 中的 VM 是本地活動目錄的一部分。
>
>

某些 Microsoft 文檔對跨界方案描述的方式略有不同，尤其是對於 DBMS 高可用性配置。 在 SAP 相關文檔中，跨界方案可歸結為網站到網站或專用[ExpressRoute](https://azure.microsoft.com/services/expressroute/)連接以及分佈在本地和 Azure 之間的 SAP 橫向。

## <a name="resources"></a>資源
Azure 上的 SAP 工作負荷上還有其他文章。 從[Azure 上的 SAP 工作負荷開始：開始](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)，然後選擇感興趣的區域。

以下 SAP 說明與 Azure 上的 SAP 有關本文檔涵蓋的區域。

| 附註編號 | Title |
| --- | --- |
| [1928533] |Azure 上的 SAP 應用程式：受支援的產品和 Azure VM 類型 |
| [2015553] |微軟 Azure 上的 SAP：支援先決條件 |
| [1999351] |對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2178632] |Microsoft Azure 上適用於 SAP 的主要監視度量 |
| [1409604] |Windows 上的虛擬化：增強的監視 |
| [2191498] |使用 Azure 在 Linux 上 SAP：增強的監視 |
| [2039619] |使用 Oracle 資料庫在 Microsoft Azure 上的 SAP 應用程式：支援的產品和版本 |
| [2233094] |DB6：使用 IBM DB2 進行 Linux、UNIX 和 Windows 的 Azure 上的 SAP 應用程式：其他資訊 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 授權問題 |
| [1984787] |SUSE LINUX Enterprise Server 12：安裝注意事項 |
| [2002167] |紅帽企業 Linux 7.x：安裝和升級 |
| [2069760] |Oracle Linux 7.x SAP 安裝和升級 |
| [1597355] |適用於 Linux 的交換空間建議 |
| [2171857] |Oracle 資料庫 12c：Linux 上的檔案系統支援 |
| [1114181] |甲骨文資料庫 11g：Linux 上的檔案系統支援 |


有關所有 SAP Linux 說明的資訊，請參閱[SAP 社區 wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

您需要瞭解 Microsoft Azure 體系結構以及如何部署和操作 Microsoft Azure 虛擬機器。 有關詳細資訊，請參閱[Azure 文檔](https://docs.microsoft.com/azure/)。

通常，Windows、Linux 和 DBMS 的安裝和配置基本上與您在本地安裝的任何虛擬機器或裸機相同。 在使用 Azure IaaS 時，有一些體系結構和系統管理實現決策不同。 本文檔介紹在使用 Azure IaaS 時要準備的特定體系結構和系統管理差異。


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>用於 RDBMS 部署的 VM 的存儲結構
要遵循本章，請閱讀並理解[部署指南][deployment-guide][本章][deployment-guide-3]中提供的資訊。 在閱讀本章之前，您需要瞭解並瞭解不同的 VM 系列以及標準和高級存儲之間的區別。 

要瞭解 Azure VM 的 Azure 存儲，請參閱：

- [Azure Windows VM 的託管磁片簡介](../../windows/managed-disks-overview.md)。
- [Azure Linux VM 的託管磁片簡介](../../linux/managed-disks-overview.md)。

在基本配置中，我們通常建議部署結構，其中作業系統、DBMS 和最終的 SAP 二進位檔案與資料庫檔案分開。 我們建議在 Azure 虛擬機器中運行的 SAP 系統具有基本 VHD 或磁片，並安裝作業系統、資料庫管理系統可執行檔和 SAP 可執行檔。 

DBMS 資料和日誌檔存儲在標準存儲或高級存儲中。 它們存儲在單獨的磁片中，並作為邏輯磁片附加到原始 Azure 作業系統映射 VM。 對於 Linux 部署，將記錄不同的建議，尤其是針對 SAP HANA。

規劃磁片佈局時，請找到以下專案之間的最佳平衡：

* 資料檔案數量。
* 包含檔案的磁碟數目。
* 單一磁碟的 IOPS 配額。
* 每個磁碟的資料輸送量。
* 每個 VM 大小可能的額外資料磁碟數目。
* VM 可提供的整體儲存體輸送量。
* 不同的 Azure 儲存體類型可以提供的延遲。
* VM SL。

Azure 會強制執行每個資料磁碟的 IOPS 配額。 對於託管在標準存儲和高級存儲上的磁片，這些配額是不同的。 I/O 延遲在兩個儲存體類型之間也有所不同。 高級存儲提供更好的 I/O 延遲。 

每種不同的 VM 類型都有數量有限的資料磁片，您可以附加這些磁片。 另一個限制是，只有某些 VM 類型才能使用高級存儲。 通常，您決定根據 CPU 和記憶體要求使用特定的 VM 類型。 您還可能考慮通常隨磁片數或高級存儲磁片類型進行縮放的 IOPS、延遲和磁片輸送量要求。 IOPS 的數量和每個磁片要實現的輸送量可能決定磁片大小，尤其是高級存儲。

> [!NOTE]
> 對於 DBMS 部署，我們建議對任何資料、事務日誌或重做檔使用高級存儲。 是否要部署生產系統還是非生產系統並不重要。

> [!NOTE]
> 為了從 Azure 唯一的單[一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)中受益，連接的所有磁片都必須是高級存儲類型，其中包括基本 VHD。

> [!NOTE]
> 不支援在位於 Azure 資料中心附近的協力廠商資料中心的存儲硬體上託管 SAP 資料庫的主要資料庫檔案（如資料和日誌檔）。 對於 SAP 工作負荷，SAP 資料庫的資料和事務日誌檔僅支援表示為本機 Azure 服務的存儲。

資料庫檔案和日誌和重做檔的位置以及您使用的 Azure 存儲類型由 IOPS、延遲和輸送量要求定義。 要有足夠的 IOPS，您可能被迫使用多個磁片或使用更大的高級存儲磁片。 如果使用多個磁片，請跨包含資料檔案或日誌和重做檔的磁片構建軟體條帶。 在這種情況下，基礎高級存儲磁片的 IOPS 和磁片輸送量 SL 或標準存儲磁片的最大可實現 IOPS 是生成的條帶集累積的。

如上所述，如果您的 IOPS 要求超過單個 VHD 可以提供的數量，請平衡資料庫檔案所需的 IOPS 數量，跨越多個 VHD。 跨磁片分發 IOPS 負載的最簡單方法是在不同的磁片上構建軟體條帶。 然後，將 SAP DBMS 的一些資料檔案放在從軟體條帶中雕刻的 LUN 上。 條帶中的磁片數由 IAP 需求、磁片輸送量要求和卷需求驅動。


---
> ![Windows][Logo_Windows] Windows
>
> 我們建議您使用 Windows 存儲空間跨多個 Azure VHD 創建條帶集。 至少使用 Windows 伺服器 2012 R2 或 Windows 伺服器 2016。
>
> ![Linux][Logo_Linux] Linux
>
> 僅支援 MDADM 和邏輯卷管理器 （LVM） 在 Linux 上構建軟體 RAID。 如需詳細資訊，請參閱
>
> - 使用 MDADM[在 Linux 上配置軟體 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - 使用 LVM [在 Azure 中的 Linux VM 上設定 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

---

> [!NOTE]
> 由於 Azure 存儲保留三個 VHD 映射，因此在條帶條時配置冗余沒有意義。 您只需配置條帶化，以便 I/O 分佈在不同的 VHD 上。
>

### <a name="managed-or-nonmanaged-disks"></a>託管或非託管磁片
Azure 存儲帳戶是一個管理構造，也是一個限制物件。 標準存儲帳戶和高級存儲帳戶之間的限制不同。 有關功能和限制的資訊，請參閱[Azure 存儲可伸縮性和性能目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

對於標準存儲，請記住每個存儲帳戶的 IOPS 有限制。 請參閱文章[Azure 存儲可伸縮性和性能目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)中包含**總請求速率**的行。 每個 Azure 訂閱的存儲帳戶數也有初始限制。 平衡不同存儲帳戶中更大的 SAP 環境的 VHD，以避免達到這些存儲帳戶的限制。 當您談論幾百台虛擬機器時，這是一項繁瑣的工作，虛擬機器具有一千多個 VHD。

由於不建議將 DBMS 部署與 SAP 工作負載結合使用，因此對標準存儲的引用和建議僅限於本文[全文](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

為了避免跨不同的 Azure 存儲帳戶規劃和部署 VHD 的管理工作，Microsoft 在 2017 年引入了[Azure 託管磁片](https://azure.microsoft.com/services/managed-disks/)。 託管磁片可用於標準存儲和高級存儲。 與非託管磁片相比，託管磁片的主要優點是：

- 對於託管磁片，Azure 在部署時自動將不同的 VHD 分發到不同的存儲帳戶中。 這樣，資料卷、I/O 輸送量和 IOPS 的存儲帳戶限制就不會受到衝擊。
- 使用託管磁片，Azure 存儲遵循 Azure 可用性集的概念。 如果 VM 是 Azure 可用性集的一部分，則 VM 的基本 VHD 和附加磁片將部署到不同的故障和更新域中。


> [!IMPORTANT]
> 鑒於 Azure 託管磁片的優點，我們建議您將 Azure 託管磁片用於 DBMS 部署和 SAP 部署。
>

要從非託管磁片轉換為託管磁片，請參閱：

- [將 Windows 虛擬機器從非託管磁片轉換為託管磁片](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)。
- [將 Linux 虛擬機器從非託管磁片轉換為託管磁片](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)。


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和資料磁碟的快取
將磁片裝載到 VM 時，可以選擇是否緩存 VM 和位於 Azure 存儲中的磁片之間的 I/O 流量。 標準和高級存儲使用兩種不同的技術進行此類緩存。

以下建議假定標準 DBMS 的 I/O 特徵：

- 它主要是針對資料庫的資料檔案的讀取工作負載。 這些讀取對於 DBMS 系統至關重要的性能。
- 根據檢查點或恒定流，對資料檔案寫入以突發形式進行。 平均一天，寫入次數少於讀取次數。 與從資料檔案讀取相反，這些寫入是非同步，不會阻止任何使用者事務。
- 幾乎不會從交易記錄或重做檔案進行任何讀取。 執行事務記錄備份時，異常是較大的 I/O。
- 針對事務或重做日誌檔的主負載是寫入。 根據工作負載的性質，可以將 I/O 小到 4 KB，或者在其他情況下，I/O 大小為 1 MB 或更多。
- 所有寫入都必須以可靠的方式保存在磁片上。

對於標準存儲，可能的緩存類型包括：

* None
* 讀取
* 讀取/寫入

要獲得一致和確定性的性能，請為所有包含 DBMS 相關資料檔案、日誌和重做檔以及表空間的所有磁片的標準存儲設置緩存到**NONE**。 基底 VHD 的快取可以保留預設值。

對於高級存儲，存在以下緩存選項：

* None
* 讀取
* 讀取/寫入
* 無 = 寫入加速器，僅適用于 Azure M 系列 VM
* 讀取和寫入加速器，僅適用于 Azure M 系列 VM

對於高級存儲，我們建議您對 SAP 資料庫**的資料檔案**使用 Read 緩存，並為**日誌檔的磁片選擇"無緩存"。**

對於 M 系列部署，我們建議您在 DBMS 部署中使用 Azure 寫入加速器。 有關 Azure 寫入加速器的詳細資訊、限制和部署，請參閱[啟用寫入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


### <a name="azure-nonpersistent-disks"></a>Azure 非持久性磁片
部署 VM 後，Azure VM 提供非持久性磁片。 在 VM 重新開機的情況下，這些磁碟機上的所有內容都已清除。給定的是，資料檔案以及資料庫的日誌和重做檔在任何情況下都不應位於這些未持久化的磁碟機上。 某些資料庫可能有例外，這些非持久化磁碟機可能適用于 tempdb 和臨時表空間。 避免將這些磁碟機用於 A 系列 VM，因為這些非持久化磁碟機的輸送量與該 VM 系列一起受到限制。 

有關詳細資訊，請參閱瞭解[Azure 中的 Windows VM 上的臨時磁碟機](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)。

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM 中的磁碟機 D 是非持久化磁碟機，由 Azure 計算節點上的一些本地磁片支援。 因為它未持久化，因此在重新開機 VM 時，對磁碟機 D 上的內容所做的任何更改都將丟失。 更改包括存儲的檔、已創建的目錄和已安裝的應用程式。
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM 在 /mnt/資源處自動裝載磁碟機，該磁碟機是由 Azure 計算節點上的本地磁片支援的未持久磁碟機。 由於它是非持久化的，因此在重新開機 VM 時，對 /mnt/資源中的內容所做的任何更改都將丟失。 更改包括存儲的檔、已創建的目錄和已安裝的應用程式。
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 儲存體復原
Microsoft Azure 存儲將基本 VHD 與作業系統和附加的磁片或 Blob 存儲在至少三個單獨的存儲節點上。 這種類型的存儲稱為本地冗余存儲 （LRS）。 LRS 是 Azure 中所有類型的存儲的預設值。

還有其他冗余方法。 有關詳細資訊，請參閱[Azure 存儲複製](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

> [!NOTE]
>高級存儲是存儲資料庫以及日誌和重做檔的 DBMS VM 和磁片的推薦存儲類型。 高級存儲的唯一可用冗余方法是 LRS。 因此，您需要設定資料庫方法，以便將資料庫資料複製到另一個 Azure 區域或可用性區域。 資料庫方法包括 SQL Server 始終打開、Oracle 資料防護和 HANA 系統複製。


> [!NOTE]
> 對於 DBMS 部署，不建議對標準存儲使用異地冗余存儲 （GRS）。 GRS 嚴重影響性能，並且不遵守附加到 VM 的不同 VHD 的寫入順序。 不遵守不同 VHD 的寫入順序可能會導致複製目標端的資料庫不一致。 如果資料庫、日誌和重做檔分佈在多個 VHD 上（通常情況下，在源 VM 端），則會出現這種情況。



## <a name="vm-node-resiliency"></a>VM 節點復原
Azure 為 VM 提供了多個不同的 SL。 有關詳細資訊，請參閱虛擬機器的[SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)最新版本。 由於 DBMS 層通常對 SAP 系統中的可用性至關重要，因此您需要瞭解可用性集、可用性區域和維護事件。 有關這些概念的詳細資訊，請參閱[管理 Azure 中的 Windows 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)和管理 Azure 中的 Linux[虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

對於具有 SAP 工作負載的生產 DBMS 方案，最低建議是：

- 在同一 Azure 區域中的單獨可用性集中部署兩個 VM。
- 在同一 Azure 虛擬網路中運行這兩個 VM，並將 NIC 從同一子網中連接。
- 使用資料庫方法讓第二部 VM 保持熱待命。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA System Replication。

您還可以在另一個 Azure 區域中部署第三個 VM，並使用相同的資料庫方法在另一個 Azure 區域中提供非同步副本。

有關如何設置 Azure 可用性集的資訊，請參閱[本教程](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)。



## <a name="azure-network-considerations"></a>Azure 網路注意事項
在大規模 SAP 部署中，請使用 Azure[虛擬資料中心](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)的藍圖。 將其用於虛擬網路配置以及組織不同部分的許可權和角色指派。

這些最佳實踐是數百個客戶部署的結果：

- SAP 應用程式部署的虛擬網路無法訪問互聯網。
- 資料庫 VM 與應用程式層在同一虛擬網路中運行。
- 虛擬網路中的 VM 具有私人 IP 位址的靜態配置。 有關詳細資訊，請參閱[Azure 中的 IP 位址類型和分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。
- 在本地 DBMS VM 上安裝防火牆*時，不會*設置與 DBMS VM 的路由限制。 相反，流量路由是使用[網路安全性群組 （NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)定義的。
- 要分離和隔離對 DBMS VM 的流量，請向 VM 分配不同的 NIC。 每個 NIC 都獲取不同的 IP 位址，並且每個 NIC 都分配給不同的虛擬網路子網。 每個子網都有不同的 NSG 規則。 網路流量的隔離或分離是路由的度量。 它不用於為網路輸送量設置配額。

> [!NOTE]
> 通過 Azure 分配靜態 IP 位址意味著將它們分配給各個虛擬 NIC。 不要在客體作業系統中為虛擬 NIC 分配靜態 IP 位址。 某些 Azure 服務（如 Azure 備份）依賴于以下事實：至少主虛擬 NIC 設置為 DHCP，而不是靜態 IP 位址。 有關詳細資訊，請參閱對[Azure 虛擬機器備份進行故障排除](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 要將多個靜態 IP 位址分配給 VM，請為 VM 分配多個虛擬 NIC。
>


> [!IMPORTANT]
> 不支援在 SAP 應用程式與 SAP NetWeaver、Hybris 或 S/4HANA SAP 系統 DBMS 層之間的通訊路徑中配置[網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/)。 此限制是出於功能和性能原因。 SAP 應用程式層和 DBMS 層之間的通訊路徑必須是直接的。 如果這些 ASG 和 NSG 規則允許直接通訊路徑，則限制不包括[應用程式安全性群組 （ASG） 和 NSG 規則](https://docs.microsoft.com/azure/virtual-network/security-overview)。 
>
> 不支援網路虛擬裝置的其他方案包括：
>
> * 代表 Linux 起搏器叢集節點和 SBD 設備的 Azure VM 之間的通訊路徑，如 SAP[應用程式 SUSE Linux 企業伺服器上的 AZURE VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)中所述。
> * Azure VM 和 Windows 伺服器橫向擴展檔案伺服器 （SOF） 之間的通訊路徑，如在[Windows 容錯移轉叢集上的 SAP ASCS/SCS 實例群集中所述，使用 Azure 中的檔共用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)。 
>
> 通訊路徑中的網路虛擬裝置可以輕鬆地使兩個通信夥伴之間的網路延遲加倍。 它們還可以限制 SAP 應用程式層和 DBMS 層之間關鍵路徑的輸送量。 在某些客戶方案中，網路虛擬裝置可能導致起搏器 Linux 群集失敗。 在這些案例中，Linux 起搏器叢集節點之間的通信通過網路虛擬裝置與其 SBD 設備通信。
>

> [!IMPORTANT]
> 另一個*不支援*的設計是 SAP 應用程式層和 DBMS 層隔離到不同的 Azure 虛擬網路中，這些網路彼此不[相互對等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 我們建議您使用 Azure 虛擬網路中的子網而不是使用不同的 Azure 虛擬網路來隔離 SAP 應用程式層和 DBMS 層。 
>
> 如果您決定不遵循建議，而是將兩個層隔離到不同的虛擬網路中，則必須對兩個虛擬網路[進行對等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 
>
> 請注意，兩[個對等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虛擬網路之間的網路流量需要轉移成本。 由多個 TB 組成的龐大資料量在 SAP 應用程式層和 DBMS 層之間交換。 如果將 SAP 應用程式層和 DBMS 層隔離到兩個對等 Azure 虛擬網路之間，則可以累積大量成本。

在 Azure 可用性集中使用兩個 VM 進行生產 DBMS 部署。 還對 SAP 應用程式層以及兩個 DBMS VM 的管理和運營流量使用單獨的路由。 請見下圖：

![兩個子網路中兩部 VM 的圖表](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure 負載等化器重定向流量
使用專用虛擬 IP 位址用於 SQL Server 始終打開或 HANA 系統複製等功能需要配置 Azure 負載等化器。 負載等化器使用探測埠來確定活動 DBMS 節點，並將流量專用路由到該活動資料庫節點。 

如果資料庫節點有容錯移轉，則 SAP 應用程式無需重新配置。 相反，最常見的 SAP 應用程式體系結構會根據專用虛擬 IP 位址重新連接。 同時，負載等化器通過將對專用虛擬 IP 位址的流量重定向到第二個節點來回應節點容錯移轉。

Azure 提供了兩個不同的[負載等化器 SKU：](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)一個基本 SKU 和一個標準 SKU。 除非要跨 Azure 可用性區域進行部署，否則基本負載等化器 SKU 可以正常。

DBMS VM 和 SAP 應用程式層之間的流量是否始終通過負載等化器路由？ 答案取決於您設定負載平衡器的方式。 

此時，到 DBMS VM 的傳入流量始終通過負載等化器路由。 從 DBMS VM 到應用程式層 VM 的傳出流量路由取決於負載等化器的配置。 

負載平衡器會提供 DirectServerReturn 選項。 如果配置了該選項，則從 DBMS VM 定向到 SAP 應用程式層的流量*不會*通過負載等化器路由。 相反，它直接轉到應用程式層。 如果未配置 DirectServerReturn，則返回 SAP 應用程式層的流量將通過負載等化器路由。

我們建議您將 DirectServerReturn 與位於 SAP 應用程式層和 DBMS 層之間的負載等化器組合使用。 此配置可減少兩個層之間的網路延遲。

有關如何使用 SQL Server 始終打開設置此配置的示例，請參閱為[Azure 中始終處於打開的可用性組配置 ILB 攔截器](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)。

如果使用已發佈的 GitHub JSON 範本作為 Azure 中 SAP 基礎結構部署的參考，請[研究此範本以進行 SAP 3 層系統](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)。 在此範本中，您還可以看到負載等化器的正確設置。

### <a name="azure-accelerated-networking"></a>Azure 加速網路
為了進一步減少 Azure VM 之間的網路延遲，我們建議您選擇[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 在為 SAP 工作負荷部署 Azure VM 時使用它，尤其是 SAP 應用程式層和 SAP DBMS 層。

> [!NOTE]
> 並非所有 VM 類型都支援加速網路。 上一篇文章列出了支援加速網路的 VM 類型。
>

---
> ![Windows][Logo_Windows] Windows
>
> 要瞭解如何部署具有 Windows 加速網路的 VM，請參閱[創建具有加速網路的 Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。
>
> ![Linux][Logo_Linux] Linux
>
> 有關 Linux 發行版本的詳細資訊，請參閱[創建具有加速網路的 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。
>
>

---

> [!NOTE]
> 如果是 SUSE、Red Hat 和 Oracle Linux，最近版本都支援加速網路。 舊版本（如 SLES 12 SP2 或 RHEL 7.2）不支援 Azure 加速網路。
>


## <a name="deployment-of-host-monitoring"></a>部署主機監視
為了在 Azure 虛擬機器中使用 SAP 應用程式，SAP 需要從運行 Azure 虛擬機器的物理主機獲取主機監視資料的能力。 需要有特定的 SAP HostAgent 修補程式等級，才能在 SAPOSCOL 和 SAP HostAgent 中啟用此功能。 確切的修補程式等級記載於 SAP 附註 [1409604]。

有關向 SAPOSCOL 和 SAP 主機代理提供主機資料的元件的部署以及這些元件的生命週期管理的詳細資訊，請參閱[部署指南][deployment-guide]。


## <a name="next-steps"></a>後續步驟
有關特定 DBMS 的詳細資訊，請參閱：

- [適用於 SAP 工作負載的 SQL Server Azure 虛擬機器 DBMS 部署](dbms_guide_sqlserver.md)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](dbms_guide_oracle.md)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](dbms_guide_ibm.md)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](dbms_guide_sapase.md)
- [Azure 上的 SAP maxDB、Live Cache 和 Content Server 部署](dbms_guide_maxdb.md)
- [Azure 上的 SAP Hana 作業指南](hana-vm-operations.md)
- [Azure 虛擬機器的 SAP Hana 高可用性](sap-hana-availability-overview.md)
- [Azure 虛擬機器上的 SAP HANA 備份指南](sap-hana-backup-guide.md)

