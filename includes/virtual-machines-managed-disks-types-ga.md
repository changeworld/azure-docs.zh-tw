---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386132"
---
## <a name="premium-ssd"></a>進階 SSD

針對輸入/輸出 (IO) 工作負載大的虛擬機器 (VM)，Azure 進階 SSD 可提供高效能和低延遲的磁碟支援。 您可以將現有 VM 磁碟遷移到進階 SSD，以利用進階儲存體磁碟的速度和效能。 進階 SSD 適用於任務關鍵性的生產應用程式。 高級 SSD 只能與高級存儲相容的 VM 系列一起使用。

要瞭解有關 Windows Azure 中單個 VM 類型和大小（包括哪些大小與高級存儲相容）的更多資訊，請參閱[Windows VM 大小](../articles/virtual-machines/windows/sizes.md)。 要瞭解有關 Azure 中針對 Linux 的單個 VM 類型和大小（包括哪些大小與高級存儲相容）的更多資訊，請參閱[Linux VM 大小](../articles/virtual-machines/linux/sizes.md)。 從其中任一文章中，都需要檢查每個單獨的 VM 大小文章，以確定它是否與高級存儲相容。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

當您佈建進階儲存體磁碟時，不同於標準儲存體的是，您可獲得該磁碟的容量、IOPS 和輸送量保證。 例如，如果您建立 P50 磁碟，Azure 會為該磁碟佈建 4,095 GB 儲存體容量、7,500 IOPS 和 250 MB/秒的輸送量。 您的應用程式可以使用全部或部分的容量和效能。 高級 SSD 磁片旨在提供低位數毫秒延遲，目標 IOPS 和輸送量（如上表所述）占 99.9%。

## <a name="bursting"></a>爆破

小於 P30 的高級 SSD 尺寸現在提供磁片爆裂功能，每個磁片的 IOPS 可爆裂高達 3，500 個，頻寬高達 170 Mbps。 爆破是自動化的，並且基於信用系統運行。 當磁片流量低於預配性能目標時，積分會自動累積到突發存儲桶中，當流量超出目標時，當流量超出目標時，則會自動使用積分，最高突發限制。 最大突發限制定義磁片 IOPS &頻寬的上限，即使您有突發積分需要使用。 磁片爆發為 IO 模式的不可預知變化提供了更好的容差。 您可以最好地將其用於作業系統磁片啟動和流量尖峰的應用程式。    

預設情況下，將在適用磁片大小的新部署上啟用磁片爆發支援，無需使用者操作。 對於具有適用大小的現有磁片，可以使用兩個選項之一啟用突發功能：分離和重新連接磁片或停止並重新啟動附加的 VM。 當磁片連接到虛擬機器時，所有突發適用的磁片大小都將從完全突發信用存儲桶開始，該虛擬機器在 30 分鐘的峰值突發限制下支援最長持續時間。 要瞭解有關突發功能在 Azure 磁片上的工作方式，請參閱[高級 SSD 爆破](../articles/virtual-machines/linux/disk-bursting.md)。 

### <a name="transactions"></a>交易

對於高級 SSD，每個 I/O 操作小於或等於 256 KiB 的輸送量被視為單個 I/O 操作。 大於 256 KiB 輸送量的 I/O 操作被視為尺寸為 256 KiB 的多個 I/O。

## <a name="standard-ssd"></a>標準 SSD

Azure 標準 SSD 是符合成本效益的儲存體選項，已針對在較低 IOPS 層級上需要一致效能的工作負載進行最佳化。 「標準 SSD」為想要移至雲端的使用者，尤其是在於內部部署 HDD 解決方案上執行之工作負載的變異方面遇到問題的使用者，提供一個良好的入門級體驗。 與標準硬碟相比，標準 SSD 可提供更好的可用性、一致性、可靠性和延遲。 標準 SSD 適用於網頁伺服器、低 IOPS 應用程式伺服器、不常使用的企業應用程式及開發/測試工作負載。 與標準 HDD 一樣，標準 SSD 在所有 Azure VM 上都可用。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

標準 SSD 旨在提供位數毫秒的延遲，IOPS 和輸送量達到上表 99% 所述的限制。 實際 IOPS 和輸送量有時可能因流量模式而異。 與 HDD 磁碟相比，「標準 SSD」將可提供延遲更低的更一致效能。

### <a name="transactions"></a>交易

對於標準 SSD，每個 I/O 操作小於或等於 256 KiB 的輸送量被視為單個 I/O 操作。 大於 256 KiB 輸送量的 I/O 操作被視為尺寸為 256 KiB 的多個 I/O。 這些交易具有計費影響。

## <a name="standard-hdd"></a>標準 HDD

如果 VM 執行的工作負載不在乎延遲，Azure 標準 HDD 可以提供可靠、低成本的磁碟支援。 使用標準儲存體時，資料會儲存在硬碟 (HDD)。 與基於 SSD 的磁片相比，標準硬碟磁片的延遲、IOPS 和輸送量差異可能更大。 標準硬碟磁片旨在在大多數 IO 操作下提供 10ms 以下的寫入延遲和 20ms 以下的讀取延遲，但實際性能可能因 IO 大小和工作負載模式而異。 使用 VM 時，可以使用標準 HDD 磁片進行開發/測試方案和不太關鍵的工作負載。 標準 HDD 在所有 Azure 區域都可用，並可用於所有 Azure VM。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>交易

對於標準 HDD，無論 I/O 大小如何，每個 IO 操作都被視為單個事務。 這些交易具有計費影響。

## <a name="billing"></a>計費

使用受控磁碟時，需考量下列計費資訊：

- 磁碟類型
- 受控磁碟大小
- 快照集
- 輸出資料傳輸
- 交易數

**受控磁碟大小**：受控磁碟會依佈建大小計費。 Azure 會將佈建大小對應 (無條件進位) 至供應項目中最接近的磁碟大小。 若要深入了解提供的磁碟大小，請參閱之前的資料表。 每一個磁碟會對應至供應項目中支援的佈建磁碟大小，並據此計費。 例如，如果您佈建了 200 GiB 的「標準 SSD」，它將會與 E15 (256 GiB) 的磁碟大小供應項目對應。 任何已佈建的磁碟都是使用每月的進階儲存體供應項目價格，以每小時的方式計費。 例如，如果您佈建了 E10 磁碟並在 20 小時後刪除它，便會按 20 小時的比例向您收取 E10 供應項目的費用。 這與寫入磁碟的實際資料量無關。

**快照**：快照根據使用的大小計費。 例如，如果建立佈建容量為 64 GiB 的受控磁碟快照集，而實際使用資料大小為 10 GiB，則只會對已使用的 10 GiB 資料大小收取快照集費用。
