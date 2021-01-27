---
title: H 系列-Azure 虛擬機器
description: H 系列 Vm 的規格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 8ba3311a67098ac04cc62161935b067e465fed57
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920132"
---
# <a name="h-series"></a>H 系列

H 系列 Vm 已針對依核心需求高 CPU 頻率或大型記憶體驅動的應用程式進行優化。 H 系列 Vm 的特色為8或 16 Intel 可高度 E5 2667 v3 處理器核心，每個 CPU 核心最多 14 GB RAM，且無超執行緒。 H 系列功能 56 Gb/秒的 Mellanox FDR 可在非封鎖的 fat 樹狀結構設定中使用，以達到一致的 RDMA 效能。 H 系列 Vm 目前尚未啟用 SR-IOV，並支援 Intel MPI 5.x 和 MS-CHAP。

[ACU](acu.md)：290-300<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
[加速網路](../virtual-network/create-vm-accelerated-networking-cli.md)：不支援<br>
<br>

| 大小 | vCPU | 處理器 | 記憶體 (GiB) | 記憶體頻寬 GB/秒 | 基礎 CPU 頻率 (GHz)  | 所有核心頻率 (GHz，尖峰)  | 單一核心頻率 (GHz，尖峰)  | RDMA 效能 (Gb/秒)  | MPI 支援 | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大磁碟輸送量︰IOPS | 最大 Ethernet Vnic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel 至 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-CHAP | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel 至 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-CHAP | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel 至 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-CHAP | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel 至 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-CHAP | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel 至 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-CHAP | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 至 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-CHAP | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> 適用于 MPI 應用程式，FDR 的「不會」網路會啟用專用 RDMA 後端網路。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> 在 [支援 RDMA 的 vm](sizes-hpc.md#rdma-capable-instances)中，H 系列不會啟用 sr-iov。 因此，支援的 [VM 映射](./workloads/hpc/configure.md#vm-images)、可 [駕駛的驅動程式](./workloads/hpc/enable-infiniband.md) 需求和支援的 [MPI 程式庫](./workloads/hpc/setup-mpi.md) ，與啟用 sr-iov 的 vm 不同。

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
