---
title: Dv2 和 Dsv2 系列-Azure 虛擬機器
description: Dv2 和 Dsv2 系列 Vm 的規格。
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 785e72a47d68bf368e160b1f3bf2a6d762d1f100
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649689"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 和 DSv2 系列

Dv2 和 Dsv2 系列（原始 D 系列的後續操作）提供功能更強大的 CPU 和最佳的 CPU 對記憶體設定，使其適用于大部分的生產工作負載。 Dv2 系列比 D 系列快約 35%。 Dv2 系列執行于 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) 、Intel®® E5-2673 v4 2.3 GHz (Broadwell) ，或 Intel®® E5-2673 v3 2.4 GHz (Haswell) 處理器與 Intel Turbo 加速技術2.0。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

## <a name="dv2-series"></a>Dv2 系列

Dv2 系列大小是在 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) 或 Intel®® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel®® E5-2673 v3 2.4 GHz (Haswell) 處理器搭配 Intel Turbo 加速技術2.0。

[ACU](acu.md)：210-250<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大資料磁碟 | 輸送量：IOPS | 最大 NIC | 預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2|750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2|1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4|3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8|6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>DSv2 系列

DSv2 系列大小是在 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) 或 Intel®最高® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel®® E5-2673 v3 2.4 GHz (Haswell) 處理器搭配 Intel Turbo 加速技術2.0 和使用 premium 儲存體。

[ACU](acu.md)：210-250<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)     | 3200/48   | 2|750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)     | 6400/96   | 2|1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172)  | 12800/192 | 4|3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344)  | 25600/384 | 8|6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688)  | 51200/768 | 8|12000 |

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