---
title: HB 系列
description: HB 系列 Vm 的規格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 3e79373da3de2750a47e68a6d8f10c3ffb9c38de
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652970"
---
# <a name="hb-series"></a>HB 系列

HB 系列 Vm 最適用于以記憶體頻寬驅動的應用程式，例如流體動態、明確有限元素分析和氣象模型。 HB Vm 功能 60 AMD EPYC 7551 處理器核心，每個 CPU 核心 4 GB RAM，且不會同時進行多執行緒處理。 HB VM 提供高達每秒 260 GB 的記憶體頻寬。

HB 系列 Vm 功能 100 Gb/秒的 Mellanox EDR EDR。 這些 Vm 會連接到非封鎖的 fat 樹狀結構，以獲得優化且一致的 RDMA 效能。 這些 Vm 支援自動調整路由和動態連線傳輸 (DCT，其他標準 RC 和 UD 傳輸) 。 這些功能增強應用程式效能、擴充性和一致性，強烈建議使用這些功能。

[ACU](acu.md)：199-216<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>

| 大小 | vCPU | 處理器 | 記憶體 (GiB) | 記憶體頻寬 GB/秒 | 基礎 CPU 頻率 (GHz)  | 所有核心頻率 (GHz，尖峰)  | 單一核心頻率 (GHz，尖峰)  | RDMA 效能 (Gb/秒)  | MPI 支援 | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 全部 | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟
- 深入瞭解如何設定 [您的 vm](./workloads/hpc/configure.md)、 [啟用](./workloads/hpc/enable-infiniband.md)可充分運用、 [設定 MPI](./workloads/hpc/setup-mpi.md)，以及將 Azure 的 Hpc 應用程式優化（ [hpc 工作負載](./workloads/hpc/overview.md)）。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
