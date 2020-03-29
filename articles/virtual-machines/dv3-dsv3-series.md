---
title: Dv3 和 Dsv3 系列 - Azure 虛擬機器
description: Dv3 和 Dsv3 系列 VM 的規格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164401"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 和 Dsv3 系列

Dv3 系列在英特爾® Xeon ® 8171M 2.1GHz（Skylake）、英特爾® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz （Haswell） 處理器上運行，為大多數一般處理器提供了更好的價值主張用途工作負載。 除了記憶體已擴充 (從 ~3.5 GiB/vCPU 到 4 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Dv3 系列不再有 D/Dv2 系列的高記憶體 VM 大小，這些 VM 大小已移動到記憶體優化的[Ev3 和 Esv3 系列](ev3-esv3-series.md)。

示例 D 系列用例包括企業級應用程式、關係資料庫、記憶體緩存和分析。

## <a name="dv3-series"></a>Dv3 系列

Dv3 系列尺寸在英特爾® Xeon ® 8171M 2.1GHz（Skylake）、英特爾®至強® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz （哈斯韋爾）處理器上運行，配備英特爾渦輪增壓技術 2.0。 Dv3 系列大小提供 CPU、記憶體與暫存儲存體組合，適用於大多數生產環境工作負載。

資料磁碟儲存體與虛擬機器分開計費。 若要使用進階儲存體磁碟，請使用 Dsv3 大小。 Dsv3 大小的定價和計費方式與 Dv3 系列相同。

Dv3 系列 VM 採用英特爾®超執行緒技術。

ACU：160-190

進階儲存體：不支援

高級存儲緩存：不支援

即時移轉：支援

記憶體保留更新：受支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大臨時存儲輸送量：IOPS/讀取 MBps/寫入 MBps | 最大 NIC/網路頻寬 |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3 系列

Dsv3 系列尺寸在英特爾® Xeon ® 8171M 2.1GHz（Skylake）、英特爾® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz （Haswell） 處理器上運行，採用英特爾渦輪增壓技術 2.0 並使用高級存儲。 Dsv3 系列大小提供 CPU、記憶體與暫存儲存憶體組合，適用於大多數生產環境工作負載。

Dsv3 系列 VM 採用英特爾®超執行緒技術。

ACU：160-190

進階儲存體：支援

高級存儲緩存：支援

即時移轉：支援

記憶體保留更新：受支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大緩存和臨時存儲輸送量：IOPS/MBps（GiB 中的緩存大小） | 最大未緩存磁片輸送量：IOPS/MBps | 最大 NIC/預期網路頻寬 （Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

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
