---
title: HB 系列 - Azure 虛擬機器
description: HB 系列 VM 的規格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164826"
---
# <a name="hb-series"></a>HB 系列

HB 系列 VM 針對由記憶體頻寬驅動的應用進行了優化，例如流體動力學、顯式有限元分析和天氣建模。 HB VM 具有 60 個 AMD EPYC 7551 處理器內核，每個 CPU 內核 4 GB 的 RAM，並且沒有同時進行多執行緒。 HB VM 提供高達 260 GB/秒的記憶體頻寬。

ACU： 199-216

進階儲存體：支援

進階儲存體快取：支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/s | 基本 CPU 頻率 （GHz） | 全核頻率（GHz，峰值） | 單核頻率（GHz，峰值） | RDMA 性能（Gb/s） | MPI 支援 | 臨時存儲 （GB） | 最大資料磁碟 | 最大乙太網 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 全部 | 700 | 4 | 1 |

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