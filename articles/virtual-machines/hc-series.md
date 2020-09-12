---
title: HC 系列-Azure 虛擬機器
description: HC 系列 Vm 的規格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: bb76dbf975ce15f72d3ad339853c407de42cf507
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594385"
---
# <a name="hc-series"></a>HC 系列

HC 系列 Vm 已針對密集計算所驅動的應用程式優化，例如隱含的有限元素分析、分子 dynamics 和計算化學。 HC Vm 功能 44 Intel 最強白金級8168處理器核心，每個 CPU 核心 8 GB RAM，且無超執行緒。 Intel 的頂級白金平臺支援 Intel 豐富的軟體工具生態系統，例如 Intel Math Kernel Library 和先進的向量處理功能，例如 AVX-512。

HC 系列 Vm 功能 100 Gb/秒的 Mellanox EDR EDR。 這些 Vm 會連接到非封鎖的 fat 樹狀結構，以獲得優化且一致的 RDMA 效能。 這些 Vm 支援自動調整路由和動態連線傳輸 (DCT，其他標準 RC 和 UD 傳輸) 。 這些功能增強應用程式效能、擴充性和一致性，強烈建議使用這些功能。

ACU：297-315

進階儲存體：支援

進階儲存體快取：支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基礎 CPU 頻率 (GHz)  | 所有核心頻率 (GHz，尖峰)  | 單一核心頻率 (GHz，尖峰)  | RDMA 效能 (Gb/秒)  | MPI 支援 | 暫存儲存體 (GB)  | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel 的頂級白金8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 全部 | 700 | 4 | 1 |

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
- 如需執行 HPC 工作負載的高層級架構視圖，請參閱 [Azure 上的高效能運算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。
- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
