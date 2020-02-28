---
title: SAP Hana Azure 虛擬機器儲存體設定 |Microsoft Docs
description: 已部署 SAP Hana 之 VM 的儲存體建議。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 155498aeaea30bf2da1d5aa0dbcb322aeb43bbdd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661289"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虛擬機器儲存體設定

Azure 提供不同類型的儲存體，適用于執行 SAP Hana 的 Azure Vm。 **SAP Hana 認證的 Azure 儲存體類型**可視為 SAP Hana 部署清單，如下所示： 

- Azure 進階 SSD  
- [Ultra 磁片](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

若要瞭解這些磁片類型，請參閱[選取磁片類型一](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)文

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 在整體情況允許的情況下，請利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。 

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 與所選擇的 Azure 儲存體類型無關，針對特定的作業系統和 DBMS，SAP 必須支援該存放裝置所使用的檔案系統。 [SAP 支援附注 #405827](https://launchpad.support.sap.com/#/notes/405827)列出不同作業系統和資料庫支援的檔案系統，包括 SAP Hana。 這適用于所有的磁片區 SAP Hana 可能會存取任何工作的讀取和寫入。 特別使用 Azure 上的 NFS 進行 SAP Hana 時，如本文稍後所述，將會套用其他 NFS 版本限制 


不同儲存類型的最小 SAP Hana 認證條件如下： 

- Azure 進階 SSD-/hana/log 必須使用 Azure[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)進行快取。 /Hana/data 磁片區可以放在沒有 Azure 寫入加速器或 Ultra 磁片上的進階 SSD
- 至少適用于/hana/log 磁片區的 Azure Ultra 磁片。 /Hana/data 磁片區可以放在沒有 Azure 寫入加速器的進階 SSD 上，或為了取得更快速的磁片重新開機時間
- 適用于/hana/log**和**/Hana/data. 的 Azure NetApp Files 之上的**NFS 4.1**磁片區 /Hana/shared 的數量可以使用 NFS v3 或 NFS 4.1 通訊協定。 NFS 4.1 通訊協定對於/hana/data 和/hana/log 磁片區而言是必要的。

有些儲存類型可以合併。 例如，您可以將/hana/data 放在進階儲存體上，並將/hana/log 放在 Ultra 磁片儲存體上，以取得所需的低延遲。 不過，如果您使用以及 for/hana/data 為基礎的 NFS 4.1 磁片區，則必須使用以及 for/hana/log. 為基礎的另一個 NFS 4.1 磁片區 **不支援**對其中一個磁片區（例如/hana/data）和 Azure 進階儲存體或 Ultra 磁片（例如/hana/log）的及上使用 NFS。

在內部部署環境中，您很少需要在意 i/o 子系統及其功能。 原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 當您自行建立 Azure 基礎結構時，您應該注意其中一些 SAP 發行的需求。 SAP 所需的部分最小輸送量特性，會導致需要：

- 在 250 MB/秒的 **/hana/log**上啟用讀取/寫入（具有 1 mb i/o 大小）
- 針對 **/hana/data** 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動
- 針對 **/hana/data** 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 等 DBMS 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此，您**必須**利用適用于 **/Hana/data**和 **/Hana/log**磁片區的 Azure Premium 磁片。 為了達到如 SAP 想要的 **/hana/log** 和 **/hana/data** 最小輸送量，您必須使用 MDADM 或 LVM 在多個 Azure 進階儲存體磁碟上建置 RAID 0。 然後使用 RAID 磁碟區作為 **/hana/data** 和 **/hana/log** 磁碟區。 

**建議：做為 RAID 0 的等量大小，建議使用：**

- 適用于 **/hana/data**的 256 KB
- 針對 **/hana/log** 使用 32 KB

> [!IMPORTANT]
> /Hana/data 的等量大小已從先前的建議變更，從較舊的 Linux 版本以客戶經驗為基礎，呼叫 64 KB 或 128 KB 至 256 KB。 256 KB 的大小會提供稍微較佳的效能

> [!NOTE]
> 您不需要使用 RAID 磁碟區設定任何備援層級，因為 Azure 進階和標準儲存體會保存三個 VHD 的映像。 RAID 磁碟區的使用方式單純是用來設定會提供足夠 I/O 輸送量的磁碟區。

RAID 下的 Azure VHD 數目累計，是從 IOPS 和儲存體輸送量端累計。 因此，如果您將 RAID 0 放在 3 x P30 Azure 進階儲存體磁碟上，它應該會給您單一 Azure 進階儲存體 P30 磁碟的 3 倍 IOPS 和 3 倍儲存體輸送量。

當調整 VM 大小或決定 VM 時，也請注意整體 VM I/O 輸送量。 [記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中說明整體虛擬機器儲存體輸送量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 透過 Linux 廠商和 SAP 的一般建議是將磁片區的 i/o 排程器模式從**cfq**模式重新設定為**noop** （非 multiqueue）或**none** （multiqueue）模式。 [#1984787 的 SAP 附注](https://launchpad.support.sap.com/#/notes/1984787)中會參考詳細資料。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>適用于 Azure M 系列虛擬機器的進階儲存體和 Azure 寫入加速器解決方案
Azure 寫入加速器是僅適用于 Azure M 系列 Vm 的功能。 如同名稱所示，這個功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 **/hana/log** 磁碟區。 因此， **/hana/data**和 **/hana/log**是不同的磁片區，只有支援 **/hana/log**磁片區的 Azure 寫入加速器。 

> [!IMPORTANT]
> 使用 Azure 進階儲存體時，Azure[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)或/hana/log 磁片區的使用方式是強制的。 寫入加速器僅適用于 premium 儲存體和 M 系列和 Mv2 系列 Vm。

下列快取建議假設 SAP HANA 的 I/O 特性如下所示：

- 幾乎沒有任何針對 HANA 資料檔案的讀取工作負載。 例外是在資料載入 HANA 時，HANA 執行個體重新啟動之後的大型 I/O。 針對資料檔案讀取較大 I/O 的另一種情況可能是 HANA 資料庫備份。 因此，讀取快取大多不合理，因為在大部分的情況下，必須完整讀取所有資料檔案磁碟區。
- 當 HANA 儲存點和 HANA 損毀復原時，對資料檔案的寫入會發生高載。 寫入儲存點是非同步的，且不會造成任何使用者交易延遲。 因為系統必須再次快速回應，所以在損毀復原期間的資料寫入效能極為重要。 不過，損毀復原應該不是例外狀況
- 幾乎不會從 HANA 重做檔案進行任何讀取。 例外是在執行交易記錄備份、損毀復原，或 HANA 執行個體重新啟動階段的大型 I/O。  
- 對於 SAP HANA 重做記錄檔的主要負載是寫入。 根據工作負載的性質，您的 I/O 可以小到 4 KB，而在其他情況下，I/O 大小也可以是 1 MB 或更大。 對 SAP HANA 重做記錄的寫入延遲效能極為重要。
- 所有寫入都必須以可靠的方式保存在磁碟上

**建議：由於 SAP Hana 所觀察到的 i/o 模式，因此使用 Azure 進階儲存體的不同磁片區快取應該設定如下：**

- **/hana/data**：無快取
- **/hana/log** -無快取-M 和 Mv2 系列的例外狀況，其中寫入加速器應該在不讀取快取的情況下啟用。 
- **/hana/shared**：讀取快取

### <a name="production-recommended-storage-solution"></a>生產建議的儲存體解決方案

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是專用於 **/hana/log** 磁碟區的 Azure Write Accelerator。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 **/hana/log** 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

建議通常超過本文稍早所述的 SAP 最低需求。 列出的建議是 SAP 的大小建議與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

**建議：生產環境案例的建議設定如下所示：**

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | /hana/log | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

檢查不同建議磁片區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用超過列出的 Vhd 來調整磁片區大小，會增加 Azure 虛擬機器類型限制內的 IOPS 和 i/o 輸送量。

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 所以至少必須將組成 **/hana/log** 磁碟區的 Azure 進階儲存體磁碟部署為受控磁碟。

Azure 寫入加速器可以支援之每個虛擬機器的 Azure 進階儲存體 VHD 有其上限。 目前的上限是：

- 適用于 M128xx 和 M416xx VM 的16個 Vhd
- 8個適用于 M64xx 和 M208xx VM 的 Vhd
- M32xx VM 上限 4 個 VHD

如需如何啟用 Azure Write Accelerator 的詳細指示，可以在[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中找到。

Azure Write Accelerator 的詳細資料和限制可以在相同文件中找到。

**建議：您必須針對構成/hana/log 磁片區的磁片使用寫入加速器**


### <a name="cost-conscious-azure-storage-configuration"></a>節省成本的 Azure 儲存體組態
下表顯示客戶也用來在 Azure Vm 上裝載 SAP Hana 之 VM 類型的設定。 可能有一些 VM 類型可能無法符合 SAP Hana 的所有最小儲存準則，或未正式支援 SAP SAP Hana。 但是目前這些虛擬機器似乎可以針對非生產案例正常執行。 **/Hana/data**和 **/hana/log**會合並成一個磁片區。 因此，Azure 寫入加速器的使用量可能會成為 IOPS 方面的限制。

> [!NOTE]
> 針對 SAP 支援的案例，請檢查[IAAS 的 sap 檔](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中是否支援 sap 所 SAP Hana 的特定 VM 類型。

> [!NOTE]
> 針對符合成本效益的解決方案，先前的建議變更是從[Azure 標準 HDD 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)移到更好的效能，並更可靠地執行[azure 標準 SSD 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

建議通常超過本文稍早所述的 SAP 最低需求。 列出的建議是 SAP 的大小建議與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GB | 1000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


根據 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，包含 3 個 P20 的較小 VM 類型所建議使用的磁碟，在空間建議方面超過磁碟區大小。 不過，已如資料表中顯示進行選擇，為 SAP Hana 提供足夠的磁碟輸送量。 如果您需要對 **/hana/backup** 磁碟區 (已調整大小來保持備份為記憶體磁碟區的兩倍) 進行變更，您可以自由調整。   
檢查不同建議磁片區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用超過列出的 Vhd 來調整磁片區大小，會增加 Azure 虛擬機器類型限制內的 IOPS 和 i/o 輸送量。 

> [!NOTE]
> 上述組態「不會」受益於 [Azure 虛擬機器的單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因為它是使用 Azure 進階儲存體和 Azure 標準儲存體的混合。 不過，為了最佳化成本而選擇了這個選項。 您必須針對以上列為 Azure 標準儲存體 (Sxx) 的所有磁碟，選擇進階儲存體，讓 VM 組態符合 Azure 單一 VM SLA 的規範。


> [!NOTE]
> 所述磁碟組態建議是以 SAP 對其基礎結構提供者所表示的最低需求為目標。 在實際的客戶部署和工作負載案例中，還是會遇到這些建議仍未能提供足夠功能的情形。 這些情況可能是客戶在 HANA 重新啟動之後需要更快的資料重新載入速度，或者備份組態需要儲存體的更高頻寬。 其他案例包括 **/hana/log**，其中 5000 IOPS 對於特定工作負載來說不足。 因此請將這些建議當作起點，並且根據工作負載的需求來調整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>適用于 SAP Hana 的 Azure Ultra 磁片儲存體設定
Microsoft 正在推出新的 Azure 儲存體類型（稱為[Azure Ultra 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)）。 目前為止所提供的 Azure 儲存體與 Ultra 磁片之間的顯著差異，在於磁片功能不會再系結到磁片大小。 身為客戶，您可以為 Ultra 磁片定義這些功能：

- 磁片的大小，範圍從 4 GiB 到 65536 GiB
- IOPS 的範圍從 100 IOPS 到 160K IOPS （最大值也取決於 VM 類型）
- 從 300 MB/秒到 2000 MB/秒的儲存體輸送量

Ultra 磁片可讓您定義單一磁片，以滿足您的大小、IOPS 和磁片輸送量範圍。 不是在 Azure 進階儲存體上使用邏輯磁片區管理員（例如 LVM 或 MDADM）來建立可滿足 IOPS 和儲存體輸送量需求的磁片區。 您可以在 Ultra 磁片與進階儲存體之間執行設定混合。 因此，您可以將 Ultra 磁片的使用量限制在效能關鍵/hana/data 和/hana/log 磁片區，並使用 Azure 進階儲存體

相較于進階儲存體，Ultra 磁片的其他優點可能是比較好的讀取延遲。 當您想要減少 HANA 啟動時間和後續將資料載入記憶體時，較快的讀取延遲可能會有一些優點。 當 HANA 正在寫入儲存點時，也可以感受到 Ultra 磁片儲存體的優點。 因為/hana/data 的進階儲存體磁片通常不會寫入加速器快取，所以與 Ultra 磁片相比，進階儲存體上/hana/data 的寫入延遲會比較高。 預期使用 Ultra 磁片寫入的儲存點會在 Ultra 磁片上執行得更好。

> [!IMPORTANT]
> Ultra 磁片尚未出現在所有 Azure 區域中，也尚未支援下列所有 VM 類型。 如需可用 Ultra 磁片和支援哪些 VM 系列的詳細資訊，請參閱[Azure 中有哪些磁片類型可供使用？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)一文。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>使用純 Ultra 磁片設定的生產環境建議儲存體解決方案
在此設定中，您會分別保留/hana/data 和/hana/log 磁片區。 建議的值衍生自此 Kpi，而 SAP 必須根據[SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中建議的 SAP Hana 和儲存體設定來認證 VM 類型。

建議通常超過本文稍早所述的 SAP 最低需求。 列出的建議是 SAP 的大小建議與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 磁片區 | /hana/data i/o 輸送量 | /hana/data IOPS | /hana/log 磁片區 | /hana/log i/o 輸送量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1200 MB/秒 | 600 GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GB | 1000 MB/秒 | 600 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1000 GiB | 1000 MB/秒 |  1200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1750 GiB | 1000 MB/秒 | 2100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2000 GiB | 2000 MB/秒 |2400 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3800 GiB | 2000 MB/秒 | 4800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 3500 GB | 1000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 7200 GB | 1000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 7200 GB | 1500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 14400 GB | 1500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

列出的值是一個起點，而且需要針對真正的需求進行評估。 Azure Ultra 磁片的優點是可以調整 IOPS 和輸送量的值，而不需要關閉 VM 或停止套用至系統的工作負載。   

> [!NOTE]
> 到目前為止，無法使用具有 Ultra 磁片儲存體的儲存體快照集。 這會封鎖使用 Azure 備份服務的 VM 快照集

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>使用單純的 Ultra 磁片設定進行成本關注的儲存體解決方案
在此設定中，您會在相同磁片上的/hana/data 和/hana/log 磁片區。 建議的值衍生自此 Kpi，而 SAP 必須根據[SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中建議的 SAP Hana 和儲存體設定來認證 VM 類型。 

建議通常超過本文稍早所述的 SAP 最低需求。 列出的建議是 SAP 的大小建議與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /Hana/data 和/log 的磁片區 | /hana/data 和記錄 i/o 輸送量 | /hana/data 和記錄 IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1200 MB/秒 | 1200 GB | 1200 MBps | 9500 | 
| M32ts | 192 GiB | 500 MB/秒 | 512 GB | 400 MBps | 9500 | 
| M32ls | 256 GiB | 500 MB/秒 | 600 GB | 400 MBps | 9500 | 
| M64ls | 512 GB | 1000 MB/秒 | 1100 GB | 900 MBps | 10,000 | 
| M64s | 1000 GiB | 1000 MB/秒 |  1700 GB | 900 MBps | 10,000 | 
| M64ms | 1750 GiB | 1000 MB/秒 | 2600 GB | 900 MBps | 10,000 | 
| M128s | 2000 GiB | 2000 MB/秒 |2900 GB | 1800 MBps |12,000 | 
| M128ms | 3800 GiB | 2000 MB/秒 | 5300 GB | 1800 MBps |12,000 |  
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 7700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 7700 GB | 1，800MBps | 12,000 |  
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 15000 GB | 1800 MBps | 12,000 |    

列出的值是一個起點，而且需要針對真正的需求進行評估。 Azure Ultra 磁片的優點是可以調整 IOPS 和輸送量的值，而不需要關閉 VM 或停止套用至系統的工作負載。  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 上的 NFS 4.1 磁片區
Azure NetApp Files 提供可用於/hana/shared、/hana/data 和/hana/log 磁片區的原生 NFS 共用。 針對/hana/data 和/hana/log 磁片區使用以及為基礎的 NFS 共用，需要使用 4.1 NFS 通訊協定。 在及上以共用為基礎時，不支援使用/hana/data 和/hana/log 磁片區的 NFS 通訊協定 v3。 

> [!IMPORTANT]
> **不**支援在 Azure NetApp Files 上執行的 NFS v3 通訊協定用於/hana/data 和/hana/log。 從功能的觀點來看，/hana/data 和/hana/log 磁片區的使用 NFS 4.1 是強制的。 而對於/hana/shared 磁片區，可以從功能的觀點來使用 NFS v3 或 NFS 4.1 通訊協定。

### <a name="important-considerations"></a>重要的考量
考慮 SAP Netweaver 和 SAP Hana 的 Azure NetApp Files 時，請注意下列重要考慮：

- 最小容量集區為 4 TiB。  
- 最小磁片區大小為 100 GiB
- Azure NetApp Files 和所有虛擬機器（將裝載 Azure NetApp Files 磁片區）必須位於相同的 Azure 虛擬網路或相同區域的[對等互連虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)中。  
- 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網。
- Azure NetApp volume 的輸送量是磁片區配額和服務等級的功能，如[Azure Netapp Files 的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 調整 HANA Azure NetApp 磁片區的大小時，請確定產生的輸送量符合 HANA 系統需求。  
- Azure NetApp Files 提供[匯出原則](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：您可以控制允許的用戶端、存取類型（讀取 & 寫入、唯讀等等）。 
- Azure NetApp Files 功能尚無法感知區域。 Azure NetApp Files 功能目前不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能的延遲含意。  
- 請務必將虛擬機器部署在接近 Azure NetApp storage 的鄰近性，以獲得低延遲。 
- <b>Sid</b>Adm 的使用者識別碼和虛擬機器上 `sapsys` 的群組識別碼必須符合 Azure NetApp Files 中的設定。 

> [!IMPORTANT]
> 針對 SAP Hana 工作負載，低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁片區已部署在接近的附近。  

> [!IMPORTANT]
> 如果<b>sid</b>Adm 的使用者識別碼與虛擬機器與 Azure NetApp 設定之間 `sapsys` 的群組識別碼不相符，則在虛擬機器上裝載的 Azure NetApp 磁片區上的檔案許可權將會顯示為 `nobody`。 [在將新系統](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)上線至 Azure NetApp Files 時，請務必指定<b>sid</b>ADM 的正確使用者識別碼，以及 `sapsys`的群組識別碼。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>針對 Azure NetApp Files 上的 HANA 資料庫進行大小調整

Azure NetApp volume 的輸送量是磁片區大小和服務層級的功能，如[Azure Netapp Files 的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

當您在 Azure 中設計 SAP 的基礎結構時，您應該注意 SAP 的一些最低儲存體輸送量需求，這會轉譯成的最小輸送量特性：

- 在 250 MB/秒的/hana/log 上啟用讀取/寫入（具有 1 MB i/o 大小）  
- 為/hana/data 啟用至少 400 MB/秒的讀取活動，適用于 16 MB 和 64 MB 的 i/o 大小  
- 針對具有 16 MB 和 64 MB i/o 大小的/hana/data，啟用至少 250 MB/秒的寫入活動  

每1個磁片區配額 TiB 的[Azure NetApp Files 輸送量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)為：
- 進階儲存體層-64 MiB/秒  
- Ultra 儲存層-128 MiB/秒  

為了符合資料和記錄的 SAP 最低輸送量需求，以及根據 `/hana/shared`的指導方針，建議的大小如下所示：

| 磁碟區 | 大小<br /> 進階儲存體層 | 大小<br /> Ultra 儲存層 | 支援的 NFS 通訊協定 |
| --- | --- | --- |
| /hana/log | 4 TiB | 2 TiB | 4\.1 版 |
| /hana/data | 6.3 TiB | 3.2 TiB | 4\.1 版 |
| HANA/shared | 每4個背景工作節點最大值（512 GB、1xRAM） | 每4個背景工作節點最大值（512 GB、1xRAM） | v3 或4.1 版 |


> [!NOTE]
> 此處所述的 Azure NetApp Files 大小建議以符合 SAP 向其基礎結構提供者表示的最低需求。 在實際的客戶部署和工作負載案例中，這可能不夠。 根據您特定工作負載的需求，使用這些建議作為起點並進行調整。  

因此，您可以考慮針對已針對 Ultra 磁片儲存體列出的及磁片區，部署類似的輸送量。 也請考慮針對不同 VM Sku 的磁片區所列出的大小大小，如已在 Ultra 磁片資料表中完成。

> [!TIP]
> 您可以動態重新調整 Azure NetApp Files 磁片區的大小，而不需要 `unmount` 磁片區、停止虛擬機器或停止 SAP Hana。 這可讓您彈性地同時滿足應用程式的預期和未預期的輸送量需求。

說明如何使用及中裝載的 Azure Vm 上的待命節點來部署具有待命節點的 SAP Hana 向外延展設定的檔，並使用[Azure 虛擬機器上的 Azure NetApp Files 在 SUSE Linux Enterprise Server 上 SAP Hana 向外](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)延展。


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱

- [適用于 Azure 虛擬機器的 SAP Hana 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
