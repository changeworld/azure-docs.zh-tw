---
title: H 系列-Azure 虛擬機器
description: H 系列 Vm 的規格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/04/2020
ms.author: lahugh
ms.openlocfilehash: 6654506a1e53165ef0891ba0de32a7937c21c904
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164809"
---
# <a name="h-series"></a>H 系列

H 系列 Vm 已針對高 CPU 頻率或每個核心需求的大型記憶體所驅動的應用程式優化。 H 系列 Vm 的功能為8或 16 Intel （r） E5 2667 v3 處理器核心，每個 CPU 核心最多 14 GB RAM，且無超執行緒。 H 系列功能 56 Gb/秒 Mellanox FDR 會在非封鎖的 fat 樹狀結構設定中，使 RDMA 效能一致。 H 系列 Vm 支援 Intel MPI 5.x 和 MS-MPI。

ACU：290 - 300

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（Gb/s） | MPI 支援 | 暫存儲存體（GB） | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel 強 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel 強 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel 強 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel 強 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel 強 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 強 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>針對 MPI 應用程式，FDR 未受的網路會啟用專用的 RDMA 後端網路。

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