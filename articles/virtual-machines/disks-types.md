---
title: 選取適用於 Azure IaaS VM 的磁碟類型 - 受控磁碟
description: 了解可供虛擬機器使用的 Azure 磁碟類型，包括 Ultra 磁碟、進階 SSD、標準 SSD 與標準 HDD。
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ef481b73b6dc42bc35252c08ae8d63b9de95b2ba
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325089"
---
# <a name="what-disk-types-are-available-in-azure"></a>在 Azure 中可使用哪些磁碟類型？

Azure 受控磁片目前提供四種磁片類型，每種類型都是針對特定客戶案例。

## <a name="disk-comparison"></a>磁碟比較

下表提供的是 ultra 磁片、高階固態硬碟 (SSD) 、標準 SSD 和標準硬碟磁片磁碟機的比較， (HDD) 適用于受控磁片，以協助您決定要使用的內容。

| 詳細資料 | Ultra 磁碟 | 進階 SSD | 標準 SSD | 標準 HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|磁碟類型   |SSD   |SSD   |SSD   |HDD   |
|案例   |需要大量 IO 的工作負載，例如 [SAP Hana](workloads/sap/hana-vm-operations-storage.md)、頂層資料庫 (例如 SQL、Oracle) 和其他大量交易工作負載。   |生產環境和重視效能的工作負載   |網頁伺服器、輕量使用的企業應用程式和開發/測試   |備份、不重要、存取不頻繁   |
|最大磁碟大小   |65,536 GiB    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大輸送量   |2,000 MB/秒    |900 MB/秒   |750 MB/秒   |500 MB/秒   |
|最大 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra 磁碟

Azure Ultra 磁碟可為 Azure IaaS VM 提供高輸送量、高 IOPS 以及一致的低延遲磁碟儲存體。 Ultra 磁片的其他優點包括能夠以動態方式變更磁片的效能和您的工作負載，而不需要重新開機 (VM) 的虛擬機器。 Ultra 磁碟適用於處理大量資料的工作負載 (例如 SAP Hana)、最上層資料庫，以及高交易量的工作負載。 Ultra 磁碟只可用來作為資料磁碟。 建議您使用進階 SSD 作為 OS 磁碟。

### <a name="performance"></a>效能

當您佈建 Ultra 磁碟時，您可個別設定磁碟的容量和效能。 Ultra 磁片有數種固定大小，範圍從 4 GiB 到 64 TiB，並提供彈性的效能設定模型，可讓您獨立設定 IOPS 和輸送量。

Ultra 磁片的一些主要功能如下：

- 磁片容量： Ultra 磁片容量的範圍是 4 GiB （最高達 64 TiB）。
- 磁片 IOPS： Ultra 磁片支援 300 IOPS/GiB 的 IOPS 限制，每個磁片最多可達 160 K IOPS。 若要達到您布建的 IOPS，請確定選取的磁片 IOPS 小於 VM IOPS 限制。 每個磁片的保證 IOPS 下限為 2 IOPS/GiB，整體基準最低為 100 IOPS。 例如，如果您有4個 GiB ultra 磁片，則至少會有 100 IOPS，而不是八個 IOPS。
- 磁片輸送量：使用 ultra 磁片時，單一磁片的輸送量限制為每個已布建 IOPS 的 256 KiB/秒，最多可達每個磁片 2000 MBps (其中 MBps = 10 ^ 6 位元組/秒) 。 每一磁片的保證輸送量下限為每個已布建 IOPS 4KiB/s，整體基準下限為 1 MBps。
- Ultra 磁片支援調整磁片效能屬性 (IOPS 和輸送量) 在執行時間，而不需要卸離虛擬機器的磁片。 向磁碟發出磁碟效能調整作業後，最多可能需要一個小時，變更才會實際生效。 在24小時的時間範圍內，有四個效能調整作業的大小限制。 效能調整作業可能會因為缺乏效能頻寬容量而失敗。

### <a name="disk-size"></a>磁碟大小

|磁碟大小 (GiB)  |IOPS 上限  |輸送量上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |153600         |2,000         |
|1,024-65,536 (此範圍中的大小會以 1 TiB 為單位遞增)     |160,000         |2,000         |

Ultra 磁片的設計目的是要提供上表99.99% 的時間所述的毫秒延遲和目標 IOPS 和輸送量。

### <a name="ga-scope-and-limitations"></a>GA 範圍和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


如果您想要開始使用 ultra 磁片，請參閱下列主題的文章： [使用 Azure ultra 磁片](disks-enable-ultra-ssd.md)。

## <a name="premium-ssd"></a>進階 SSD

針對輸入/輸出 (IO) 工作負載大的虛擬機器 (VM)，Azure 進階 SSD 可提供高效能和低延遲的磁碟支援。 您可以將現有 VM 磁碟遷移到進階 SSD，以利用進階儲存體磁碟的速度和效能。 進階 SSD 適用於任務關鍵性的生產應用程式。 Premium Ssd 只能搭配 premium 儲存體相容的 VM 系列使用。

若要深入瞭解適用于 Windows 或 Linux 的 Azure 中的個別 VM 類型與大小，包括符合 premium 儲存體的大小，請參閱 [azure 中虛擬機器的大小](sizes.md)。 在本文中，您需要檢查每個個別 VM 大小的文章，以判斷它是否符合 premium 儲存體相容性。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

當您佈建進階儲存體磁碟時，不同於標準儲存體的是，您可獲得該磁碟的容量、IOPS 和輸送量保證。 例如，如果您建立 P50 磁碟，Azure 會為該磁碟佈建 4,095 GB 儲存體容量、7,500 IOPS 和 250 MB/秒的輸送量。 您的應用程式可以使用全部或部分的容量和效能。 進階固態硬碟的設計是為了在 99.9% 的時間內，提供低個位數毫秒延遲以及上表所述的目標 IOPS 和輸送量。

## <a name="bursting"></a>爆破

進階 SSD 小於 P30 的大小現在提供磁片高載，並可將其 IOPS 高載為每個磁片（最高3500）及其頻寬（最高 170 MB/秒）。 高載是自動化的，而且會根據信用系統來運作。 當磁片流量低於已布建的效能目標時，會自動在高載值區中累積點數，且當流量高載超過目標時，會自動取用點數，最高高載限制。 如果您有要取用的高載點數，則最大高載限制會定義磁片 IOPS & 頻寬的上限。 磁片高載可針對無法預期的 IO 模式變更提供更好的容錯。 您可以使用它來針對 OS 磁片開機和具有尖峰流量的應用程式，充分利用它。    

預設會針對適用磁片大小的新部署啟用磁片高載支援，而不需要使用者動作。 針對適用大小的現有磁片，您可以使用下列兩個選項之一來啟用負載平衡：卸離並重新連接磁片，或停止並重新啟動連結的 VM。 當磁片連接至支援尖峰高載限制30分鐘的虛擬機器時，所有高載的適用磁片大小都會從完整的高載點數值區開始。 若要深入瞭解高載在 Azure 磁片上的運作方式，請參閱 [進階 SSD](linux/disk-bursting.md)負載平衡。 

### <a name="transactions"></a>交易

針對 premium Ssd，每個 i/o 作業小於或等於 256 KiB 的輸送量會視為單一 i/o 作業。 大於 256 KiB 輸送量的 i/o 作業會被視為大小為 256 KiB 的多個 i/o。

## <a name="standard-ssd"></a>標準 SSD

Azure 標準 SSD 是符合成本效益的儲存體選項，已針對在較低 IOPS 層級上需要一致效能的工作負載進行最佳化。 「標準 SSD」為想要移至雲端的使用者，尤其是在於內部部署 HDD 解決方案上執行之工作負載的變異方面遇到問題的使用者，提供一個良好的入門級體驗。 相較于標準 Hdd，標準 Ssd 提供更佳的可用性、一致性、可靠性及延遲。 標準 SSD 適用於網頁伺服器、低 IOPS 應用程式伺服器、不常使用的企業應用程式及開發/測試工作負載。 如同標準 Hdd，標準 Ssd 可在所有 Azure Vm 上使用。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

標準 Ssd 的設計目的是要提供一個位數的毫秒延遲，以及上表99% 的時間內所述的最高 IOPS 和輸送量。 實際的 IOPS 和輸送量有時可能會因流量模式而有所不同。 與 HDD 磁碟相比，「標準 SSD」將可提供延遲更低的更一致效能。

### <a name="transactions"></a>交易

若為標準 Ssd，每個 i/o 作業小於或等於 256 KiB 的輸送量都會被視為單一 i/o 操作。 大於 256 KiB 輸送量的 i/o 作業會被視為大小為 256 KiB 的多個 i/o。 這些交易會產生計費影響。

## <a name="standard-hdd"></a>標準 HDD

如果 VM 執行的工作負載不在乎延遲，Azure 標準 HDD 可以提供可靠、低成本的磁碟支援。 使用標準儲存體時，資料會儲存在硬碟 (HDD)。 相較于以 SSD 為基礎的磁片，標準 HDD 磁片的延遲、IOPS 和輸送量可能會有更廣泛的差異。 標準 HDD 磁片的設計目的，是為了在大部分 IO 作業的情況下，于20毫秒的10毫秒和讀取延遲下提供寫入延遲，不過實際效能可能會因 IO 大小和工作負載模式而異。 使用 Vm 時，您可以針對開發/測試案例和較不重要的工作負載使用標準 HDD 磁片。 標準 Hdd 可在所有 Azure 區域中使用，並可與所有 Azure Vm 搭配使用。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>交易

針對標準 Hdd，每個 IO 作業都會視為單一交易，不論 i/o 大小為何。 這些交易會產生計費影響。

## <a name="billing"></a>計費

使用受控磁碟時，需考量下列計費資訊：

- 磁碟類型
- 受控磁碟大小
- 快照集
- 輸出資料傳輸
- 交易數

**受控磁碟大小**：受控磁碟會依佈建大小計費。 Azure 會將佈建大小對應 (無條件進位) 至供應項目中最接近的磁碟大小。 若要深入了解提供的磁碟大小，請參閱之前的資料表。 每一個磁碟會對應至供應項目中支援的佈建磁碟大小，並據此計費。 例如，如果您佈建了 200 GiB 的「標準 SSD」，它將會與 E15 (256 GiB) 的磁碟大小供應項目對應。 任何已布建磁片的計費方式會使用儲存體供應專案的每月價格依小時比例計費。 例如，如果您佈建了 E10 磁碟並在 20 小時後刪除它，便會按 20 小時的比例向您收取 E10 供應項目的費用。 這與寫入磁碟的實際資料量無關。

**快照** 集：快照集會根據使用的大小來計費。 例如，如果建立佈建容量為 64 GiB 的受控磁碟快照集，而實際使用資料大小為 10 GiB，則只會對已使用的 10 GiB 資料大小收取快照集費用。

如需有關快照集的詳細資訊，請參閱[受控磁碟概觀](managed-disks-overview.md)中有關快照集的小節。

**輸出資料傳輸**： [輸出資料傳輸](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

**交易**：您需支付在標準受控磁片上執行的交易數目。 若為標準 Ssd，每個 i/o 作業小於或等於 256 KiB 的輸送量都會被視為單一 i/o 操作。 大於 256 KiB 輸送量的 i/o 作業會被視為大小為 256 KiB 的多個 i/o。 針對標準 Hdd，每個 IO 作業都會視為單一交易，不論 i/o 大小為何。

如需受控磁碟定價的詳細資訊，包括交易成本，請參閱 [受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra 磁片 VM 保留費用

Azure Vm 具有可指出是否與 ultra 磁片相容的功能。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當 Ultra 磁碟連結至與 Ultra 磁碟相容的 VM 時，就不會收取此費用。 此費用會根據在 VM 上佈建的 vCPU 計費。 

> [!Note]
> 針對 [受條件約束的核心 VM 大小](constrained-vcpu.md)，保留費用是根據實際的個 vcpu 數目，而不是受限制的核心數目。 針對 Standard_E32 8s_v3，保留費用將以32核心為基礎。 

請參閱 [Azure 磁片定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/) 以取得 ultra 磁片定價詳細資料。

### <a name="azure-disk-reservation"></a>Azure 磁片保留

磁片保留可讓您以折扣預先購買一年的磁片儲存體，以降低總成本。 購買磁片保留時，您會在目的地區域中選取特定的磁片 SKU，例如，在美國東部2區域中的 10 P30 (1TiB) premium Ssd 一年。 保留體驗類似于保留的虛擬機器 (VM) 實例。 您可以組合 VM 和磁片保留區，以最大化您的節省量。 目前，「Azure 磁片保留」為 P30 (1TiB) 的 premium SSD Sku 提供一年承諾用量方案，可在所有生產區域中 P80 (32 TiB) 。 如需保留磁片定價的詳細資訊，請參閱 [Azure 磁片定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="next-steps"></a>後續步驟

請參閱 [受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/) 以開始著手。
