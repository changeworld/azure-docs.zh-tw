---
title: SAP Hana Azure 虛擬機器儲存體設定 | Microsoft Docs
description: 針對已部署 SAP Hana 的 VM 提供儲存體建議。
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
ms.date: 08/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5497f50f9e868338541143a18ab0c83f32c1d1b
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080519"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虛擬機器儲存體設定

Azure 針對執行 SAP Hana 的 Azure VM，提供不同儲存體類型。 **SAP Hana 認證的 Azure 儲存體類型**，可考慮用於 SAP Hana 部署清單，例如： 

- Azure premium SSD 或 premium 儲存體 
- [Ultra 磁碟](../../linux/disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

若要瞭解這些磁片類型，請參閱[適用于 SAP 工作負載的 Azure 儲存體類型](./planning-guide-storage.md)一文和[選取磁片類型](../../linux/disks-types.md)

Azure 為 Azure Standard 和 premium 儲存體上的 Vhd 提供兩種部署方法。 我們希望您可以利用[azure 受控磁片](https://azure.microsoft.com/services/managed-disks/)來進行 azure 區塊儲存體部署。 

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 無論所選擇的 Azure 儲存體類型為何，適用於特定作業系統和 DBMS 的 SAP 必須可支援該存放裝置所使用的檔案系統。 [SAP 支援附註 #405827](https://launchpad.support.sap.com/#/notes/405827) 會列出不同作業系統和資料庫 (包括 SAP Hana) 支援的檔案系統。 這適用於所有 SAP Hana 可能會存取以進行任何工作讀取和寫入的磁碟區。 特別是在適用於 SAP Hana 的 Azure 上使用 NFS，這將會套用其他 NFS 版本限制，如本文稍後所述 


不同儲存類型的最低 SAP Hana 認證條件如下： 

- Azure premium 儲存體- **/hana/log**必須受到 azure[寫入加速器](../../linux/how-to-enable-write-accelerator.md)支援。 **/Hana/data**磁片區可以放在沒有 Azure 寫入加速器或 Ultra 磁片上的 premium 儲存體上
- 至少適用于 **/hana/log**磁片區的 Azure Ultra 磁片。 **/Hana/data**磁片區可以放在沒有 Azure 寫入加速器或的 premium 儲存體上，以獲得更快速的重新開機時間（Ultra 磁片）
- 適用于 **/hana/log 和/hana/data**的 Azure NetApp Files 頂端的**NFS 4.1**磁片區。 /Hana/shared 的數量可以使用 NFS v3 或 NFS 4.1 通訊協定

有些儲存體類型可以合併。 例如，您可以將 **/hana/data**放到高階儲存體，並將 **/Hana/log**放在 Ultra 磁片儲存體上，以取得所需的低延遲。 如果您使用以及為基礎的磁片區來進行 **/hana/data**，則 **/hana/log**磁片區也必須以及上的 NFS 為基礎。 在及上使用 NFS 的其中一個磁片區 (例如/hana/data) 和 Azure premium 儲存體，或其他磁片區的 Ultra 磁片 (例如 **/hana/log**) ，則**不受支援**。

在內部部署環境中，您很少需要在意 I/O 子系統及其功能。 原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 當您自行建置 Azure 基礎結構時，您應該對這些 SAP 發出的需求有些了解。 SAP 所建議的部分最小輸送量特性如下：

- 以 1 MB i/o 大小 **/hana/log** 250 MB/秒的讀取/寫入
- 至少每秒 400 MB 的讀取活動， **/hana/data**為 16 mb 和 64 mb 的 i/o 大小
- 至少 250 MB/秒的寫入活動，適用于 **/hana/data**的 16 mb 和 64 mb i/o 大小

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 等 DBMS 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此，您**必須**將 Azure premium 儲存體、Ultra 磁片或及用於 **/hana/data**和 **/hana/log**磁片區。 


為 HANA 選取儲存體設定的一些指導原則，如下所示：

- 根據[SAP 工作負載的 Azure 儲存體類型](./planning-guide-storage.md)決定儲存體類型，並[選取磁片類型](../../linux/disks-types.md)
- 調整大小或決定 VM 時，會記住整體 VM i/o 輸送量和 IOPS 限制。 整體 VM 儲存體輸送量記載于[記憶體優化的虛擬機器大小](../../sizes-memory.md)一文中
- 在決定存放裝置設定時，請嘗試使用您的 **/hana/data**磁片區設定，保持在 VM 的整體輸送量之下。 寫入儲存點，SAP Hana 可以是積極的發行 i/o。 當您撰寫儲存點時，很容易就能推送至 **/hana/data**磁片區的輸送量限制。 如果您的磁片 () 建立 **/hana/data**磁片區的輸送量高於您的 VM 所允許的輸送量，您可能會遇到儲存點寫入所使用的輸送量與重做記錄檔寫入的輸送量需求的情況。 可能會影響應用程式輸送量的情況
- 如果您使用的是 Azure premium 儲存體，最便宜的設定是使用邏輯磁片區管理員來建立等量集合，以建立 **/hana/data**和 **/hana/log**磁片區

> [!IMPORTANT]
> 儲存體設定的建議是開始使用的指示。 執行工作負載和分析儲存體使用模式，您可能會發現未使用所有的儲存體頻寬或提供的 IOPS。 您可以考慮在儲存體上縮減。 或者，相反地，您的工作負載可能需要比使用這些設定所建議的更多儲存體輸送量。 因此，您可能需要部署更多的容量、IOPS 或輸送量。 在所需儲存體容量之間的張力欄位中，需要儲存體延遲、需要的儲存體輸送量和 IOPS，以及成本最低的設定，Azure 提供足夠的不同儲存體類型，具有不同的功能和不同的價格點，以尋找並調整為您和您的 HANA 工作負載所需的正確危害。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 Linux 廠商和 SAP 的一般建議是重新設定磁碟區的 I/O 排程器模式，從 **mq-deadline** 或 **kyber** 模式設定為 **noop** (非 multiqueue) 或 **none** (multiqueue) 模式。 詳細資料記載於 [SAP 附註編號 #1984787](https://launchpad.support.sap.com/#/notes/1984787)。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>適用于 Azure M 系列虛擬機器的 premium 儲存體和 Azure 寫入加速器解決方案
Azure 寫入加速器是專用於 Azure M 系列 VM 的功能。 如其名稱所示，此功能的目的是為了改善對 Azure premium 儲存體進行寫入的 i/o 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 **/hana/log** 磁碟區。 因此， **/hana/data** 和 **/hana/log** 是不同的磁碟區，而 Azure 寫入加速器僅支援 **/hana/log** 磁碟區。 

> [!IMPORTANT]
> 使用 Azure premium 儲存體時， **/hana/log**磁片區的 azure[寫入加速器](../../linux/how-to-enable-write-accelerator.md)使用方式是必要的。 寫入加速器僅適用于 premium 儲存體和 M 系列和 Mv2 系列 Vm。 寫入加速器不會與其他 Azure VM 系列（例如 Esv3 或 Edsv4）搭配運作。

下列 Azure premium 磁片的快取建議假設 SAP Hana 該清單的 i/o 特性如下所示：

- 幾乎沒有任何針對 HANA 資料檔案的讀取工作負載。 例外是在資料載入 HANA 時，HANA 執行個體重新啟動之後的大型 I/O。 針對資料檔案讀取較大 I/O 的另一種情況可能是 HANA 資料庫備份。 因此，讀取快取大多不合理，因為在大部分的情況下，必須完整讀取所有資料檔案磁碟區。 
- 當 HANA 儲存點和 HANA 損毀復原時，對資料檔案的寫入會發生高載。 寫入儲存點是非同步的，且不會造成任何使用者交易延遲。 因為系統必須再次快速回應，所以在損毀復原期間的資料寫入效能極為重要。 不過，損毀復原應該不是例外狀況
- 幾乎不會從 HANA 重做檔案進行任何讀取。 例外是在執行交易記錄備份、損毀復原，或 HANA 執行個體重新啟動階段的大型 I/O。  
- 對於 SAP HANA 重做記錄檔的主要負載是寫入。 根據工作負載的性質，您的 I/O 可以小到 4 KB，而在其他情況下，I/O 大小也可以是 1 MB 或更大。 對 SAP HANA 重做記錄的寫入延遲效能極為重要。
- 所有寫入都必須以可靠的方式保存在磁碟上

**建議：由於 SAP Hana 所觀察到的 i/o 模式，因此使用 Azure premium 儲存體的不同磁片區快取應該設定如下：**

- **/hana/data** -無快取或讀取快取
- **/hana/log** - 無快取，但 M 和 Mv2 系列除外，其中寫入加速器可在不需要讀取快取的情況下啟用。 
- **/hana/shared**：讀取快取
- **OS 磁片**-請勿變更 AZURE 在 VM 建立時所設定的預設快取


如果您使用 LVM 或 mdadm 跨數個 Azure premium 磁片建立等量集合，則需要定義等量大小。 這些大小在 **/hana/data**與 **/hana/log**之間有所不同。 **建議：做為等量大小建議使用：**

- 針對 **/hana/data** 使用 256 KB
- 適用于 **/hana/log**的 64 KB

> [!NOTE]
> **/Hana/data**的等量大小已從先前的建議變更，從較舊的 Linux 版本以客戶經驗為基礎，呼叫 64 kb 或 128 KB 至 256 KB。 256 KB 的大小提供稍微較佳的效能。 我們也將 **/hana/log**的等量大小建議，從 32 kb 變更為 64 KB，以取得具有較大 i/o 大小的足夠輸送量。

> [!NOTE]
> 您不需要使用 RAID 磁片區來設定任何冗余層級，因為 Azure 區塊儲存體會保留三個 VHD 映射。 使用包含 Azure premium 磁片的等量集，純粹是用來設定磁片區，以提供足夠的 IOPS 和/或 i/o 輸送量。

在等量集合底下累積一些 Azure Vhd，會從 IOPS 和儲存體輸送量端累加。 因此，如果您將等量集合放在超過 3 x P30 的 Azure premium 儲存體磁片上，它應該會提供您單一 Azure premium 儲存體 P30 磁片的三倍 IOPS 和儲存體輸送量三倍。

> [!IMPORTANT]
> 如果您使用 LVM 或 mdadm 作為磁片區管理員，在多個 Azure premium 磁片上建立等量集合，則三個 SAP Hana 檔案系統/data、/log 和/shared 不能放在預設或根磁片區群組中。 強烈建議您遵循 Linux 廠商的指導方針，這通常會引導您針對 /data、/log 和/shared 建立個別的磁碟區群組。


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium 儲存體的 Azure 高載功能
針對容量較小或等於 512 GiB 的 Azure premium 儲存體磁片，會提供高載功能。 磁片高載運作方式的確切方式如下所[述。](../../linux/disk-bursting.md) 當您閱讀文章時，您瞭解當 i/o 工作負載低於磁片的名義 IOPS 和輸送量時，產生 IOPS 和輸送量的概念 (如需有關名義輸送量的詳細資訊，請參閱[受控磁片定價](https://azure.microsoft.com/pricing/details/managed-disks/)) 。 您將會在目前的使用量和磁片的名義值之間，累積 IOPS 和輸送量的差異。 高載限制為30分鐘。

在中，可規劃此高載功能的理想情況可能是包含不同 DBMS 資料檔案的磁片區。 對這些磁片區預期的 i/o 工作負載，特別是小型到中型的系統，應該看起來像這樣：

- 較低到適中的讀取工作負載，因為資料的理想情況是快取在記憶體中，或類似于 HANA 的情況應該完全在記憶體中
- 定期發出的資料庫檢查點或儲存點所觸發的寫入的高載
- 在不透過儲存體快照集執行備份的情況下，讀取連續串流的備份工作負載
- 針對 SAP Hana，在實例重新開機後將資料載入記憶體中

特別是在您的工作負載只處理每秒數百筆交易的較小 DBMS 系統上，這類高載功能對於儲存交易或重做記錄的磁片或磁片區也很有意義。 針對這類磁片或磁片區所預期的工作負載看起來像這樣：

- 定期寫入相依于工作負載的磁片和工作負載的本質，因為應用程式所發出的每個認可都可能會觸發 i/o 作業
- 作業工作案例的輸送量較高（例如建立或重建索引）
- 執行交易記錄或重做記錄備份時，讀取高載


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>以 Azure premium 儲存體為基礎的生產環境建議儲存體解決方案

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是專用於 **/hana/log** 磁碟區的 Azure Write Accelerator。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 **/hana/log** 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 在牽涉到 Azure premium 儲存體的案例中，我們會在設定中執行高載功能。 當您使用任何圖形或表單的儲存體測試控管時，請記住[Azure premium 磁片](../../linux/disk-bursting.md)高載的運作方式。 執行透過 SAP HWCCT 或 HCMT 工具傳遞的存放裝置測試時，我們不會預期所有測試都會通過準則，因為有些測試會超過您可以累積的高載點數。 特別是當所有測試都在不中斷的情況下執行時。


> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

**建議：適用于生產環境之 Azure premium 儲存體的建議設定如下所示：**

SAP **/hana/data**磁片區的設定：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | 最大高載輸送量 | IOPS | 高載 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M64ls | 512 GB | 1,000 MBps | 4 x P10 |  680 MBps | 2,000 | 14,000 |
| M64s | 1,000 GiB | 1,000 MBps | 4 x P15 | 680 MBps | 4400 | 14,000 |
| M64ms | 1,750 GiB | 1,000 MBps | 4 x P20 | 680 MBps | 9200 | 14,000 |  
| M128s | 2,000 GiB | 2000 MBps | 4 x P20 | 680 MBps | 9200| 14,000 | 
| M128ms | 3,800 GiB | 2000 MBps | 4 x P30 | 800 MBps (布建的)  | 20,000 | 無高載 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 4 x P30 | 800 MBps (布建的)  | 20,000| 無高載 | 
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 4 x P40 | 1000 MBps (布建的)  | 25,000 | 無高載 |
| M416s_v2 | 5,700 GiB | 2000 MBps | 4 x P40 | 1000 MBps (布建的)  | 25,000 | 無高載 |
| M416ms_v2 | 11,400 GiB | 2000 MBps | 4 x P50 | 2000 MBps (布建的)  | 25,000 | 無高載 |


適用于 **/hana/log**磁片區。 設定看起來會像這樣：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | **/hana/log**磁片區 | 最大高載輸送量 | IOPS | 高載 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M64ls | 512 GB | 1,000 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M64s | 1,000 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 
| M64ms | 1,750 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M128s | 2,000 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500|  
| M128ms | 3,800 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M416s_v2 | 5,700 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M416ms_v2 | 11,400 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 


若是其他磁片區，設定看起來會像這樣：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | HANA/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P20 | 1 x P6 | 1 x P6 |
| M64ls | 512 GB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1,000 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1,750 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2,000 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3,800 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2,850 GiB | 1,000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5,700 GiB | 2000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11,400 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


請針對不同的建議磁碟區，檢查儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更高的磁片區來進行 **/hana/data**和 **/hana/log**，您需要增加 Azure premium 儲存體 vhd 的數目。 使用比列出數目更多的 VHD 來調整磁碟區大小，會增加在 Azure 虛擬機器類型限制內的 IOPS 和 I/O 輸送量。

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 因此，至少必須將形成 **/hana/log**磁片區的 Azure premium 儲存體磁片部署為受控磁片。 如需更詳細的 Azure 寫入加速器指示和限制，請參閱[寫入加速器](../../linux/how-to-enable-write-accelerator.md)文章。

針對 Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series)系列和[Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)的 HANA 認證 vm，您必須針對 **/HANA/DATA**和 **/hana/log**磁片區及。 或者，您必須使用 Azure Ultra 磁片儲存體，而不是僅適用于 **/hana/log**磁片區的 azure premium 儲存體。 因此，Azure premium 儲存體上 **/hana/data**磁片區的設定可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | 最大高載輸送量 | IOPS | 高載 IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  510 MBps | 1,500 | 10500|
| E48ds_v4 | 384 GiB | 1152 MBps | 3 x P15 |  510 MBps | 3300  | 10500 | 
| E64ds_v4 | 504 GiB | 1,200 MBps | 3 x P15 |  510 MBps | 3300 | 10500 | 
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 3 x P15 |  510 MBps | 3300 | 10500 | 

針對其他磁片區（包括 Ultra 磁片上的 **/hana/log** ），設定可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/log 磁碟區 | /hana/log I/O 輸送量 | /hana/log IOPS | HANA/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1152 MBps | 192 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1,200 MBps | 256 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1,200 MBps | 220 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>適用於 SAP Hana 的 Azure Ultra 磁碟儲存體設定
另一個 Azure 儲存體類型稱為「 [Azure Ultra 磁片](../../windows/disks-types.md#ultra-disk)」。 迄今所提供的 Azure 儲存體與 Ultra 磁碟之間的顯著差異，在於磁碟功能不會再繫結到磁碟大小。 身為客戶的您可以為 Ultra 磁碟定義這些功能：

- 磁碟的大小，範圍從 4 GiB 到 65,536 GiB
- IOPS 的範圍從 100 IOPS 到 160K IOPS (最大值也取決於 VM 類型)
- 從 300 MB/秒到 2,000 MB/秒的儲存體輸送量

Ultra 磁碟可讓您定義滿足大小、IOPS 和磁碟輸送量範圍的單一磁碟。 不是在 Azure premium 儲存體上使用邏輯磁片區管理員（例如 LVM 或 MDADM）來建立可滿足 IOPS 和儲存體輸送量需求的磁片區。 您可以在 Ultra 磁片和 premium 儲存體之間執行設定混合。 如此一來，您可以將 Ultra 磁片的使用量限制在效能關鍵 **/hana/data**和 **/hana/log**磁片區，並使用 Azure premium 儲存體來涵蓋其他磁片區

相較于 premium 儲存體，Ultra 磁片的其他優點可能是更好的讀取延遲。 較快的讀取延遲在減少 HANA 啟動時間和後續的記憶體資料載入上都會帶來好處。 您也可以在 HANA 寫入儲存點時，感受到 Ultra 磁碟儲存體的優點。 

> [!NOTE]
> Ultra 磁碟尚未出現在所有 Azure 區域中，也尚未支援下列所有 VM 類型。 如需 Ultra 磁碟可用於何處和支援哪些 VM 系列的詳細資訊，請參閱下列文章：[在 Azure 中可使用哪些磁碟類型？](../../windows/disks-types.md#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>生產環境建議的儲存體解決方案和純 Ultra 磁碟設定
在此設定中，您會分別保留 **/hana/data**和 **/hana/log**磁片區。 建議的值衍生自此 KPI，而 SAP 必須依照 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建議，為 SAP Hana 和儲存體設定認證 VM 類型。

建議通常會超過本文稍早所述的 SAP 最低需求。 所列建議是 SAP 建議的大小與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 磁碟區 | /hana/data I/O 輸送量 | /hana/data IOPS | /hana/log 磁碟區 | /hana/log I/O 輸送量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 200 GB | 400 MBps | 2,500 | 80 GB | 250 MB | 1,800 |
| E32ds_v4 | 256 GiB | 768 MB/秒 | 300 GB | 400 MBps | 2,500 | 128 GB | 250 MBps | 1,800 |
| E48ds_v4 | 384 GiB | 1152 MB/秒 | 460 GB | 400 MBps | 3,000 | 192 GB | 250 MBps | 1,800 |
| E64ds_v4 | 504 GiB | 1200 MB/秒 | 610 GB | 400 MBps | 3,500 |  256 GB | 250 MBps | 1,800 |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 610 GB | 400 MBps | 3,500 | 220 GB | 250 MB | 1,800 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 2,500 | 96 GB | 250 MBps  | 1,800 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 2,500 | 256 GB | 250 MBps  | 1,800 |
| M64ls | 512 GB | 1,000 MB/秒 | 620 GB | 400 MBps | 3,500 | 256 GB | 250 MBps  | 1,800 |
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M128s | 2,000 GiB | 2,000 MB/秒 |2,400 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 750 MBps |7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 4,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 9,000 | 512 GB | 400 MBps  | 4,000 |   

**列出的值應視為一個起點，而且需要針對真正的需求進行評估。** Azure Ultra 磁碟的優點是可以調整 IOPS 和輸送量的值，而不需要關閉 VM 或停止套用至系統的工作負載。   

> [!NOTE]
> 到目前為止，您還無法使用具有 Ultra 磁碟儲存體的儲存體快照集。 這會讓您無法使用 Azure 備份服務的 VM 快照集


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 上的 NFS v4.1 磁碟區
Azure NetApp Files 提供可用於 **/hana/shared**、 **/hana/data**和 **/hana/log**磁片區的原生 NFS 共用。 針對 **/hana/data**和 **/hana/log**磁片區使用以及為基礎的 nfs 共用，需要使用 4.1 nfs 通訊協定。 在及上以共用為基礎時，不支援使用 **/hana/data**和 **/hana/log**磁片區的 NFS 通訊協定 v3。 

> [!IMPORTANT]
> **不**支援在 Azure NetApp Files 上執行的 NFS v3 通訊協定用於 **/hana/data**和 **/hana/log**。 從功能的觀點來看， **/hana/data**和 **/hana/log**磁片區的使用 NFS 4.1 是強制的。 而對於 **/hana/shared**磁片區，可以從功能的觀點來使用 nfs V3 或 nfs 4.1 通訊協定。

### <a name="important-considerations"></a>重要考量︰
考慮到適用於 SAP Netweaver 和 SAP Hana 的 Azure NetApp Files 時，請注意下列重要考量：

- 最小容量集區為 4 TiB。  
- 最小磁碟區大小為 100 GiB
- Azure NetApp Files 和所有虛擬機器 (將裝載 Azure NetApp Files 磁碟區的位置) 必須位於相同的 Azure 虛擬網路，或位於相同區域的[對等互連虛擬網路](../../../virtual-network/virtual-network-peering-overview.md)中。  
- 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網路。
- Azure NetApp 磁碟區的輸送量是磁碟區配額的功能和服務等級，如 [Azure NetApp Files 的服務等級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 調整 HANA Azure NetApp 磁碟區的大小時，請確定產生的輸送量符合 HANA 系統需求。  
- Azure NetApp Files 提供[匯出原則](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)：您可以控制允許的用戶端、存取類型 (讀取及寫入、唯讀等等)。 
- Azure NetApp Files 功能尚無法感知區域。 Azure NetApp Files 功能目前不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能出現的延遲情形。  
- 請務必將虛擬機器部署在 Azure NetApp 儲存體附近，以降低延遲。 
- <b>sid</b>adm 的使用者識別碼，以及虛擬機器上 `sapsys` 的群組識別碼，都必須符合 Azure NetApp Files 中的設定。 

> [!IMPORTANT]
> 針對 SAP Hana 工作負載，低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁碟區已部署在附近。  

> [!IMPORTANT]
> 如果 <b>sid</b>adm 的使用者識別碼與虛擬機器和 Azure NetApp 設定之間的 `sapsys` 群組識別碼不符，則 Azure NetApp 磁碟區 (裝載於虛擬機器) 上的檔案權限將會顯示為 `nobody`。 [將新系統上線](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)至 Azure NetApp Files 時，請務必為 <b>sid</b>adm 指定正確的使用者識別碼，以及為 `sapsys` 指定群組識別碼。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>針對 Azure NetApp Files 上的 HANA 資料庫進行大小調整

Azure NetApp 磁碟區的輸送量是磁碟區大小的功能和服務等級，如 [Azure NetApp Files 的服務等級](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

當您在 Azure 中設計 SAP 的基礎結構時，您應該注意 SAP 的一些最低儲存體輸送量需求，這會轉譯成下列項目的最低輸送量特性：

- 在 **/hana/log** 上啟用 250MB/秒及 1 MB I/O 大小的讀取/寫入  
- 針對 **/hana/data** 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動  
- 針對 **/hana/data** 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動  

每 1 TiB 磁碟區配額的 [Azure NetApp Files 輸送量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)如下：
- Premium 儲存層-64 MiB/秒  
- Ultra 儲存層 - 128 MiB/秒  

> [!IMPORTANT]
> 與您在單一 NFS 磁碟區上部署的容量不同，取用者在虛擬機器中所用的輸送量峰值預期會出現在 1.2-1.4 GB/秒的頻寬範圍內。 這必須搭配 ANF 供應項目的基礎結構和 NFS 周圍相關的 Linux 工作階段限制。 [適用於Azure NetApp Files 的效能基準測試](../../../azure-netapp-files/performance-benchmarks-linux.md)一文所述的效能和輸送量數目是針對一個具有多個用戶端 VM 的共用 NFS 磁碟區進行，因此有多個工作階段。 這種情況與我們在 SAP 中測量的案例不同。 我們會從單一 VM 中對 NFS 磁碟區測量輸送量。 裝載於 ANF 上。

為了符合資料和記錄的 SAP 最低輸送量需求，以及遵循 `/hana/shared` 的指導方針，建議的大小如下：

| 磁碟區 | 大小<br /> 進階儲存層 | 大小<br /> Ultra 儲存層 | 支援的 NFS 通訊協定 |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| HANA/shared | 每 4 個背景工作節點最大值 (512 GB、1xRAM) | 每 4 個背景工作節點最大值 (512 GB、1xRAM) | v3 或 4.1 版 |


> [!NOTE]
> 此處所述的 Azure NetApp Files 大小建議是為了符合 SAP 向其基礎結構提供者表示的最低需求。 在實際的客戶部署和工作負載案例中，這可能不夠。 請將這些建議當作起點，並且根據特定工作負載的需求來調整。  

因此，您可以考慮為已針對 Ultra 磁碟儲存體列出的 ANF 磁碟區，部署類似的輸送量。 針對大小，也請考慮 Ultra 磁碟資料表中已針對不同 VM SKU 列出的磁碟區大小。

> [!TIP]
> 您可以動態地重新調整 Azure NetApp Files 磁碟區大小，而不需要 `unmount` 磁碟區、停止虛擬機器或停止 SAP Hana。 這可讓您彈性地滿足應用程式上預期和未預期的輸送量需求。

[使用SUSE Linux Enterprise Server 上的 Azure NetApp Files，在 Azure VM 上使用待命節點來擴展 SAP Hana](./sap-hana-scale-out-standby-netapp-files-suse.md) 中，已發佈如何使用 ANF 中所裝載的 NFS v4.1 磁碟區，搭配待命節點來部署 SAP Hana 延展設定的方法。


## <a name="cost-conscious-solution-with-azure-premium-storage"></a>使用 Azure premium 儲存體的成本在乎解決方案
到目前為止，本檔中所述的 Azure premium 儲存體解決方案會在[適用于 Azure M 系列虛擬機器的高階儲存體和 azure 寫入加速器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines)一節中說明，這適用于 SAP Hana 生產支援的案例。 生產環境支援設定的其中一項特性是區隔磁片區，以 SAP Hana 資料和重做記錄到兩個不同的磁片區。 這類分離的原因是磁片區上的工作負載特性不相同。 因此，使用建議的生產環境設定時，可能需要不同類型的快取或甚至是不同類型的 Azure 區塊儲存體。 使用 Azure 區塊儲存體目標的生產支援設定也會與[azure 虛擬機器的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)相容。  針對非生產案例，針對生產系統所採取的一些考慮可能不適用於較低的非生產系統。 因此，可以合併 HANA 資料和記錄磁片區。 但最後有一些原因，例如最終不會符合生產系統所需的特定輸送量或延遲 Kpi。 在這類環境中降低成本的另一個層面，就是[Azure 標準 SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage#azure-standard-ssd-storage)的使用量。 雖然會使[Azure 虛擬機器的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)失效的選擇。 

這類設定成本較低的替代方式如下所示：


| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| E16v3 | 128 GB | 384 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM 類型未通過 HANA 認證 <br /> 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 5000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM 類型未通過 HANA 認證 <br /> 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 5000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| E64v3 | 432 GiB | 1,200 MB/秒 | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/秒 |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 將無法達到小於1毫秒儲存體延遲<sup>1</sup> |
| M64ls | 512 GB | 1,000 MB/秒 | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M64s | 1,000 GiB | 1,000 MB/秒 | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M128s | 2,000 GiB | 2,000 MB/秒 |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 20000<sup>2</sup> |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 20000<sup>2</sup> |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 20000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 將寫入加速器用於結合的資料和記錄磁片區，會將 IOPS 速率限制為 20000<sup>2</sup> |


<sup>1</sup> [Azure 寫入加速器](../../linux/how-to-enable-write-accelerator.md)不能與 Ev4 和 Ev4 VM 系列搭配使用。 由於使用 Azure premium 儲存體的結果，i/o 延遲不會小於1毫秒

<sup>2</sup> VM 系列支援[Azure 寫入加速器](../../linux/how-to-enable-write-accelerator.md)，但寫入加速器的 IOPS 限制可能會限制磁片設定 iops 功能

在結合資料和記錄磁片區以進行 SAP Hana 時，建立等量磁片區的磁片不應該啟用讀取快取或讀寫快取。

列出的 VM 類型未通過 SAP 認證，因此未列在中，因此稱為[SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 客戶的意見反應是那些未列出的 VM 類型已針對某些非生產工作成功使用。


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱

- [Azure 虛擬機器的 SAP Hana 高可用性指南](./sap-hana-availability-overview.md)。
