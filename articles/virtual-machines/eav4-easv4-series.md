---
title: Eav4 系列和 Easv4 系列 - Azure 虛擬機器
description: Eav4 和 Easv4 系列 VM 的規格。
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164384"
---
# <a name="eav4-and-easv4-series"></a>Eav4 和 Easv4 系列

Eav4 系列和 Easv4 系列採用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 處理器，採用多執行緒配置，具有高達 256MB L3 緩存，增加了運行大多數記憶體優化工作負載的選項。 Eav4 系列和 Easv4 系列與 Ev3 & Esv3 系列具有相同的記憶體和磁片配置。

## <a name="eav4-series"></a>Eav4 系列

ACU： 230 - 260

高級存儲：不支援

高級存儲緩存：不支援

即時移轉：支援

記憶體保留更新：受支援

Eav4 系列尺寸基於 2.35Ghz AMD EPYC<sup>TM</sup> 7452 處理器，可實現 3.35GHz 的提升最大頻率並使用高級 SSD。 Eav4 系列尺寸非常適合記憶體密集型企業應用。 資料磁碟儲存體與虛擬機器分開計費。 要使用高級 SSD，請使用 Easv4 系列尺寸。 Easv4 尺寸的定價和計費表與 Eav3 系列相同。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大 NIC / 預期網路頻寬 （MBps） |
| -----|-----|-----|-----|-----|-----|-----|
| 標準\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| 標準\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| 標準\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| 標準\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| 標準\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| 標準\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| 標準\_E48a\_v4<sup>**</sup> |48|384|1200|32| | |
| 標準\_E64a\_v4<sup>**</sup> |64|512|1600|32| | |
| 標準\_E96a\_v4<sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>這些大小位於預覽版中。 如果您有興趣嘗試這些更大的尺寸，請註冊。 [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)

## <a name="easv4-series"></a>Easv4 系列

ACU： 230 - 260

進階儲存體：支援

高級存儲緩存：支援

即時移轉：支援

記憶體保留更新：受支援

Easv4 系列尺寸基於 2.35Ghz AMD EPYC<sup>TM</sup> 7452 處理器，可實現 3.35GHz 的提升最大頻率並使用高級 SSD。 Easv4 系列尺寸非常適合記憶體密集型企業應用。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC / 預期網路頻寬 （MBps） |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4<sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4<sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4<sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>這些大小位於預覽版中。 如果您有興趣嘗試這些更大的尺寸，請註冊。 [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)

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
