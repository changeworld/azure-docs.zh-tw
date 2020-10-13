---
title: Mv2 系列-Azure 虛擬機器
description: Mv2 系列 Vm 的規格。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 5bf6a17c1a4aa1c7f212045af0d1b750ddb2a906
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653548"
---
# <a name="mv2-series"></a>Mv2 系列

Mv2 系列提供高輸送量、低延遲的平臺，在超執行緒 Intel®的®白金 GHz 上執行，並以 2.5 GHz 的最大 turbo 頻率和 3.8 GHz 的最大 turbo 頻率 (Skylake) 處理器。 所有的 Mv2 系列虛擬機器大小都可以同時使用標準和 premium 的持續性磁片。 Mv2 系列實例是記憶體優化的 VM 大小，可提供無與倫比的計算效能，以支援大型記憶體內部資料庫和工作負載，且具有高記憶體與 CPU 的比例，適用于關係資料庫伺服器、大型快取，以及記憶體內部分析。

Mv2 系列 VM 的功能 Intel® Hyper-Threading 技術

[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
[寫入加速器](./how-to-enable-write-accelerator.md)：支援<br>
<br>

|大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC | 預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040)  | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040)  | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080)  | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080)  | 80000/2000 | 8 | 32000 |

<sup>1</sup> Mv2 系列 vm 僅限第2代，並支援第2代支援映射的子集。 如需 Mv2 系列支援的映射完整清單，請參閱下方。 如果您使用 Linux，請參閱 [Azure 上的第2代 Vm 支援](./linux/generation-2.md) ，以取得如何尋找和選取映射的指示。 如果您使用的是 Windows，請參閱 [Azure 上的第2代 Vm 支援](./windows/generation-2.md) ，以取得如何尋找和選取映射的指示。 

- Windows Server 2019 或更新版本
- SUSE Linux Enterprise Server 12 SP4 和更新版本，或 SUSE Linux Enterprise Server 15 SP1 和更新版本
- Red Hat Enterprise Linux 7.6、7.7、8.1 或更新版本 
- Oracle Enterprise Linux 7.7 或更新版本



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

定價計算機： [定價計算機](https://azure.microsoft.com/pricing/calculator/)

磁片類型的詳細資訊： [磁片類型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。