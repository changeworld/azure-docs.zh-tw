---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e145cf481dd165144b188e6205e4b78cc61359fd
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202486"
---
## <a name="premium-ssd"></a>進階 SSD

針對輸入/輸出 (IO) 工作負載大的虛擬機器 (VM)，Azure 進階 SSD 可提供高效能和低延遲的磁碟支援。 您可以將現有 VM 磁碟遷移到進階 SSD，以利用進階儲存體磁碟的速度和效能。 進階 SSD 適用於任務關鍵性的生產應用程式。 Premium Ssd 只能與高階儲存體相容的 VM 系列搭配使用。

若要深入瞭解適用于 Windows 的 Azure 中的個別 VM 類型和大小，包括哪些大小與高階儲存體相容，請參閱[WINDOWS VM 大小](../articles/virtual-machines/windows/sizes.md)。 若要深入瞭解適用于 Linux 的 Azure 中的個別 VM 類型和大小，包括哪些大小與高階儲存體相容，請參閱[LINUX VM 大小](../articles/virtual-machines/linux/sizes.md)。 從這兩篇文章中，您必須檢查每個個別的 VM 大小一文，以判斷它是否為高階儲存體相容。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

當您佈建進階儲存體磁碟時，不同於標準儲存體的是，您可獲得該磁碟的容量、IOPS 和輸送量保證。 例如，如果您建立 P50 磁碟，Azure 會為該磁碟佈建 4,095 GB 儲存體容量、7,500 IOPS 和 250 MB/秒的輸送量。 您的應用程式可以使用全部或部分的容量和效能。 進階 SSD 磁片的設計可提供低的單一位數毫秒延遲，以及上表99.9% 的時間所述的目標 IOPS 和輸送量。

## <a name="bursting-preview"></a>高載（預覽）

小於 P30 的進階 SSD 大小現在提供磁片高載（預覽），並可將每個磁片的 IOPS 高載到3500，而其頻寬上限為 170 Mbps。 高載會自動化，並根據信用系統運作。 當磁片流量低於已布建的效能目標時，會自動在高載值區中累積信用額度，而當流量激增超過目標時，會自動取用信用額度，最多可達最大容量上限。 最大高載限制會定義磁片 IOPS 的上限 & 頻寬，即使您有高載點數可供使用。 磁片高載對於 IO 模式無法預期的變更提供更好的容錯能力。 針對 OS 磁片開機和具有尖峰流量的應用程式，您可以充分利用它。    

根據預設，[預覽區域](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)中適用磁片大小的新部署將會啟用磁片高載支援，使用者不必採取任何動作。 對於適用大小的現有磁片，您可以使用兩個選項的其中一個來啟用負載平衡：卸離並重新連結磁片，或停止並重新啟動連接的 VM。 當磁片連接到支援30分鐘尖峰高載上限的虛擬機器時，所有高載適用的磁片大小都會以完整的高載點數 bucket 開始。 若要深入瞭解 Azure 磁片上的高載如何工作，請參閱[進階 SSD](../articles/virtual-machines/linux/disk-bursting.md)負載平衡。 

### <a name="transactions"></a>交易

針對高階 Ssd，小於或等於 256 KiB 輸送量的每個 i/o 作業都會被視為單一 i/o 作業。 大於 256 KiB 輸送量的 i/o 作業會視為大小為 256 KiB 的多個 i/o。

## <a name="standard-ssd"></a>標準 SSD

Azure 標準 SSD 是符合成本效益的儲存體選項，已針對在較低 IOPS 層級上需要一致效能的工作負載進行最佳化。 「標準 SSD」為想要移至雲端的使用者，尤其是在於內部部署 HDD 解決方案上執行之工作負載的變異方面遇到問題的使用者，提供一個良好的入門級體驗。 相較于標準 Hdd，標準 Ssd 提供更佳的可用性、一致性、可靠性和延遲。 標準 SSD 適用於網頁伺服器、低 IOPS 應用程式伺服器、不常使用的企業應用程式及開發/測試工作負載。 如同標準 Hdd，標準 Ssd 適用于所有 Azure Vm。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

標準 Ssd 的設計是為了提供單一位數毫秒延遲，以及上表99% 當時所述限制的 IOPS 和輸送量。 實際的 IOPS 和輸送量有時會因流量模式而有所不同。 與 HDD 磁碟相比，「標準 SSD」將可提供延遲更低的更一致效能。

### <a name="transactions"></a>交易

針對標準 Ssd，小於或等於 256 KiB 輸送量的每個 i/o 作業都會被視為單一 i/o 作業。 大於 256 KiB 輸送量的 i/o 作業會視為大小為 256 KiB 的多個 i/o。 這些交易會產生計費影響。

## <a name="standard-hdd"></a>標準 HDD

如果 VM 執行的工作負載不在乎延遲，Azure 標準 HDD 可以提供可靠、低成本的磁碟支援。 使用標準儲存體時，資料會儲存在硬碟 (HDD)。 相較于以 SSD 為基礎的磁片，標準 HDD 磁片的延遲、IOPS 和輸送量可能會變得更廣泛。 標準 HDD 磁片的設計目的是要在10毫秒下提供寫入延遲，並在20毫秒下讀取延遲以進行大部分的 IO 作業，但是實際的效能可能會因 IO 大小和工作負載模式而有所不同。 使用 Vm 時，您可以針對開發/測試案例和較不重要的工作負載使用標準 HDD 磁片。 標準 Hdd 適用于所有 Azure 區域，並可與所有 Azure Vm 搭配使用。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>交易

針對標準 Hdd，不論 i/o 大小為何，每個 IO 作業都會被視為單一交易。 這些交易會產生計費影響。

## <a name="billing"></a>計費

使用受控磁碟時，需考量下列計費資訊：

- 磁碟類型
- 受控磁碟大小
- 快照集
- 輸出資料傳輸
- 交易數

**受控磁碟大小**：受控磁碟會依佈建大小計費。 Azure 會將佈建大小對應 (無條件進位) 至供應項目中最接近的磁碟大小。 若要深入了解提供的磁碟大小，請參閱之前的資料表。 每一個磁碟會對應至供應項目中支援的佈建磁碟大小，並據此計費。 例如，如果您佈建了 200 GiB 的「標準 SSD」，它將會與 E15 (256 GiB) 的磁碟大小供應項目對應。 任何已佈建的磁碟都是使用每月的進階儲存體供應項目價格，以每小時的方式計費。 例如，如果您佈建了 E10 磁碟並在 20 小時後刪除它，便會按 20 小時的比例向您收取 E10 供應項目的費用。 這與寫入磁碟的實際資料量無關。

**快照**集：快照集是根據使用的大小來計費。 例如，如果建立佈建容量為 64 GiB 的受控磁碟快照集，而實際使用資料大小為 10 GiB，則只會對已使用的 10 GiB 資料大小收取快照集費用。
