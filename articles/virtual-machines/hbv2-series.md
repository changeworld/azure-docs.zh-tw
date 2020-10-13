---
title: HBv2 系列-Azure 虛擬機器
description: HBv2 系列 Vm 的規格。
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e5d7842f465ae335630819de283881d40ce2a2cd
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929620"
---
# <a name="hbv2-series"></a>HBv2 系列

HBv2 系列 Vm 最適用于以記憶體頻寬驅動的應用程式，例如流體動態、有限元素分析和容器模擬。 HBv2 Vm 功能 120 AMD EPYC 7742 處理器核心，每個 CPU 核心 4 GB RAM，且不會同時進行多執行緒處理。 每個 HBv2 VM 最多可提供每秒 340 GB 的記憶體頻寬，以及最多 4 teraFLOPS 的 FP64 計算。

HBv2 系列 Vm 功能 200 Gb/秒 Mellanox HDR 的空間。 這些 Vm 會連接到非封鎖的 fat 樹狀結構，以獲得優化且一致的 RDMA 效能。 這些 Vm 支援自動調整路由和動態連線傳輸 (DCT，其他標準 RC 和 UD 傳輸) 。 這些功能增強應用程式效能、擴充性和一致性，強烈建議使用這些功能。

[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 處理器 | 記憶體 (GiB) | 記憶體頻寬 GB/秒 | 基礎 CPU 頻率 (GHz)  | 所有核心頻率 (GHz，尖峰)  | 單一核心頻率 (GHz，尖峰)  | RDMA 效能 (Gb/秒)  | MPI 支援 | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大 Ethernet Vnic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | 全部 | 480 + 960 | 8 | 8 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何設定 [您的 vm](./workloads/hpc/configure.md)、 [啟用](./workloads/hpc/enable-infiniband.md)可充分運用、 [設定 MPI](./workloads/hpc/setup-mpi.md) ，以及將 Azure 的 Hpc 應用程式優化至 [hpc 工作負載](./workloads/hpc/overview.md)。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
