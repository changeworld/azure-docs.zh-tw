---
title: NDv2 系列 - Azure 虛擬機器
description: NDv2 系列 VM 的規格。
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247277"
---
# <a name="updated-ndv2-series"></a>更新的 NDv2 系列

NDv2 系列虛擬機器是 GPU 系列的新新增功能，專為最苛刻的 GPU 加速 AI、機器學習、模擬和 HPC 工作負載而設計。

NDv2 由 8 個 NVIDIA 特斯拉 V100 NVLINK 連接的 GPU 供電，每個 GPU 記憶體為 32 GB。 每個 NDv2 VM 還具有 40 個非超執行緒英特爾至強白金 8168 （天湖）內核和 672 GiB 的系統記憶體。

NDv2 實例利用 CUDA GPU 優化的計算內核，以及支援 GPU 加速的"開箱即用"的許多 AI、ML 和分析工具，如 TensorFlow、Pytorch、Caffe、RAPIDS 等，為 HPC 和 AI 工作負載提供出色的性能框架。

關鍵是，NDv2 專為計算密集擴展（每個 VM 佔用 8 個 GPU）和橫向擴展（利用多個 VM 協同工作）工作負載而構建。 NDv2 系列現在支援 100 千兆英菲尼班 EDR 後端網路，類似于 HB 系列 HPC VM 的後端網路，允許高性能群集用於並行方案，包括 AI 和 ML 的分散式培訓。 此後端網路支援所有主要的 InfiniBand 協定，包括 NVIDIA NCCL2 庫採用的協定，允許無縫群集 GPU。

> 在ND40rs_v2 VM[上啟用 InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband)時，請使用 4.7-1.0.0.1 Mellanox OFED 驅動程式。
>
> 由於 GPU 記憶體增加，新的ND40rs_v2 VM 需要使用[第 2 代 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)和市場映射。 
>
> 請注意：具有 16 GB 每 GPU 記憶體的ND40s_v2不再可用於預覽，並已被更新ND40rs_v2取代。

<br>

進階儲存體：支援

高級存儲緩存：支援

即時移轉：不支援

記憶體保留更新：不支援

英菲尼班：支援

| 大小 | vCPU | 記憶體：GiB | 臨時存儲 （SSD）： GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大網路頻寬 | 最大 NIC |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB （NVLink） | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

要利用 Azure N 系列 VM 的 GPU 功能，必須安裝 NVIDIA GPU 驅動程式。

[NVIDIA GPU 驅動程式擴充功能](./extensions/hpccompute-gpu-linux.md)會在 N 系列 VM 上安裝適當的 NVIDIA CUDA 或 GRID 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需有關虛擬機器擴充功能的一般資訊，請參閱 [Azure 虛擬機器擴充功能和功能](./extensions/overview.md)。

如果選擇手動安裝 NVIDIA GPU 驅動程式，請參閱[Linux 的 N 系列 GPU 驅動程式設置](./linux/n-series-driver-setup.md)。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
