---
title: SAP HANA Azure 虛擬機器存儲配置 |微軟文檔
description: 已部署 SAP HANA 的 VM 的存儲建議。
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
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080303"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虛擬機器儲存體設定

Azure 提供不同類型的存儲，適用于運行 SAP HANA 的 Azure VM。 **SAP HANA 認證的 Azure 存儲類型**，可考慮用於 SAP HANA 部署清單，如下所示： 

- Azure 高級 SSD  
- [Ultra 磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

要瞭解這些磁片類型，請參閱文章["選擇磁片類型](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)"

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 在整體情況允許的情況下，請利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。 

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 與選擇的 Azure 存儲類型無關，SAP 需要支援用於該存儲的檔案系統，用於特定的作業系統和 DBMS。 [SAP 支援說明#405827](https://launchpad.support.sap.com/#/notes/405827)列出支援用於不同作業系統和資料庫（包括 SAP HANA）的檔案系統。 這適用于 SAP HANA 可能訪問的所有卷，用於讀取和寫入任何任務。 具體使用 Azure 上的 NFS 進行 SAP HANA，NFS 版本的其他限制適用于本文後面所述 


不同存儲類型的最低 SAP HANA 認證條件為： 

- Azure 高級 SSD - /hana/log 需要使用 Azure[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)進行緩存。 /hana/資料卷可以放置在高級 SSD 上，無需 Azure 寫入加速器或超磁片
- Azure 超級磁片至少適用于 /hana/日誌卷。 /hana/資料卷可以放置在高級 SSD 上，無需 Azure 寫入加速器，也可以用於更快地重新開機超級磁片
- **NFS v4.1**卷在 Azure NetApp 檔之上的 /hana/log**和**/hana/資料。 /hana/shared 的卷可以使用 NFS v3 或 NFS v4.1 協定。 NFS v4.1 協定對於 /hana/資料和/哈納/日誌卷是必需的。

可以組合某些存儲類型。 例如，可以將 /hana/資料放在高級存儲上，/hana/log 可以放在超磁片存儲上，以獲得所需的低延遲。 但是，如果您使用基於 ANF 的 NFS v4.1 捲進行 /hana/資料，則需要使用基於 ANF 的 /hana/log 的另一個 NFS v4.1 卷。 **不支援**將 NFS 用於 ANF 的頂部，用於其中一個卷（如 /hana/資料）和 Azure 高級存儲或超磁片，用於其他卷（如 /hana/log）。

在本地世界中，您很少關心 I/O 子系統及其功能。 原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 在自行構建 Azure 基礎結構時，應瞭解這些 SAP 發佈的一些要求。 SAP 需要的某些最低輸送量特徵導致需要：

- 在 **/hana/log**上啟用讀取/寫入，該日誌的讀取/寫入為 250 MB/秒，尺寸為 1 MB I/O
- 針對 **/hana/data** 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動
- 針對 **/hana/data** 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 等 DBMS 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此，**必須**利用 Azure 高級磁片進行 **/hana/資料和** **/hana/log**卷。 為了達到如 SAP 想要的 **/hana/log** 和 **/hana/data** 最小輸送量，您必須使用 MDADM 或 LVM 在多個 Azure 進階儲存體磁碟上建置 RAID 0。 然後使用 RAID 磁碟區作為 **/hana/data** 和 **/hana/log** 磁碟區。 

**建議：作為 RAID 0 的條帶大小，建議使用：**

- 256 KB 用於 **/哈納/資料**
- 針對 **/hana/log** 使用 32 KB

> [!IMPORTANT]
> /hana/data 的條帶大小從之前要求 64 KB 或 128 KB 的建議更改為 256 KB，具體取決於具有較新 Linux 版本的客戶體驗。 256 KB 的大小提供了略佳的性能

> [!NOTE]
> 您不需要使用 RAID 磁碟區設定任何備援層級，因為 Azure 進階和標準儲存體會保存三個 VHD 的映像。 RAID 磁碟區的使用方式單純是用來設定會提供足夠 I/O 輸送量的磁碟區。

RAID 下的 Azure VHD 數目累計，是從 IOPS 和儲存體輸送量端累計。 因此，如果您將 RAID 0 放在 3 x P30 Azure 進階儲存體磁碟上，它應該會給您單一 Azure 進階儲存體 P30 磁碟的 3 倍 IOPS 和 3 倍儲存體輸送量。

當調整 VM 大小或決定 VM 時，也請注意整體 VM I/O 輸送量。 [記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中說明整體虛擬機器儲存體輸送量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 通過 Linux 供應商和 SAP 的常見建議是重新配置磁片卷的 I/O 調度程式模式，從**cfq**模式到**noop（** 非多佇列）或**無**（多佇列）模式。 詳細資訊在[SAP 說明#1984787](https://launchpad.support.sap.com/#/notes/1984787)中引用。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>適用于 Azure M 系列虛擬機器的高級存儲和 Azure 寫入加速器的解決方案
Azure 寫入加速器是一種功能，僅供 Azure M 系列 VM 使用。 如同名稱所示，這個功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 **/hana/log** 磁碟區。 因此 **，/hana/資料和** **/hana/log**是單獨的卷，Azure 寫入加速器僅支援 **/hana/log**卷。 

> [!IMPORTANT]
> 使用 Azure 高級存儲時，必須使用 Azure[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)或 /hana/log 卷。 寫入加速器僅適用于高級存儲和 M 系列和 Mv2 系列 VM。

下列快取建議假設 SAP HANA 的 I/O 特性如下所示：

- 幾乎沒有任何針對 HANA 資料檔案的讀取工作負載。 例外是在資料載入 HANA 時，HANA 執行個體重新啟動之後的大型 I/O。 針對資料檔案讀取較大 I/O 的另一種情況可能是 HANA 資料庫備份。 因此，讀取快取大多不合理，因為在大部分的情況下，必須完整讀取所有資料檔案磁碟區。
- 當 HANA 儲存點和 HANA 損毀復原時，對資料檔案的寫入會發生高載。 寫入儲存點是非同步的，且不會造成任何使用者交易延遲。 因為系統必須再次快速回應，所以在損毀復原期間的資料寫入效能極為重要。 不過，損毀復原應該不是例外狀況
- 幾乎不會從 HANA 重做檔案進行任何讀取。 例外是在執行交易記錄備份、損毀復原，或 HANA 執行個體重新啟動階段的大型 I/O。  
- 對於 SAP HANA 重做記錄檔的主要負載是寫入。 根據工作負載的性質，您的 I/O 可以小到 4 KB，而在其他情況下，I/O 大小也可以是 1 MB 或更大。 對 SAP HANA 重做記錄的寫入延遲效能極為重要。
- 所有寫入都必須以可靠的方式保存在磁碟上

**建議：由於 SAP HANA 觀察到的 I/O 模式，使用 Azure 高級存儲的不同卷的緩存設置應類似：**

- **/hana/data**：無快取
- **/hana/log** - 無緩存 - M-和 Mv2 系列例外，其中應啟用寫入加速器而不進行讀取緩存。 
- **/哈納/共用**- 讀取緩存

### <a name="production-recommended-storage-solution"></a>生產推薦的存儲解決方案

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是專用於 **/hana/log** 磁碟區的 Azure Write Accelerator。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 **/hana/log** 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

這些建議通常超出了本文前面所述的 SAP 最低要求。 列出的建議是 SAP 建議的大小建議與不同 VM 類型提供的最大存儲輸送量之間的折衷。

**建議：生產方案的建議配置如下所示：**

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | /hana/log | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 吉布 | 1000 MB/秒 | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 吉布 | 1000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 吉布 | 2000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 吉布 | 2000 MB/秒 | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

檢查不同建議的卷的存儲輸送量是否符合要運行的工作負荷。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用比列出的多 VHD 的捲進行大小調整會增加 Azure 虛擬機器類型限制範圍內的 IOPS 和 I/O 輸送量。

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 所以至少必須將組成 **/hana/log** 磁碟區的 Azure 進階儲存體磁碟部署為受控磁碟。

Azure 寫入加速器可以支援之每個虛擬機器的 Azure 進階儲存體 VHD 有其上限。 目前的上限是：

- 16 VHD 用於 M128xx 和 M416xx VM
- 8 VHD 用於 M64xx 和 M208xx VM
- M32xx VM 上限 4 個 VHD

如需如何啟用 Azure Write Accelerator 的詳細指示，可以在[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中找到。

Azure Write Accelerator 的詳細資料和限制可以在相同文件中找到。

**建議：您需要對形成 /hana/log 卷的磁片使用寫入加速器**


### <a name="cost-conscious-azure-storage-configuration"></a>節省成本的 Azure 儲存體組態
下表顯示了客戶也用於在 Azure VM 上託管 SAP HANA 的 VM 類型的配置。 可能有一些 VM 類型可能不符合 SAP HANA 的所有最低存儲條件，或者 SAP 未正式支援 SAP HANA。 但是目前這些虛擬機器似乎可以針對非生產案例正常執行。 **/hana/資料和** **/hana/log**組合為一個卷。 因此，Azure 寫入加速器的使用可能成為 IOPS 方面的限制。

> [!NOTE]
> 對於 SAP 支援的方案，檢查 SAP[在 IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)的 SAP 文檔中是否支援 SAP HANA 的特定 VM 類型。

> [!NOTE]
> 與以前關於成本意識解決方案的建議的一個變化是，從[Azure 標準硬碟磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)轉向性能更好、更可靠的[Azure 標準 SSD 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

這些建議通常超出了本文前面所述的 SAP 最低要求。 列出的建議是 SAP 建議的大小建議與不同 VM 類型提供的最大存儲輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1，200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2，000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GB | 1，000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1，000 吉布 | 1，000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1，750 吉布 | 1，000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2，000 吉布 | 2，000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3，800 吉布 | 2，000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2，850 吉布 | 1，000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5，700 吉布 | 1，000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5，700 吉布 | 2，000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 吉布 | 2，000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


根據 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，包含 3 個 P20 的較小 VM 類型所建議使用的磁碟，在空間建議方面超過磁碟區大小。 不過，已如資料表中顯示進行選擇，為 SAP Hana 提供足夠的磁碟輸送量。 如果您需要更改 **/hana/備份**卷（用於保留表示記憶體容量兩倍的備份的大小），請隨時進行調整。   
檢查不同建議的卷的存儲輸送量是否符合要運行的工作負荷。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用比列出的多 VHD 的捲進行大小調整會增加 Azure 虛擬機器類型限制範圍內的 IOPS 和 I/O 輸送量。 

> [!NOTE]
> 上述組態「不會」受益於 [Azure 虛擬機器的單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因為它是使用 Azure 進階儲存體和 Azure 標準儲存體的混合。 不過，為了最佳化成本而選擇了這個選項。 您必須針對以上列為 Azure 標準儲存體 (Sxx) 的所有磁碟，選擇進階儲存體，讓 VM 組態符合 Azure 單一 VM SLA 的規範。


> [!NOTE]
> 所述磁碟組態建議是以 SAP 對其基礎結構提供者所表示的最低需求為目標。 在實際的客戶部署和工作負載案例中，還是會遇到這些建議仍未能提供足夠功能的情形。 這些情況可能是客戶在 HANA 重新啟動之後需要更快的資料重新載入速度，或者備份組態需要儲存體的更高頻寬。 其他案例包括 **/hana/log**，其中 5000 IOPS 對於特定工作負載來說不足。 因此請將這些建議當作起點，並且根據工作負載的需求來調整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>適用于 SAP HANA 的 Azure 超磁片存儲配置
微軟正在推出一種新的 Azure 存儲類型，稱為[Azure 超磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)。 到目前為止提供的 Azure 存儲與 Ultra 磁片之間的顯著區別是磁片功能不再綁定到磁片大小。 作為客戶，您可以為 Ultra 磁片定義這些功能：

- 磁片大小範圍從 4 GiB 到 65，536 GiB
- IOPS 範圍從 100 IOPS 到 160K IOPS（最大值也取決於 VM 類型）
- 存儲輸送量從 300 MB/秒到 2，000 MB/秒

Ultra 磁片使您能夠定義滿足大小、IOPS 和磁片輸送量範圍的單個磁片。 而不是在 Azure 高級存儲之上使用 LVM 或 MDADM 等邏輯卷管理器來構造滿足 IOPS 和存儲輸送量要求的卷。 您可以在超磁片和高級存儲之間回合組態組合。 因此，您可以將 Ultra 磁片的使用限制為性能關鍵/哈納/資料和 /hana/log 卷，並覆蓋使用 Azure 高級存儲的其他卷

與高級存儲相比，Ultra 磁片的其他優點可能是更好的讀取延遲。 當您希望減少 HANA 啟動時間和資料後續載入到記憶體中時，更快的讀取延遲可能具有優勢。 當 HANA 編寫存儲點時，也可以感受到超磁片存儲的優勢。 由於 /hana/資料的高級存儲磁片通常不寫入加速器緩存，因此與 Ultra 磁片相比，高級存儲上對 /hana/資料的寫入延遲更高。 可以預期，使用 Ultra 磁片的存儲點寫入在 Ultra 磁片上性能更好。

> [!IMPORTANT]
> 超級磁片尚未存在於所有 Azure 區域中，並且還不支援下面列出的所有 VM 類型。 有關 Ultra 磁片可用以及支援哪些 VM 系列的詳細資訊，請查看["Azure 中有哪些磁片類型可用？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>使用純超磁片配置生產推薦的存儲解決方案
在此配置中，您可以單獨保留 /hana/資料和 /hana/log 卷。 建議的值派生自 SAP 必須根據[SAP TDI 存儲白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建議對 SAP HANA 和存儲配置的 VM 類型進行驗證的 KPI。

這些建議通常超出了本文前面所述的 SAP 最低要求。 列出的建議是 SAP 建議的大小建議與不同 VM 類型提供的最大存儲輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /哈納/資料量 | /哈納/資料 I/O 輸送量 | /哈納/資料 IOPS | /哈納/日誌卷 | /哈納/日誌 I/O 輸送量 | /哈納/日誌 IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1，200 MB/s | 600 GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GB | 1，000 MB/s | 600 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1，000 吉布 | 1，000 MB/s |  1，200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1，750 吉布 | 1，000 MB/s | 2，100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2，000 吉布 | 2，000 MB/s |2，400 GB | 1，200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3，800 吉布 | 2，000 MB/s | 4，800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2，850 吉布 | 1，000 MB/s | 3，500 GB | 1，000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5，700 吉布 | 1，000 MB/s | 7，200 GB | 1，000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5，700 吉布 | 2，000 MB/s | 7，200 GB | 1，500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11，400 吉布 | 2，000 MB/s | 14，400 GB | 1，500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

列出的值旨在作為起點，需要根據實際需求進行評估。 Azure Ultra 磁片的優點是可以調整 IOPS 和輸送量的值，而無需關閉 VM 或停止應用於系統的工作負載。   

> [!NOTE]
> 到目前為止，使用 Ultra 磁片存儲的存儲快照不可用。 這將阻止使用 Azure 備份服務使用 VM 快照

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>具有純超磁片配置的注重成本的存儲解決方案
在此配置中，您在同一磁片上的 /hana/資料和 /hana/log 卷。 建議的值派生自 SAP 必須根據[SAP TDI 存儲白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建議對 SAP HANA 和存儲配置的 VM 類型進行驗證的 KPI。 

這些建議通常超出了本文前面所述的 SAP 最低要求。 列出的建議是 SAP 建議的大小建議與不同 VM 類型提供的最大存儲輸送量之間的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /哈納/資料和 /日誌的卷 | /hana/資料和日誌 I/O 輸送量 | /哈納/資料和日誌 IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1，200 MB/s | 1，200 GB | 1，200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/秒 | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/秒 | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GB | 1，000 MB/s | 1，100 GB | 900 MBps | 10,000 | 
| M64s | 1，000 吉布 | 1，000 MB/s |  1，700 GB | 900 MBps | 10,000 | 
| M64ms | 1，750 吉布 | 1，000 MB/s | 2，600 GB | 900 MBps | 10,000 | 
| M128s | 2，000 吉布 | 2，000 MB/s |2，900 GB | 1，800 MBps |12,000 | 
| M128ms | 3，800 吉布 | 2，000 MB/s | 5，300 GB | 1，800 MBps |12,000 |  
| M208s_v2 | 2，850 吉布 | 1，000 MB/s | 4，000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5，700 吉布 | 1，000 MB/s | 7，700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5，700 吉布 | 2，000 MB/s | 7，700 GB | 1，800MBps | 12,000 |  
| M416ms_v2 | 11，400 吉布 | 2，000 MB/s | 15，000 GB | 1，800 MBps | 12,000 |    

列出的值旨在作為起點，需要根據實際需求進行評估。 Azure Ultra 磁片的優點是可以調整 IOPS 和輸送量的值，而無需關閉 VM 或停止應用於系統的工作負載。  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp 檔上的 NFS v4.1 卷
Azure NetApp 檔提供本機 NFS 共用，可用於 /hana/共用、//哈納/資料和 /hana/log 卷。 對 /hana/資料和 /hana/log 卷使用基於 ANF 的 NFS 共用需要使用 v4.1 NFS 協定。 在基於 ANF 時，不支援使用 /hana/資料和 /hana/log 卷的 NFS 協定 v3。 

> [!IMPORTANT]
> 不支援在 Azure NetApp 檔上實現的 NFS v3 協定用於 /hana/資料和 /hana/log。 **not** 從功能角度來看，NFS 4.1 對於 /hana/資料和 /hana/log 卷是必需的。 而對於 /hana/共用卷，可以從功能角度使用 NFS v3 或 NFS v4.1 協定。

### <a name="important-considerations"></a>重要考量︰
在考慮 SAP Netweaver 和 SAP HANA 的 Azure NetApp 檔時，請注意以下重要注意事項：

- 最小容量池為 4 TiB。  
- 最小體積大小為 100 GiB
- Azure NetApp 檔和將裝載 Azure NetApp 檔卷的所有虛擬機器必須位於同一 Azure 虛擬網路或同一區域中的[對等虛擬網路中](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。  
- 所選虛擬網路必須具有委派給 Azure NetApp 檔的子網。
- Azure NetApp 卷的輸送量是卷配額和服務等級的函數，如[Azure NetApp 檔的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 調整 HANA Azure NetApp 卷的量量時，請確保生成的輸送量滿足 HANA 系統要求。  
- Azure NetApp 檔提供[匯出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：您可以控制允許的用戶端、訪問類型（讀取&寫入、僅讀取等）。 
- Azure NetApp 檔功能尚未了解區域。 當前，Azure NetApp 檔功能未部署在 Azure 區域中的所有可用性區域中。 請注意某些 Azure 區域的潛在延遲影響。  
- 將虛擬機器部署在靠近 Azure NetApp 存儲的位置以降低延遲非常重要。 
- 虛擬機器上的<b>sid</b>dm 的使用者 ID 和`sapsys`虛擬機器上的組 ID 必須與 Azure NetApp 檔中的配置匹配。 

> [!IMPORTANT]
> 對於 SAP HANA 工作負載，低延遲至關重要。 與 Microsoft 代表合作，確保虛擬機器和 Azure NetApp 檔卷在接近的地方部署。  

> [!IMPORTANT]
> 如果<b>sid</b>adm 的使用者 ID 與虛擬機器和 Azure `sapsys` NetApp 配置之間的組 ID 不匹配，則安裝在虛擬機器上的 Azure NetApp 卷上的檔許可權將顯示為`nobody`。 在[將新系統輸入](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)Azure NetApp 檔時，請確保為`sapsys`sid <b>adm</b>指定正確的使用者 ID 和 組的 ID。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp 檔上的 HANA 資料庫的尺寸

Azure NetApp 卷的輸送量是卷大小和服務等級的函數，如[Azure NetApp 檔的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

在 Azure 中為 SAP 設計基礎結構時，應瞭解 SAP 的一些最低存儲輸送量要求，這轉化為以下最低輸送量特徵：

- 在 /hana/log 上啟用讀取/寫入，該日誌的讀取/寫入為 250 MB/秒，尺寸為 1 MB I/O  
- 針對 /hana/data 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動  
- 針對 /hana/data 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動  

[Azure NetApp 檔輸送量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)每 1 TiB 的卷配額是：
- 高級存儲層 - 64 MiB/s  
- 超存儲層 - 128 MiB/s  

> [!IMPORTANT]
> 與您在單個 NFS 卷上部署的容量無關，輸送量預計將在虛擬機器中的消費者利用的 1.2-1.4 GB/sec 頻寬範圍內穩定下來。 這與 ANF 產品提供的基礎結構架構和 NFS 的相關 Linux 會話限制有關。 在文章["Azure NetApp 檔的性能基準測試結果](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks)"中記錄的性能和輸送量編號是針對具有多個用戶端 VM 的一個共用 NFS 捲進行的，因此具有多個會話。 這種情況不同于我們在 SAP 中測量的方案。 我們根據 NFS 卷測量單個 VM 的輸送量。 託管在 ANF 上。

為了滿足 SAP 資料和日誌的最低輸送量要求，並根據 的`/hana/shared`準則，建議的大小如下所示：

| 磁碟區 | 大小<br /> 高級存儲層 | 大小<br /> 超存儲層 | 支援的 NFS 協定 |
| --- | --- | --- |
| /哈納/日誌/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| HANA/shared | 每個 4 個輔助節點的最大最大（512 GB、1xRAM） | 每個 4 個輔助節點的最大最大（512 GB、1xRAM） | v3 或 v4.1 |


> [!NOTE]
> 此處列出的 Azure NetApp 檔案大小調整建議旨在滿足 SAP 對其基礎結構供應商表示的最低要求。 在實際的客戶部署和工作負載方案中，這可能是不夠的。 將這些建議作為起點，並根據特定工作負載的要求進行調整。  

因此，您可以考慮為已列出的超磁片存儲的 ANF 卷部署類似的輸送量。 還要考慮為不同的 VM SKU 列出的大小的大小，就像 Ultra 磁片表中所做的那樣。

> [!TIP]
> 您可以動態調整 Azure NetApp 檔卷的大小，而無需卷`unmount`、停止虛擬機器或停止 SAP HANA。 這允許靈活性，以滿足您的應用程式的預期和不可預見的輸送量需求。

有關如何使用 ANF 中託管的 NFS v4.1 卷使用備用節點部署 SAP HANA 橫向擴展配置的文檔在[SAP HANA 橫向擴展中發佈，在 Azure VM 上使用在 SUSE Linux 企業伺服器上具有 Azure NetApp 檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)。


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱

- [SAP HANA Azure 虛擬機器的高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
