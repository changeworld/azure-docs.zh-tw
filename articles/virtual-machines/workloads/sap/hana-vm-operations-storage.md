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
ms.date: 05/19/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa3096f43952c047620b310412b27c434fc5fb06
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682596"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虛擬機器儲存體設定

Azure 針對執行 SAP Hana 的 Azure VM，提供不同儲存體類型。 **SAP Hana 認證的 Azure 儲存體類型**，可考慮用於 SAP Hana 部署清單，例如： 

- Azure 進階 SSD  
- [Ultra 磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

若要了解這些磁碟類型，請參閱[選取磁碟類型](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)一文

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 在整體情況允許的情況下，請利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。 

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 無論所選擇的 Azure 儲存體類型為何，適用於特定作業系統和 DBMS 的 SAP 必須可支援該存放裝置所使用的檔案系統。 [SAP 支援附註 #405827](https://launchpad.support.sap.com/#/notes/405827) 會列出不同作業系統和資料庫 (包括 SAP Hana) 支援的檔案系統。 這適用於所有 SAP Hana 可能會存取以進行任何工作讀取和寫入的磁碟區。 特別是在適用於 SAP Hana 的 Azure 上使用 NFS，這將會套用其他 NFS 版本限制，如本文稍後所述 


不同儲存類型的最低 SAP Hana 認證條件如下： 

- Azure 進階 SSD - /hana/log 必須使用 Azure [寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)進行快取。 /hana/data 磁碟區可以放在沒有 Azure 寫入加速器的進階 SSD 或 Ultra 磁碟上
- Azure Ultra 磁碟，/hana/log 磁碟區的最低需求。 /hana/data 磁碟區可以放在沒有 Azure 寫入加速器的進階 SSD 上，或放在 Ultra 磁碟以享有更快速的重新啟動時間
- Azure NetApp Files 上方的 **NFS v4.1** 磁碟區，適用於 /hana/log **和** /hana/data。 /hana/shared 的磁碟區可以使用 NFS v3 或 NFS 4.1 通訊協定。 NFS 4.1 通訊協定對於 /hana/data 和 /hana/log 磁碟區而言是必要的。

有些儲存體類型可以合併。 例如，您可以將 /hana/data 放在進階儲存體上，並將 /hana/log 放在 Ultra 磁碟儲存體上，以達到所需的低延遲。 不過，如果您針對 /hana/data 使用以 ANF 為基礎的 NFS 4.1 磁碟區，則必須針對 /hana/log 使用另一個以 ANF 為基礎的 NFS 4.1 磁碟區。 **不支援**針對其中一個磁碟區 (例如 /hana/data) 使用 ANF 上方的 NFS，而對另一個磁碟區 (例如 /hana/log) 使用 Azure 進階儲存體或 Ultra 磁碟。

在內部部署環境中，您很少需要在意 I/O 子系統及其功能。 原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 當您自行建置 Azure 基礎結構時，您應該對這些 SAP 發出的需求有些了解。 SAP 所需的一些最低輸送量特性會產生以下需求：

- 在 **/hana/log** 上啟用 250MB/秒及 1 MB I/O 大小的讀取/寫入
- 針對 **/hana/data** 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動
- 針對 **/hana/data** 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 等 DBMS 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此，針對 **/hana/data** 和 **/hana/log** 磁碟區**必須**使用 Azure 進階磁碟。 為了達到如 SAP 想要的 **/hana/log** 和 **/hana/data** 最小輸送量，您必須使用 MDADM 或 LVM 在多個 Azure 進階儲存體磁碟上建置 RAID 0。 然後使用 RAID 磁碟區作為 **/hana/data** 和 **/hana/log** 磁碟區。 

> [!IMPORTANT]
>這三個 SAP Hana 檔案系統 /data、/log 和 /shared 不得放在預設或根磁碟區群組中。  強烈建議您遵循 Linux 廠商的指導方針，這通常會引導您針對 /data、/log 和/shared 建立個別的磁碟區群組。

**建議：對於 RAID 0 的等量磁碟大小，建議使用：**

- 針對 **/hana/data** 使用 256 KB
- 針對 **/hana/log** 使用 32 KB

> [!IMPORTANT]
> /Hana/data 的等量大小 (stripe size) 已在先前的建議中變更，根據最近 Linux 版本的客戶經驗，將 64 KB 或 128 KB 變更為 256 KB。 256 KB 的大小會提供稍微較好的效能

> [!NOTE]
> 您不需要使用 RAID 磁碟區設定任何備援層級，因為 Azure 進階和標準儲存體會保存三個 VHD 的映像。 RAID 磁碟區的使用方式單純是用來設定會提供足夠 I/O 輸送量的磁碟區。

RAID 下的 Azure VHD 數目累計，是從 IOPS 和儲存體輸送量端累計。 因此，如果您將 RAID 0 放在 3 x P30 Azure 進階儲存體磁碟上，它應該會給您單一 Azure 進階儲存體 P30 磁碟的 3 倍 IOPS 和 3 倍儲存體輸送量。

當調整 VM 大小或決定 VM 時，也請注意整體 VM I/O 輸送量。 [記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中說明整體虛擬機器儲存體輸送量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 Linux 廠商和 SAP 的一般建議是重新設定磁碟區的 I/O 排程器模式，從 **mq-deadline** 或 **kyber** 模式設定為 **noop** (非 multiqueue) 或 **none** (multiqueue) 模式。 詳細資料記載於 [SAP 附註編號 #1984787](https://launchpad.support.sap.com/#/notes/1984787)。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>適用於 Azure M 系列虛擬機器的進階儲存體和 Azure 寫入加速器解決方案
Azure 寫入加速器是專用於 Azure M 系列 VM 的功能。 如同名稱所示，這個功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 **/hana/log** 磁碟區。 因此， **/hana/data** 和 **/hana/log** 是不同的磁碟區，而 Azure 寫入加速器僅支援 **/hana/log** 磁碟區。 

> [!IMPORTANT]
> 使用 Azure 進階儲存體時，必須使用 Azure [寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 或 /hana/log 磁碟區。 寫入加速器僅適用於進階儲存體和 M 系列與 Mv2 系列的 VM。

下列快取建議假設 SAP HANA 的 I/O 特性如下所示：

- 幾乎沒有任何針對 HANA 資料檔案的讀取工作負載。 例外是在資料載入 HANA 時，HANA 執行個體重新啟動之後的大型 I/O。 針對資料檔案讀取較大 I/O 的另一種情況可能是 HANA 資料庫備份。 因此，讀取快取大多不合理，因為在大部分的情況下，必須完整讀取所有資料檔案磁碟區。
- 當 HANA 儲存點和 HANA 損毀復原時，對資料檔案的寫入會發生高載。 寫入儲存點是非同步的，且不會造成任何使用者交易延遲。 因為系統必須再次快速回應，所以在損毀復原期間的資料寫入效能極為重要。 不過，損毀復原應該不是例外狀況
- 幾乎不會從 HANA 重做檔案進行任何讀取。 例外是在執行交易記錄備份、損毀復原，或 HANA 執行個體重新啟動階段的大型 I/O。  
- 對於 SAP HANA 重做記錄檔的主要負載是寫入。 根據工作負載的性質，您的 I/O 可以小到 4 KB，而在其他情況下，I/O 大小也可以是 1 MB 或更大。 對 SAP HANA 重做記錄的寫入延遲效能極為重要。
- 所有寫入都必須以可靠的方式保存在磁碟上

**建議：由 SAP HANA 觀察到的這些 I/O 模式結果，使用 Azure 進階儲存體對不同磁碟區的快取設定應該設定如下：**

- **/hana/data**：無快取
- **/hana/log** - 無快取，但 M 和 Mv2 系列除外，其中寫入加速器可在不需要讀取快取的情況下啟用。 
- **/hana/shared**：讀取快取

### <a name="production-recommended-storage-solution"></a>生產環境建議的儲存體解決方案

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是專用於 **/hana/log** 磁碟區的 Azure Write Accelerator。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 **/hana/log** 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

建議通常會超過本文稍早所述的 SAP 最低需求。 所列建議是 SAP 建議的大小與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

**建議：生產案例的建議設定應如下所示：**

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

請針對不同的建議磁碟區，檢查儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用比列出數目更多的 VHD 來調整磁碟區大小，會增加在 Azure 虛擬機器類型限制內的 IOPS 和 I/O 輸送量。

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 所以至少必須將組成 **/hana/log** 磁碟區的 Azure 進階儲存體磁碟部署為受控磁碟。

Azure 寫入加速器可以支援之每個虛擬機器的 Azure 進階儲存體 VHD 有其上限。 目前的上限是：

- M128xx 和 M416xx VM 的上限是 16 個 VHD
- M64xx 和 M208xx VM 的上限是 8 個 VHD
- M32xx VM 上限 4 個 VHD

如需如何啟用 Azure Write Accelerator 的詳細指示，可以在[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中找到。

Azure Write Accelerator 的詳細資料和限制可以在相同文件中找到。

**建議：您需要針對形成 /hana/log 磁碟區的磁碟使用寫入加速器**


### <a name="cost-conscious-azure-storage-configuration"></a>節省成本的 Azure 儲存體組態
下表顯示客戶也用來在 Azure 虛擬機器上裝載 SAP Hana 的虛擬機器類型組態。 可能有部分虛擬機器類型無法符合 SAP Hana 的所有最小儲存體準則，或並未由 SAP 透過 SAP Hana 正式支援。 但是目前這些虛擬機器似乎可以針對非生產案例正常執行。 **/hana/data** 和 **/hana/log** 會合併為一個磁碟區。 因此，Azure 寫入加速器的使用量可能會成為 IOPS 方面的限制。

> [!NOTE]
> 針對 SAP 支援案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP Hana 的特定虛擬機器類型。

> [!NOTE]
> 針對符合成本效益的解決方案，先前建議的變更是從 [Azure 標準 HDD 磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)移到效能更好且更可靠的 [Azure 標準 SSD 磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

建議通常會超過本文稍早所述的 SAP 最低需求。 所列建議是 SAP 建議的大小與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1,200 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2,000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GB | 1,000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1,000 GiB | 1,000 MB/秒 | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


根據 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，包含 3 個 P20 的較小 VM 類型所建議使用的磁碟，在空間建議方面超過磁碟區大小。 不過，已如資料表中顯示進行選擇，為 SAP Hana 提供足夠的磁碟輸送量。 如果您需要對 **/hana/backup** 磁碟區 (已調整大小來保持備份為記憶體磁碟區的兩倍) 進行變更，您可以自由調整。   
請針對不同的建議磁碟區，檢查儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用比列出數目更多的 VHD 來調整磁碟區大小，會增加在 Azure 虛擬機器類型限制內的 IOPS 和 I/O 輸送量。 

> [!NOTE]
> 上述組態「不會」受益於 [Azure 虛擬機器的單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因為它是使用 Azure 進階儲存體和 Azure 標準儲存體的混合。 不過，為了最佳化成本而選擇了這個選項。 您必須針對以上列為 Azure 標準儲存體 (Sxx) 的所有磁碟，選擇進階儲存體，讓 VM 組態符合 Azure 單一 VM SLA 的規範。


> [!NOTE]
> 所述磁碟組態建議是以 SAP 對其基礎結構提供者所表示的最低需求為目標。 在實際的客戶部署和工作負載案例中，還是會遇到這些建議仍未能提供足夠功能的情形。 這些情況可能是客戶在 HANA 重新啟動之後需要更快的資料重新載入速度，或者備份組態需要儲存體的更高頻寬。 其他案例包括 **/hana/log**，其中 5000 IOPS 對於特定工作負載來說不足。 因此請將這些建議當作起點，並且根據工作負載的需求來調整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>適用於 SAP Hana 的 Azure Ultra 磁碟儲存體設定
Microsoft 正在推出稱為 [Azure Ultra 磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)的新 Azure 儲存體類型。 迄今所提供的 Azure 儲存體與 Ultra 磁碟之間的顯著差異，在於磁碟功能不會再繫結到磁碟大小。 身為客戶的您可以為 Ultra 磁碟定義這些功能：

- 磁碟的大小，範圍從 4 GiB 到 65,536 GiB
- IOPS 的範圍從 100 IOPS 到 160K IOPS (最大值也取決於 VM 類型)
- 從 300 MB/秒到 2,000 MB/秒的儲存體輸送量

Ultra 磁碟可讓您定義滿足大小、IOPS 和磁碟輸送量範圍的單一磁碟。 而不是在 Azure 進階儲存體頂端使用邏輯磁碟區管理員 (例如 LVM 或 MDADM) 來建立可滿足 IOPS 和儲存體輸送量需求的磁碟區。 您可以在 Ultra 磁碟與進階儲存體之間執行設定混合。 因此，您可以將 Ultra 磁碟的使用量限制在與效能密切相關的 /hana/data 和 /hana/log 磁碟區上，並將 Azure 進階儲存體用於其他磁碟區

相較於進階儲存體，Ultra 磁碟的其他優點可能是較優秀的讀取延遲。 較快的讀取延遲在減少 HANA 啟動時間和後續的記憶體資料載入上都會帶來好處。 您也可以在 HANA 寫入儲存點時，感受到 Ultra 磁碟儲存體的優點。 因為用於 /hana/data 的進階儲存體磁碟通常不會以寫入加速器快取，所以與 Ultra 磁碟相比，進階儲存體上 /hana/data 的寫入延遲會比較高。 可預期的是，使用 Ultra 磁碟寫入的儲存點在 Ultra 磁碟上執行的效能會比較好。

> [!IMPORTANT]
> Ultra 磁碟尚未出現在所有 Azure 區域中，也尚未支援下列所有 VM 類型。 如需 Ultra 磁碟可用於何處和支援哪些 VM 系列的詳細資訊，請參閱下列文章：[在 Azure 中可使用哪些磁碟類型？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>生產環境建議的儲存體解決方案和純 Ultra 磁碟設定
在此設定中，您會分別保留 /hana/data 和 /hana/log 磁碟區。 建議的值衍生自此 KPI，而 SAP 必須依照 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建議，為 SAP Hana 和儲存體設定認證 VM 類型。

建議通常會超過本文稍早所述的 SAP 最低需求。 所列建議是 SAP 建議的大小與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 磁碟區 | /hana/data I/O 輸送量 | /hana/data IOPS | /hana/log 磁碟區 | /hana/log I/O 輸送量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 600 GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GB | 1,000 MB/秒 | 600 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2,000 GiB | 2,000 MB/秒 |2,400 GB | 1,200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

列出的值應視為一個起點，而且需要針對真正的需求進行評估。 Azure Ultra 磁碟的優點是可以調整 IOPS 和輸送量的值，而不需要關閉 VM 或停止套用至系統的工作負載。   

> [!NOTE]
> 到目前為止，您還無法使用具有 Ultra 磁碟儲存體的儲存體快照集。 這會讓您無法使用 Azure 備份服務的 VM 快照集

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>節省成本的儲存體方案和純 Ultra 磁碟設定
在此設定中，您會將 /hana/data 和 /hana/log 磁碟區放在相同磁碟上。 建議的值衍生自此 KPI，而 SAP 必須依照 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建議，為 SAP Hana 和儲存體設定認證 VM 類型。 

建議通常會超過本文稍早所述的 SAP 最低需求。 所列建議是 SAP 建議的大小與不同 VM 類型提供的最大儲存體輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /Hana/data 和 /log 的磁碟區 | /hana/data 和 log I/O 輸送量 | /hana/data 和 log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 1,200 GB | 1,200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/秒 | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/秒 | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GB | 1,000 MB/秒 | 1,100 GB | 900 MBps | 10,000 | 
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,700 GB | 900 MBps | 10,000 | 
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,600 GB | 900 MBps | 10,000 | 
| M128s | 2,000 GiB | 2,000 MB/秒 |2,900 GB | 1,800 MBps |12,000 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5,300 GB | 1,800 MBps |12,000 |  
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4,000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,700 GB | 1,800MBps | 12,000 |  
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 15,000 GB | 1,800 MBps | 12,000 |    

列出的值應視為一個起點，而且需要針對真正的需求進行評估。 Azure Ultra 磁碟的優點是可以調整 IOPS 和輸送量的值，而不需要關閉 VM 或停止套用至系統的工作負載。  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 上的 NFS v4.1 磁碟區
Azure NetApp Files 提供可用於 /hana/shared、/hana/data 和 /hana/log 磁碟區的原生 NFS 共用。 針對 /hana/data 和 /hana/log 磁碟區使用以 ANF 為基礎的 NFS 共用時，需要使用 4.1 NFS 通訊協定。 在以 ANF 為基礎來進行共用時，不支援在 /hana/data 和 /hana/log 磁碟區的用法上使用 NFS 通訊協定 v3。 

> [!IMPORTANT]
> 在 Azure NetApp Files 上實作的 NFS v3 通訊協定**不**支援用於 /hana/data 和 /hana/log。 從功能的觀點來看，/hana/data 和 /hana/log 磁碟區必須使用 NFS 4.1。 但從功能的觀點來看，/hana/shared 磁碟區則可以使用 NFS v3 或 NFS 4.1 通訊協定。

### <a name="important-considerations"></a>重要考量︰
考慮到適用於 SAP Netweaver 和 SAP Hana 的 Azure NetApp Files 時，請注意下列重要考量：

- 最小容量集區為 4 TiB。  
- 最小磁碟區大小為 100 GiB
- Azure NetApp Files 和所有虛擬機器 (將裝載 Azure NetApp Files 磁碟區的位置) 必須位於相同的 Azure 虛擬網路，或位於相同區域的[對等互連虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)中。  
- 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網路。
- Azure NetApp 磁碟區的輸送量是磁碟區配額的功能和服務等級，如 [Azure NetApp Files 的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 調整 HANA Azure NetApp 磁碟區的大小時，請確定產生的輸送量符合 HANA 系統需求。  
- Azure NetApp Files 提供[匯出原則](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：您可以控制允許的用戶端、存取類型 (讀取及寫入、唯讀等等)。 
- Azure NetApp Files 功能尚無法感知區域。 Azure NetApp Files 功能目前不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能出現的延遲情形。  
- 請務必將虛擬機器部署在 Azure NetApp 儲存體附近，以降低延遲。 
- <b>sid</b>adm 的使用者識別碼，以及虛擬機器上 `sapsys` 的群組識別碼，都必須符合 Azure NetApp Files 中的設定。 

> [!IMPORTANT]
> 針對 SAP Hana 工作負載，低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁碟區已部署在附近。  

> [!IMPORTANT]
> 如果 <b>sid</b>adm 的使用者識別碼與虛擬機器和 Azure NetApp 設定之間的 `sapsys` 群組識別碼不符，則 Azure NetApp 磁碟區 (裝載於虛擬機器) 上的檔案權限將會顯示為 `nobody`。 [將新系統上線](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)至 Azure NetApp Files 時，請務必為 <b>sid</b>adm 指定正確的使用者識別碼，以及為 `sapsys` 指定群組識別碼。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>針對 Azure NetApp Files 上的 HANA 資料庫進行大小調整

Azure NetApp 磁碟區的輸送量是磁碟區大小的功能和服務等級，如 [Azure NetApp Files 的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

當您在 Azure 中設計 SAP 的基礎結構時，您應該注意 SAP 的一些最低儲存體輸送量需求，這會轉譯成下列項目的最低輸送量特性：

- 在 /hana/log 上啟用 250MB/秒及 1 MB I/O 大小的讀取/寫入  
- 針對 /hana/data 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動  
- 針對 /hana/data 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動  

每 1 TiB 磁碟區配額的 [Azure NetApp Files 輸送量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)如下：
- 進階儲存層 - 64 MiB/秒  
- Ultra 儲存層 - 128 MiB/秒  

> [!IMPORTANT]
> 與您在單一 NFS 磁碟區上部署的容量不同，取用者在虛擬機器中所用的輸送量峰值預期會出現在 1.2-1.4 GB/秒的頻寬範圍內。 這必須搭配 ANF 供應項目的基礎結構和 NFS 周圍相關的 Linux 工作階段限制。 [適用於Azure NetApp Files 的效能基準測試](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux)一文所述的效能和輸送量數目是針對一個具有多個用戶端 VM 的共用 NFS 磁碟區進行，因此有多個工作階段。 這種情況與我們在 SAP 中測量的案例不同。 我們會從單一 VM 中對 NFS 磁碟區測量輸送量。 裝載於 ANF 上。

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

[使用SUSE Linux Enterprise Server 上的 Azure NetApp Files，在 Azure VM 上使用待命節點來擴展 SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 中，已發佈如何使用 ANF 中所裝載的 NFS v4.1 磁碟區，搭配待命節點來部署 SAP Hana 延展設定的方法。


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱

- [Azure 虛擬機器的 SAP Hana 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
