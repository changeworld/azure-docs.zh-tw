---
title: NDv2 系列-Azure 虛擬機器
description: NDv2 系列 Vm 的規格。
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: c4f2a8deed256af54ffd4331210fdfc89b0f4e49
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161936"
---
# <a name="updated-ndv2-series-preview"></a>已更新 NDv2 系列（預覽）

NDv2 系列虛擬機器是 GPU 系列的新新增元件，專為需求最高的 GPU 加速 AI、機器學習、模擬和 HPC 工作負載所設計。

NDv2 是由8個 NVIDIA Tesla V100 NVLINK 連接的 Gpu 所提供，每個都有 32 GB 的 GPU 記憶體。 每個 NDv2 VM 也都有40非超執行緒 Intel Skylake 的白金8168（）核心和 672 GiB 的系統記憶體。

NDv2 實例可為 HPC 和 AI 工作負載提供絕佳的效能，利用 CUDA 的 GPU 優化計算核心，以及許多支援 GPU 加速「現成可用」的 AI、ML 和分析工具，例如 TensorFlow、Pytorch、Caffe、RAPIDS 和其他集成.

嚴重來說，NDv2 是針對計算密集相應增加（每個 VM 的8個 Gpu）和向外延展（利用多個 Vm 一起運作）工作負載而建立的。 NDv2 系列現在支援 100-Gigabit 未處理的 EDR 後端網路，類似于 HB 系列的 HPC VM 上所提供的資料，以允許平行案例的高效能叢集，包括 AI 和 ML 的分散式訓練。 這個後端網路支援所有主要的未使用通訊協定，包括 NVIDIA 的 NCCL2 程式庫所採用的，讓 Gpu 能夠順暢地叢集化。


> [!NOTE]
> 在 ND40rs_v2 的 VM 上[啟用](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband)「未使用」時，請使用 4.7-1.0.0.1 Mellanox OFED 驅動程式。
>
> 由於 GPU 記憶體增加，新的 ND40rs_v2 VM 需要使用[第2代 vm](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)和 marketplace 映射。 
>
> [註冊以要求搶先存取 NDv2 虛擬機器預覽版。](https://aka.ms/AzureNDrv2Preview)
>
> 請注意：每個 GPU 記憶體有 16 GB 的 ND40s_v2 已不再提供預覽，並已由更新過的 ND40rs_v2 取代。

<br>

進階儲存體：支援

進階儲存體快取：支援

即時移轉：不支援

記憶體保留更新：不支援

不適用：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體（SSD）： GiB | GPU | GPU 記憶體： GiB | 最大資料磁碟 | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大網路頻寬 | 最大 NIC |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB （NVLink） | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

若要利用 Azure N 系列 Vm 的 GPU 功能，必須安裝 NVIDIA GPU 驅動程式。

[NVIDIA GPU 驅動程式擴充功能](./extensions/hpccompute-gpu-windows.md)會在 N 系列 VM 上安裝適當的 NVIDIA CUDA 或 GRID 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需支援的作業系統和部署步驟，請參閱 [NVIDIA GPU 驅動程式擴充功能文件](./extensions/hpccompute-gpu-windows.md)。 如需有關虛擬機器擴充功能的一般資訊，請參閱 [Azure 虛擬機器擴充功能和功能](/.extensions/overview.md)。

如果您選擇手動安裝 NVIDIA GPU 驅動程式，請參閱適用于[Windows 的 n 系列 gpu 驅動程式設定](./windows/n-series-driver-setup.md)或[Linux 的 n 系列 gpu 驅動](./linux/n-series-driver-setup.md)程式設定，以瞭解支援的作業系統、驅動程式、安裝和驗證步驟。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
