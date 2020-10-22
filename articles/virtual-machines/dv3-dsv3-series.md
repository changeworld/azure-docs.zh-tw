---
title: Dv3 和 Dsv3 系列
description: Dv3 和 Dsv3 系列 Vm 的規格。
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 3ac90d3cabc179ecd1c1e421fd026a584f7158e3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368565"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 和 Dsv3 系列

Dv3 系列是在 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) 、Intel®® E5-2673 v4 2.3 GHz (Broadwell) ，或 Intel®最高® E5-2673 v3 2.4 GHz (Haswell) 處理器（在超執行緒設定中），為大部分的一般用途工作負載提供更好的價值主張。 除了記憶體已擴充 (從 ~3.5 GiB/vCPU 到 4 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Dv3 系列不再擁有 D/Dv2 系列的高記憶體 VM，這些都已移至記憶體最佳化的 [Ev3 和 Esv3 系列](ev3-esv3-series.md)。

範例 D 系列使用案例包括企業級應用程式、關係資料庫、記憶體內部快取及分析。

## <a name="dv3-series"></a>Dv3 系列

Dv3 系列大小是在 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) 、Intel®® E5-2673 v4 2.3 GHz (Broadwell) ，或 Intel®® E5-2673 v3 2.4 GHz (Haswell) 處理器搭配 [intel &reg; Turbo 加速技術 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)。 Dv3 系列大小提供 CPU、記憶體與暫存儲存體組合，適用於大多數生產環境工作負載。

資料磁碟儲存體與虛擬機器分開計費。 若要使用進階儲存體磁碟，請使用 Dsv3 大小。 Dsv3 大小的定價和計費方式與 Dv3 系列相同。

Dv3 系列 Vm 具備 Intel® Hyper-Threading 技術的功能。

[ACU](acu.md)：160-190<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大 Nic/網路頻寬 |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3 系列

Dsv3 系列大小是在 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) 、Intel®® E5-2673 v4 2.3 GHz (Broadwell) ，或 Intel®® E5-2673 v3 2.4 GHz (Haswell) 處理器搭配 [intel &reg; Turbo 加速技術 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) 和使用 premium 儲存體。 Dsv3 系列大小提供 CPU、記憶體與暫存儲存憶體組合，適用於大多數生產環境工作負載。

Dsv3 系列 Vm 具備 Intel® Hyper-Threading 技術的功能。

[ACU](acu.md)：160-190<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大高載快取和暫存儲存體輸送量： IOPS/MBps<sup>1</sup> | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大高載未快取磁片輸送量： IOPS/MBps<sup>1</sup> | 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)        | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)       | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)     | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)     | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)     | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)    | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600)  | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup>  個 Dsv3 系列 vm 可以提高其磁片效能，並一次最多可達30分鐘的最高 [負載](linux/disk-bursting.md) 。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)
- [定價計算機](https://azure.microsoft.com/pricing/calculator/)
- 如需磁片類型的詳細資訊，請參閱 [Azure 中有哪些可用的磁片類型？](disks-types.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
