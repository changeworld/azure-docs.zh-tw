---
title: HBv2 系列-Azure 虛擬機器
description: HBv2 系列 Vm 的規格。
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164792"
---
# <a name="hbv2-series"></a>HBv2 系列

HBv2 系列 Vm 已針對記憶體頻寬所驅動的應用程式優化，例如流體 dynamics、有限元素分析和容器模擬。 HBv2 Vm 功能 120 AMD EPYC 7742 處理器核心，每個 CPU 核心 4 GB RAM，且不會同時進行多執行緒處理。 每個 HBv2 VM 最多可提供每秒 340 GB 的記憶體頻寬，以及最多4次 teraflop 的 FP64 計算。

進階儲存體：支援

即時移轉：不支援

記憶體保留更新：不支援

| Size | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基本 CPU 頻率（GHz） | 所有核心頻率（GHz、尖峰） | 單核心頻率（GHz、尖峰） | RDMA 效能（Gb/s） | MPI 支援 | 暫存儲存體（GB） | 最大資料磁碟 | 最大乙太網路 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | 全部 | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。