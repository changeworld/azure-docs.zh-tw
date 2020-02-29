---
title: Dv3 和 Dsv3 系列-Azure 虛擬機器
description: Dv3 和 Dsv3 系列 Vm 的規格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164401"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 和 Dsv3 系列

Dv3 系列執行于 Intel®的® 8171M 2.1 GHz （Skylake）、Intel®的® E5-2673 v4 2.3 g h z （Broadwell），或超執行緒設定中的 Intel®（r）® E5-2673 v3 2.4 g h z （Haswell）處理器，為最常見的提供更好的價值主張目的工作負載。 除了記憶體已擴充 (從 ~3.5 GiB/vCPU 到 4 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Dv3 系列不再具有 D/Dv2 系列的高記憶體 VM 大小，而是已移至記憶體優化[Ev3 和 Esv3 系列](ev3-esv3-series.md)。

D 系列使用案例的範例包括企業級應用程式、關係資料庫、記憶體內部快取及分析。

## <a name="dv3-series"></a>Dv3 系列

Dv3 系列大小執行于 Intel®的® 8171M 2.1 GHz （Skylake）、Intel®的® E5-2673 v4 2.3 GHz （Broadwell），或 Intel®的® E5-2673 v3 2.4 g h z （Haswell）處理器與 Intel Turbo 加速技術2.0。 Dv3 系列大小提供 CPU、記憶體與暫存儲存體組合，適用於大多數生產環境工作負載。

資料磁碟儲存體與虛擬機器分開計費。 若要使用進階儲存體磁碟，請使用 Dsv3 大小。 Dsv3 大小的定價和計費方式與 Dv3 系列相同。

Dv3 系列 Vm 功能 Intel®超執行緒技術。

ACU：160-190

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：支援

記憶體保留更新：支援

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

Dsv3 系列大小執行于 Intel®的® 8171M 2.1 GHz （Skylake）、Intel®的® E5-2673 v4 2.3 GHz （Broadwell），或 Intel®的® E5-2673 v3 2.4 g h z （Haswell）處理器，搭配 Intel Turbo 加速技術2.0 並使用 premium storage。 Dsv3 系列大小提供 CPU、記憶體與暫存儲存憶體組合，適用於大多數生產環境工作負載。

Dsv3 系列 Vm 功能 Intel®超執行緒技術。

ACU：160-190

進階儲存體：支援

進階儲存體快取：支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps （GiB 中的快取大小） | 最大未快取的磁片輸送量： IOPS/MBps | 最大 Nic/預期的網路頻寬（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 （50）       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 （100）      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 （200）    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 （400）    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 （800）    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 （1200）   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 （1600） | 80000/1200 | 8/30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
