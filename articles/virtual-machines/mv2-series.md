---
title: Mv2 系列 - Azure 虛擬機器
description: Mv2 系列 VM 的規格。
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885781"
---
# <a name="mv2-series"></a>Mv2 系列

Mv2 系列具有高吞吐量、低延遲平臺,可在超線程英特爾® Xeon ®白金 8180M 2.5GHz(Skylake) 處理器上運行,所有核心基頻率為 2.5 GHz,最大渦輪頻率為 3.8 GHz。 所有 Mv2 系列虛擬機器大小都可以使用標準和高級持久磁碟。 Mv2 系列實例是記憶體優化的 VM 大小,提供無與倫比的運算效能,支援大型記憶體資料庫和工作負載,具有高記憶體 CPU 比率,非常適合關係資料庫伺服器、大型緩存和記憶體分析。

Mv2 系列 VM 的功能英特爾®超線程技術

進階儲存體：支援

進階儲存快取:支援

即時移轉:不支援

記憶體保留更新:不支援

寫入加速器:[支援](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2 系列 VM 僅是第 2 代。 如果使用 Linux,請參閱 Azure[上支援第 2 代 VM](./linux/generation-2.md)瞭解如何查找和選擇映射的說明。 如果使用 Windows,請參閱 Azure[上支援第 2 代 VM](./windows/generation-2.md)瞭解如何查找和選擇映射的說明。 所有四個不同的 Mv2 系列 VM 類型清單所需的最小作業系統版本,如下所示:

- Windows Server 2019 或更新版本
- SUSE Linux 企業伺服器 12 SP4 及更高版本或 SUSE Linux 企業伺服器 15 SP1 及更高版本
- 紅帽企業 Linux 7.6、 7.7、 8.1 或更高版本 
- 甲骨文企業Linux 7.7或更高版本



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
