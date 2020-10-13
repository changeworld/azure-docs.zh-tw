---
title: SAP Hana Azure 虛擬機器儲存體設定 | Microsoft Docs
description: 針對已部署 SAP Hana 的 VM 提供儲存體建議。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP，Azure HANA，儲存體 Ultra 磁片，Premium 儲存體
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9194b461cdceab889e1dfd20e3e70f3f69cb4369
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978249"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虛擬機器儲存體設定

Azure 針對執行 SAP Hana 的 Azure VM，提供不同儲存體類型。 **SAP Hana 認證的 Azure 儲存體類型**，可考慮用於 SAP Hana 部署清單，例如： 

- Azure premium SSD 或 premium 儲存體 
- [Ultra 磁碟](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

若要深入瞭解這些磁片類型，請參閱[AZURE 儲存體 SAP 工作負載類型](./planning-guide-storage.md)和[選取磁片類型](../../disks-types.md)的相關文章

Azure 針對 Azure Standard 和 premium 儲存體上的 Vhd 提供兩種部署方法。 我們希望您利用 [azure 受控磁片](https://azure.microsoft.com/services/managed-disks/) 來進行 azure 區塊儲存體部署。 

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 無論所選擇的 Azure 儲存體類型為何，適用於特定作業系統和 DBMS 的 SAP 必須可支援該存放裝置所使用的檔案系統。 [SAP 支援附註 #405827](https://launchpad.support.sap.com/#/notes/405827) 會列出不同作業系統和資料庫 (包括 SAP Hana) 支援的檔案系統。 這適用於所有 SAP Hana 可能會存取以進行任何工作讀取和寫入的磁碟區。 特別是在適用於 SAP Hana 的 Azure 上使用 NFS，這將會套用其他 NFS 版本限制，如本文稍後所述 


不同儲存類型的最低 SAP Hana 認證條件如下： 

- Azure[寫入加速器](../../how-to-enable-write-accelerator.md)必須支援 azure premium 儲存體- **/hana/log** 。 **/Hana/data**磁片區可放置於沒有 Azure 寫入加速器或 Ultra 磁片上的 premium 儲存體
- 至少適用于 **/hana/log** 磁片區的 Azure Ultra 磁片。 **/Hana/data**磁片區可以放在不含 Azure 寫入加速器的 premium 儲存體上，或以更快速的方式取得 Ultra 磁片的重新開機時間
- 適用于 **/hana/log 和/hana/data**之 Azure NetApp Files 頂端的**NFS 4.1**磁片區。 /Hana/shared 的數量可以使用 NFS v3 或 NFS 4.1 通訊協定

有些儲存體類型可以合併。 例如，您可以將 **/hana/data** 放到 premium 儲存體，而 **/hana/log** 可放置在 Ultra 磁片儲存體上，以便取得所需的低延遲。 如果您使用以 ANF **/hana/data**為基礎的磁片區，  **/hana/log** 磁片區也必須以 ANF 上的 NFS 為基礎。 在 ANF 上針對其中一個磁片區使用 NFS (例如/hana/data) ，以及其他磁片區的 Azure premium 儲存體或 Ultra 磁片 (例如 **/hana/log**) 不 **受支援**。

在內部部署環境中，您很少需要在意 I/O 子系統及其功能。 原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 當您自行建置 Azure 基礎結構時，您應該對這些 SAP 發出的需求有些了解。 SAP 所建議的一些最小輸送量特性如下：

- 以 1 MB i/o 大小 **/hana/log** 每秒 250 MB 的讀取/寫入
- 針對 **/hana/data** 16 mb 和 64 MB 的 i/o 大小，至少每秒 400 MB 的讀取活動
- 至少每秒 250 MB 的寫入活動，適用于 **/hana/data** 16 mb 和 64 mb 的 i/o 大小

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 等 DBMS 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此，您 **必須** 利用 **/hana/data** 和 **/hana/log** 磁片區的 Azure PREMIUM 儲存體、Ultra 磁片或 ANF。 


針對 HANA 選取儲存體設定的一些指導原則可能會如下所示：

- 根據 [SAP 工作負載的 Azure 儲存體類型](./planning-guide-storage.md) 決定儲存體類型，並 [選取磁片類型](../../disks-types.md)
- 當調整大小或決定 VM 時，會考慮整體 VM 的 i/o 輸送量和 IOPS 限制。 整體 VM 儲存體輸送量記載于一文 [記憶體優化的虛擬機器大小](../../sizes-memory.md)
- 當您決定儲存設定時，請嘗試使用您的 **/hana/data** 磁片區設定來維持低於 VM 的整體輸送量。 寫入儲存點、SAP Hana 可能會主動發出 i/o。 當您寫入儲存點時，很容易就能推送至 **/hana/data** 磁片區的輸送量限制。 如果建立 **/hana/data** (磁片區的磁片) 的輸送量高於您 VM 所允許的輸送量，您可能會遇到由儲存點寫入所使用的輸送量會干擾重做記錄寫入的輸送量需求的情況。 可能會影響應用程式輸送量的情況
- 如果您使用 Azure premium 儲存體，最便宜的設定是使用邏輯磁片區管理員來建立等量集來建立 **/hana/data** 和 **/hana/log** 磁片區

> [!IMPORTANT]
> 儲存體設定的建議就是開始使用的指示。 執行工作負載並分析儲存體使用模式時，您可能會發現您未利用提供的所有儲存體頻寬或 IOPS。 您可以考慮在存放裝置上縮減。 相反地，您的工作負載可能需要比使用這些設定所建議更多的儲存體輸送量。 因此，您可能需要部署更多容量、IOPS 或輸送量。 在需要儲存體容量之間的張力領域中，需要儲存體延遲、儲存體輸送量和需要的儲存體輸送量，以及最便宜的設定，Azure 會提供足夠的不同儲存體類型，具有不同的功能和不同的價位，以找出並調整為您和您的 HANA 工作負載的正確危害。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 Linux 廠商和 SAP 的一般建議是重新設定磁碟區的 I/O 排程器模式，從 **mq-deadline** 或 **kyber** 模式設定為 **noop** (非 multiqueue) 或 **none** (multiqueue) 模式。 詳細資料記載於 [SAP 附註編號 #1984787](https://launchpad.support.sap.com/#/notes/1984787)。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>適用于 Azure M 系列虛擬機器的 premium 儲存體和 Azure 寫入加速器解決方案
Azure 寫入加速器是專用於 Azure M 系列 VM 的功能。 如同名稱所示，功能的目的是要改善對 Azure premium 儲存體進行寫入的 i/o 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 **/hana/log** 磁碟區。 因此， **/hana/data** 和 **/hana/log** 是不同的磁碟區，而 Azure 寫入加速器僅支援 **/hana/log** 磁碟區。 

> [!IMPORTANT]
> 使用 Azure premium 儲存體時， **/hana/log**磁片區的 azure[寫入加速器](../../how-to-enable-write-accelerator.md)使用方式是必要的。 寫入加速器僅適用于 premium 儲存體和 M 系列和 Mv2-Series Vm。 寫入加速器無法與其他 Azure VM 系列（例如 Esv3 或 Edsv4）搭配使用。

下列 Azure premium 磁片的快取建議假設 SAP Hana 的 i/o 特性，如下所示：

- 幾乎沒有任何針對 HANA 資料檔案的讀取工作負載。 例外是在資料載入 HANA 時，HANA 執行個體重新啟動之後的大型 I/O。 針對資料檔案讀取較大 I/O 的另一種情況可能是 HANA 資料庫備份。 因此，讀取快取大多不合理，因為在大部分的情況下，必須完整讀取所有資料檔案磁碟區。 
- 當 HANA 儲存點和 HANA 損毀復原時，對資料檔案的寫入會發生高載。 寫入儲存點是非同步的，且不會造成任何使用者交易延遲。 因為系統必須再次快速回應，所以在損毀復原期間的資料寫入效能極為重要。 不過，損毀復原應該不是例外狀況
- 幾乎不會從 HANA 重做檔案進行任何讀取。 例外是在執行交易記錄備份、損毀復原，或 HANA 執行個體重新啟動階段的大型 I/O。  
- 對於 SAP HANA 重做記錄檔的主要負載是寫入。 根據工作負載的性質，您的 I/O 可以小到 4 KB，而在其他情況下，I/O 大小也可以是 1 MB 或更大。 對 SAP HANA 重做記錄的寫入延遲效能極為重要。
- 所有寫入都必須以可靠的方式保存在磁碟上

**建議：由於 SAP Hana 觀察到的 i/o 模式，因此使用 Azure premium 儲存體的不同磁片區快取應設定如下：**

- **/hana/data** -無快取或讀取快取
- **/hana/log** -無快取-M 的例外狀況，以及應啟用 Azure 寫入加速器 Mv2-Series vm 
- **/hana/shared**：讀取快取
- **OS 磁片** -請勿變更 AZURE 在 VM 建立時所設定的預設快取


如果您使用 LVM 或 mdadm 跨數個 Azure premium 磁片建立等量集，您需要定義 stripe 大小。 這些大小在 **/hana/data** 和 **/hana/log**之間不同。 **建議：如使用等量大小，建議使用：**

- 針對 **/hana/data** 使用 256 KB
- 適用于 **/hana/log**的 64 KB

> [!NOTE]
> **/Hana/data**的等量大小已從先前的建議變更，這些建議會根據客戶體驗，以較新的 Linux 版本來呼叫 64 kb 或 128 KB 到 256 KB。 256 KB 的大小會提供稍微更好的效能。 我們也已將 **/hana/log** 的等量大小建議從 32 kb 變更為 64 kb，以取得足夠的輸送量，並提供更大的 i/o 大小。

> [!NOTE]
> 您不需要使用 RAID 磁片區設定任何冗余層級，因為 Azure 區塊儲存體會保留三個 VHD 映射。 使用具有 Azure premium 磁片的等量集，純粹是設定可提供足夠 IOPS 和/或 i/o 輸送量的磁片區。

將多個 Azure Vhd 累積在等量集合下，會從 IOPS 和儲存體輸送量端累加。 因此，如果您將等量集合放在超過 3 x 個 P30 的 Azure premium 儲存體磁片上，它應該會提供您 IOPS 的三倍，以及單一 Azure premium 儲存體 P30 磁片的儲存體輸送量三倍。

> [!IMPORTANT]
> 如果您使用 LVM 或 mdadm 做為磁片區管理員來建立跨多個 Azure premium 磁片的等量集，則必須將三個 SAP Hana 檔案系統/data sources、/log 和/shared 置於預設或根磁片區群組中。 強烈建議您遵循 Linux 廠商的指導方針，這通常會引導您針對 /data、/log 和/shared 建立個別的磁碟區群組。


### <a name="azure-burst-functionality-for-premium-storage"></a>適用于 premium 儲存體的 Azure 高載功能
針對較小或等於 512 GiB 容量的 Azure premium 儲存體磁片，會提供高載功能。 磁片高載運作方式的確切方式，會在 [磁片](../../linux/disk-bursting.md)高載一文中說明。 當您閱讀這篇文章時，您會瞭解當 i/o 工作負載低於磁片的名義 IOPS 和輸送量時，所產生的 IOPS 和輸送量概念 (如需名義輸送量的詳細資訊，請參閱 [受控磁片定價](https://azure.microsoft.com/pricing/details/managed-disks/)) 。 您將會在目前的使用量和磁片的名義值之間，累積 IOPS 和輸送量之間的差異。 高載限制為最多30分鐘。

這種高載功能可以在其中規劃的理想情況，可能會是包含不同 DBMS 之資料檔的磁片區或磁片。 對這些磁片區預期的 i/o 工作負載，尤其是小型到中型的系統應該如下所示：

- 低至適中的讀取工作負載，因為資料在理想情況下會快取在記憶體中，或類似于 HANA 的情況應該完全在記憶體中
- 定期發出的資料庫檢查點或儲存點所觸發的寫入高載
- 在未透過儲存體快照集執行備份的情況下，于連續資料流程中讀取的備份工作負載
- 針對 SAP Hana，請在實例重新開機後將資料載入記憶體中

尤其是在您的工作負載只處理每秒數百筆交易的較小型 DBMS 系統上，這類高載功能也適用于儲存交易或重做記錄的磁片或磁片區。 針對這樣的磁片或磁片區，預期的工作負載看起來像這樣：

- 定期寫入至相依于工作負載的磁片以及工作負載的本質，因為應用程式發出的每個認可都可能觸發 i/o 作業。
- 針對作業工作的案例，以較高的輸送量工作負載，例如建立或重建索引
- 執行交易記錄或重做記錄備份時讀取高載


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>以 Azure premium 儲存體為基礎的生產環境建議儲存體解決方案

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是專用於 **/hana/log** 磁碟區的 Azure Write Accelerator。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 **/hana/log** 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 在涉及 Azure premium 儲存體的案例中，我們會在設定中執行高載功能。 當您使用任何圖形或表單的儲存體測試控管時，請記住 [Azure premium 磁片](../../linux/disk-bursting.md) 高載的運作方式。 執行透過 SAP HWCCT 或 HCMT 工具傳遞的存放裝置測試時，不會預期所有測試都會通過準則，因為有些測試將會超過您可累積的高載點數。 尤其是當所有測試都在不中斷的情況下連續執行時。


> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

**建議：適用于生產環境的 Azure premium 儲存體建議設定如下所示：**

SAP **/hana/data** volume 的設定：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | 最大高載輸送量 | IOPS | 高載 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M64ls | 512 GB | 1,000 MBps | 4 x P10 |  680 MBps | 2,000 | 14,000 |
| M64s | 1,000 GiB | 1,000 MBps | 4 x P15 | 680 MBps | 4400 | 14,000 |
| M64ms | 1,750 GiB | 1,000 MBps | 4 x P20 | 680 MBps | 9200 | 14,000 |  
| M128s | 2,000 GiB | 2000 MBps | 4 x P20 | 680 MBps | 9200| 14,000 | 
| M128ms | 3,800 GiB | 2000 MBps | 4 x P30 | 800 MBps (布建)  | 20,000 | 無高載 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 4 x P30 | 800 MBps (布建)  | 20,000| 無高載 | 
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 4 x P40 | 1000 MBps (布建)  | 25,000 | 無高載 |
| M416s_v2 | 5,700 GiB | 2000 MBps | 4 x P40 | 1000 MBps (布建)  | 25,000 | 無高載 |
| M416ms_v2 | 11,400 GiB | 2000 MBps | 4 x P50 | 2000 MBps (布建)  | 25,000 | 無高載 |


適用于 **/hana/log** 磁片區。 設定看起來會像這樣：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | **/hana/log** 磁片區 | 最大高載輸送量 | IOPS | 高載 IOPS |
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


針對其他磁片區，設定看起來會像這樣：

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


請針對不同的建議磁碟區，檢查儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要較高的磁片區來進行 **/hana/data** 和 **/hana/log**，您需要增加 Azure premium 儲存體 vhd 的數目。 使用比列出數目更多的 VHD 來調整磁碟區大小，會增加在 Azure 虛擬機器類型限制內的 IOPS 和 I/O 輸送量。

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 因此，至少需要將構成 **/hana/log** 磁片區的 Azure premium storage 磁片部署為受控磁片。 如需 Azure 寫入加速器的詳細指示和限制，請參閱 [寫入加速器](../../how-to-enable-write-accelerator.md)的文章。

針對 Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) 系列和 [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)的 HANA 認證 VM，您需要 ANF **/hana/data** 和 **/hana/log** 的磁片區。 或者，您必須使用 Azure Ultra 磁片儲存體，而不是僅針對 **/hana/log** 磁片區使用 azure premium 儲存體。 因此，Azure premium 儲存體上 **/hana/data** 磁片區的設定看起來可能像這樣：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | 最大高載輸送量 | IOPS | 高載 IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  510 MBps | 1,500 | 10500|
| E48ds_v4 | 384 GiB | 1152 MBps | 3 x P15 |  510 MBps | 3300  | 10500 | 
| E64ds_v4 | 504 GiB | 1,200 MBps | 3 x P15 |  510 MBps | 3300 | 10500 | 
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 3 x P15 |  510 MBps | 3300 | 10500 | 

針對其他磁片區（包括 Ultra 磁片上的 **/hana/log** ），設定看起來會像這樣：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/log 磁碟區 | /hana/log I/O 輸送量 | /hana/log IOPS | HANA/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1152 MBps | 192 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1,200 MBps | 256 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1,200 MBps | 220 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>適用於 SAP Hana 的 Azure Ultra 磁碟儲存體設定
另一個 Azure 儲存體類型稱為 [Azure Ultra 磁片](../../disks-types.md#ultra-disk)。 迄今所提供的 Azure 儲存體與 Ultra 磁碟之間的顯著差異，在於磁碟功能不會再繫結到磁碟大小。 身為客戶的您可以為 Ultra 磁碟定義這些功能：

- 磁碟的大小，範圍從 4 GiB 到 65,536 GiB
- IOPS 的範圍從 100 IOPS 到 160K IOPS (最大值也取決於 VM 類型)
- 從 300 MB/秒到 2,000 MB/秒的儲存體輸送量

Ultra 磁碟可讓您定義滿足大小、IOPS 和磁碟輸送量範圍的單一磁碟。 與其在 Azure premium 儲存體上使用像是 LVM 或 MDADM 的邏輯磁片區管理員，來建造滿足 IOPS 和儲存體輸送量需求的磁片區。 您可以在 Ultra 磁片與 premium 儲存體之間執行設定混合。 因此，您可以將 Ultra 磁片的使用量限制為效能關鍵 **/hana/data** 和 **/hana/log** 磁片區，並使用 Azure premium 儲存體來涵蓋其他磁片區

相較于 premium 儲存體，Ultra 磁片的其他優點可能是更好的讀取延遲。 較快的讀取延遲在減少 HANA 啟動時間和後續的記憶體資料載入上都會帶來好處。 您也可以在 HANA 寫入儲存點時，感受到 Ultra 磁碟儲存體的優點。 

> [!NOTE]
> Ultra 磁碟尚未出現在所有 Azure 區域中，也尚未支援下列所有 VM 類型。 如需 Ultra 磁碟可用於何處和支援哪些 VM 系列的詳細資訊，請參閱下列文章：[在 Azure 中可使用哪些磁碟類型？](../../disks-types.md#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>生產環境建議的儲存體解決方案和純 Ultra 磁碟設定
在此設定中，您會將 **/hana/data** 和 **/hana/log** 磁片區分開。 建議的值衍生自此 KPI，而 SAP 必須依照 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建議，為 SAP Hana 和儲存體設定認證 VM 類型。

建議通常會超過本文稍早所述的 SAP 最低需求。 所列建議是 SAP 建議的大小與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

> [!NOTE]
> Azure Ultra 磁片最少為磁片的每 Gb 容量強制執行 2 IOPS


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
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 750 MBps |9,600 | 512 GB | 250 MBps  | 2,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 750 MBps | 14400 | 512 GB | 250 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,000 MBps | 14400 | 512 GB | 400 MBps  | 4,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 28800 | 512 GB | 400 MBps  | 4,000 |   

**列出的值應視為一個起點，而且需要針對真正的需求進行評估。** Azure Ultra 磁碟的優點是可以調整 IOPS 和輸送量的值，而不需要關閉 VM 或停止套用至系統的工作負載。   

> [!NOTE]
> 到目前為止，您還無法使用具有 Ultra 磁碟儲存體的儲存體快照集。 這會讓您無法使用 Azure 備份服務的 VM 快照集


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 上的 NFS v4.1 磁碟區
如需 ANF for HANA 的詳細資訊，請閱讀 [Azure NetApp Files 上的檔 NFS 4.1 磁片區以取得 SAP Hana](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>使用 Azure premium 儲存體的成本意識解決方案
到目前為止，本檔中所述的 Azure premium 儲存體解決方案適用于 [Azure M 系列虛擬機器的高階儲存體和 azure 寫入加速器](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) 的各節解決方案，是為了 SAP Hana 生產環境的支援案例。 生產環境支援設定的其中一個特性是將 SAP Hana 資料的磁片區區隔，並將重新執行登入兩個不同的磁片區。 這類分隔的原因是磁片區上的工作負載特性不同。 而且，在建議的生產環境設定中，可能需要不同類型的快取或甚至不同類型的 Azure 區塊儲存體。 使用 Azure 區塊儲存體目標的生產環境支援設定，也會符合 [Azure 虛擬機器的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 。  針對非生產案例，針對生產系統所採取的一些考慮可能不適用於較低的非生產系統。 如此一來，就可以合併 HANA 資料和記錄磁片區。 但最終有一些原因，例如最後不符合生產系統所需的特定輸送量或延遲 Kpi。 減少這類環境成本的另一個層面就是使用 [Azure 標準 SSD 儲存體](./planning-guide-storage.md#azure-standard-ssd-storage)。 但可 [讓 Azure 虛擬機器的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)失效的選項。 

成本較低的替代方案可能如下所示：


| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| E16v3 | 128 GB | 384 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 未通過 HANA 認證的 VM 類型 <br /> 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 5000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 未通過 HANA 認證的 VM 類型 <br /> 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 5000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| E64v3 | 432 GiB | 1,200 MB/秒 | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/秒 |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不會達到低於1毫秒的儲存體延遲<sup>1</sup> |
| M64ls | 512 GB | 1,000 MB/秒 | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M64s | 1,000 GiB | 1,000 MB/秒 | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M128s | 2,000 GiB | 2,000 MB/秒 |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 20000<sup>2</sup> |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 20000<sup>2</sup> |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 10000<sup>2</sup> |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 20000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 針對合併的資料和記錄磁片區使用寫入加速器，會將 IOPS 速率限制為 20000<sup>2</sup> |


<sup>1</sup> [Azure 寫入加速器](../../how-to-enable-write-accelerator.md) 無法搭配 Ev4 和 Ev4 VM 系列使用。 由於使用 Azure premium storage，i/o 延遲不會小於1毫秒

<sup>2</sup> VM 系列支援 [Azure 寫入加速器](../../how-to-enable-write-accelerator.md)，但寫入加速器的 IOPS 限制可能會限制磁片設定 iops 功能

如果結合 SAP Hana 的資料和記錄磁片區，則建立等量磁片區的磁片不應該啟用讀取快取或讀取/寫入快取。

列出的 VM 類型未經過 SAP 認證，如未列在中，因此稱為 [SAP Hana 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 客戶的意見反應是針對某些非生產工作成功使用了那些未列出的 VM 類型。


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱

- [Azure 虛擬機器的 SAP Hana 高可用性指南](./sap-hana-availability-overview.md)。