---
title: Lsv2 系列 - Azure 虛擬機器
description: Lsv2 系列 VM 的規格。
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164027"
---
# <a name="lsv2-series"></a>Lsv2 系列

Lsv2 系列以在全核心加速為 2.55GHz 且最大加速為 3.0GHz 之 [AMD EPYC<sup>TM</sup> 7551 處理器](https://www.amd.com/en/products/epyc-7000-series)上執行的高輸送量、低延遲、直接對應的本機 NVMe 儲存體為特色。 Lsv2 系列 VM 提供採用同時多執行緒設定且具有 8 到 80 個 vCPU 的大小。  每一 vCPU 有 8 GiB 記憶體，每 8 個 vCPU 有一個 1.92 TB NVMe SSD M.2 裝置，在 L80s v2 上最高可達 19.2 TB (10 x 1.92 TB)。

> [!NOTE]
> Lsv2 系列 VM 經過優化，可使用直接連接到 VM 的節點上的本地磁片，而不是使用持久資料磁片。 這可提升您工作負載的 IOPS/輸送量。 Lsv2 和 Ls 系列不支援創建本機快取以增加持久資料磁片可實現的 IAP。
>
> 本地磁片的高輸送量和 IAP 使 Lsv2 系列 VM 成為 NoSQL 存儲（如 Apache Cassandra 和 MongoDB）的理想選擇，這些存儲跨多個 VM 複製資料，以便在單個 VM 發生故障時實現持久性。
>
> 要瞭解更多資訊，請參閱優化[適用于 Windows](../virtual-machines/windows/storage-performance.md)或[Linux](../virtual-machines/linux/storage-performance.md)的 Lsv2 系列虛擬機器的性能。  

ACU： 150-175

進階儲存體：支援

高級存儲緩存：不支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 記憶體 (GiB) | 暫存磁碟<sup>1</sup> (GiB) | NVMe 磁碟<sup>2</sup> | NVMe 磁片輸送量<sup>3（</sup>讀取 IOPS/MBps） | 最大未緩存資料磁片輸送量（IIP/MBps）<sup>4</sup> | 資料磁碟數上限 | 最大 NIC/預期的網路頻寬 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1 x 1.92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2 x 1.92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4 x 1.92 TB  | 1.5M/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1.92 TB  | 2.2M/14000   | 48000/960  | 32 | 8 / 16000* |
| Standard_L64s_v2  | 64 | 512 | 640 |  8 x 1.92 TB  | 2.9M/16000   | 64000/1280 | 32 | 8 / 16000* |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10 x 1.92 TB | 3.8M/20000 | 80000/1400 | 32 | 8 / 16000* |

<sup>1</sup> Lsv2 系列 VM 具有一個適用於 OS 分頁檔使用的標準 SCSI 型暫存資源磁碟 (在 Windows 上為 D:，在 Linux 上為 /dev/sdb)。 此磁碟針對每 8 個 vCPU 提供 80 GiB 的儲存體、4,000 IOPS 及 80 MBps 傳輸率 (例如 Standard_L80s_v2 提供 40,000 IOPS 和 800 MBPS 的 800 GiB)。 這可確保 NVMe 磁碟機可完全專供應用程式使用。 此磁碟為暫時磁碟，所有資料在停止/解除配置時都會遺失。

<sup>2</sup>本機 NVMe 磁碟都是暫時的，如果您停止/解除配置您的 VM，這些磁碟上的資料將會遺失。

<sup>3</sup> Hyper-V NVMe Direct 技術對已安全對應至客體 VM 空間的本機 NVMe 磁碟機提供未節流存取。  若要達到最大效能，必須使用來自 Azure Marketplace 的最新 WS2019 組建或是 Ubuntu 18.04 或 16.04。  寫入效能會依據 IO 大小、磁碟機負載及容量使用率而有所不同。

<sup>4</sup> Lsv2 系列 VM 並未針對資料磁碟提供主機快取，因為這對 Lsv2 工作負載沒有幫助。  不過，Lsv2 VM 可通融 Azure 的「暫時性 VM」OS 磁碟選項 (最多 30 GiB)。

<sup>具有</sup>64 個 vCPU 的 5 個 VM 需要以下支援的客體作業系統之一：

- Windows 伺服器 2016 或更高版本
- Ubuntu 16.04 LTS 或更高版本，使用 Azure 調諧內核（4.15 內核或更高版本）
- SLES 12 SP2 或更高版本
- RHEL 或 CentOS 版本 6.7 到 6.10，安裝了 Microsoft 提供的 LIS 套裝軟體 4.3.1（或更高版本）
- RHEL 或 CentOS 版本 7.3，安裝了 Microsoft 提供的 LIS 套裝軟體 4.2.1（或更高版本）
- RHEL 或 CentOS 版本 7.6 或更高版本
- 帶有 UEK4 或更高版本的 Oracle Linux
- Debian 9 與後埠內核， Debian 10 或更高版本
- 具有 4.14 個或更高版本的 CoreOS

## <a name="size-table-definitions"></a>資料表大小定義

- 儲存容量會以 GiB 或是 1024^3 位元組為單位顯示。 當比較使用 GB (1000^3 位元組) 為度量單位的磁碟與使用 GiB (1024^3) 為度量單位的磁碟時，請記住以 GiB 為單位提供的容量數字可能較小。 例如，1023 GiB = 1098.4 GB
- 磁碟輸送量是以每秒輸入/輸出作業 (IOPS) 和 MBps 進行測量，其中 MBps = 10^6 位元組/每秒。
- 如果您想要讓虛擬機器獲得最佳效能，應將資料磁碟數目限制為每個 vCPU 有 2 個磁碟。
- **預期的網路頻寬**是根據跨所有目的地之所有 NIC 的 VM 類型所[配置的最大彙總頻寬](../virtual-network/virtual-machine-network-throughput.md)。 不保證上限，但會提供選取想要之應用程式的正確 VM 類型的指引。 實際網路效能取決於各種因素，包括網路壅塞、應用程式負載和網路設定。 如需最佳化網路輸送量的資訊，請參閱[最佳化 Windows 和 Linux 的網路輸送量](../virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要達到 Linux 或 Windows 上的預期網路效能，可能需要選取特定版本，或最佳化 VM。 如需詳細資訊，請參閱[如何可靠地測試虛擬機器輸送量](../virtual-network/virtual-network-bandwidth-testing.md)。

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
