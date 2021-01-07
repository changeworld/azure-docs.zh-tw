---
title: Eav4 系列和 Easv4 系列
description: Eav4 和 Easv4 系列 Vm 的規格。
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9b6ea9491575236a3ef84d660ead8022d13e01ef
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964123"
---
# <a name="eav4-and-easv4-series"></a>Eav4 和 Easv4 系列

Eav4 系列和 Easv4 系列在多執行緒設定中使用 AMD 的採用 2.35 Ghz EPYC<sup>TM</sup> 7452 處理器，最多可達 256mb L3 快取，並增加執行大部分記憶體優化工作負載的選項。 Eav4 系列和 Easv4 系列具有與 Ev3 和 Esv3 系列相同的記憶體與磁碟設定。

## <a name="eav4-series"></a>Eav4 系列

[ACU](acu.md)： 230-260<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>

Eav4 系列大小以採用 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 處理器為基礎，可達到最大的 3.35 ghz 頻率。 Eav4 系列大小適合記憶體密集型企業應用程式。 資料磁碟儲存體與虛擬機器分開計費。 若要使用 premium SSD，請使用 Easv4 系列大小。 Easv4 大小的定價和計費方式與 Eav3 系列相同。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大 NIC | 預期的網路頻寬 (Mbps)  |
| -----|-----|-----|-----|-----|-----|-----|-----|
| 標準 \_ E2a \_ v4|2|16|50|4|3000 / 46 / 23|2 | 1000 |
| 標準 \_ E4a \_ v4|4|32|100|8|6000 / 93 / 46|2 | 2000 |
| 標準 \_ E8a \_ v4|8|64|200|16|12000 / 187 / 93|4 | 4000 |
| 標準 \_ E16a \_ v4|16|128|400|32|24000 / 375 / 187|8 | 8000 |
| 標準 \_ E20a \_ v4|20|160|500|32|30000/468/234|8 | 10000 |
| 標準 \_ E32a \_ v4|32|256|800|32|48000 / 750 / 375|8 | 16000 |
| 標準 \_ E48a \_ v4|48|384|1200|32|96000/1000 (500) |8 | 24000 |
| 標準 \_ E64a \_ v4|64|512|1600|32|96000/1000 (500) |8 | 30000 |
| 標準 \_ E96a \_ v4|96|672|2400|32|96000/1000 (500) |8 | 30000 |

## <a name="easv4-series"></a>Easv4 系列

[ACU](acu.md)： 230-260<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>

Easv4 系列大小以採用 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 處理器為基礎，可達到最大的 3.35 ghz 頻率，並使用 premium SSD。 Easv4 系列大小適合記憶體密集型企業應用程式。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC | 預期的網路頻寬 (Mbps)  |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50) |3200/48|2 | 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100) |6400/96|2 | 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200) |12800/192|4 | 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400) |25600/384|8 | 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500) |32000/480|8 | 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800) |51200/768|8 | 16000 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200) |76800/1148|8 | 24000 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600) |80000/1200|8 | 30000 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400) |80000/1200|8 | 30000 |

<sup>1</sup>個 [可用的有限核心大小](./constrained-vcpu.md)。

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
