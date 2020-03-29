---
title: Mv2 系列 - Azure 虛擬機器
description: Mv2 系列 VM 的規格。
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6fb0f93d3ac124b21bbc52ddc57bc720de6406e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163891"
---
# <a name="mv2-series"></a>Mv2 系列

Mv2 系列具有高輸送量、低延遲平臺，可在超執行緒英特爾® Xeon ®白金 8180M 2.5GHz（Skylake） 處理器上運行，所有核心基頻率為 2.5 GHz，最大渦輪頻率為 3.8 GHz。 所有 Mv2 系列虛擬機器大小都可以使用標準和高級持久磁片。 Mv2 系列實例是記憶體優化的 VM 大小，提供無與倫比的計算性能，支援大型記憶體資料庫和工作負載，具有高記憶體與 CPU 的比率，非常適合關係資料庫伺服器、大型緩存和記憶體中分析。

Mv2 系列 VM 的功能英特爾®超執行緒技術

進階儲存體：支援

高級存儲緩存：支援

即時移轉：不支援

記憶體保留更新：不支援

寫入加速器：[支援](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1， 2</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1， 2</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2 系列 VM 僅是第 2 代。 如果使用 Linux，請參閱 Azure[上支援第 2 代 VM](./linux/generation-2.md)瞭解如何查找和選擇映射的說明。

<sup>2</sup>對於M416ms_v2和M416s_v2大小，請注意，只有以下映射的初始支援："GEN2：用於 SAP 應用程式的 SUSE Linux 企業伺服器 （SLES） 12 SP4。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
