---
title: NCv3 系列-Azure 虛擬機器
description: NCv3 系列 Vm 的規格。
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cdd2a1b25eec4f649b062721200349d932fb7599
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273912"
---
# <a name="ncv3-series"></a>NCv3 系列

NCv3 系列 VM 是由 [NVIDIA Tesla V100](https://www.nvidia.com/data-center/tesla-v100/) GPU 提供技術支援。 這些 GPU 可提供 NCv2 系列 1.5 倍的計算效能。 客戶可針對儲槽模型、DNA 定序、蛋白質分析、蒙地卡羅模擬等傳統 HPC 工作負載，善用這些更新過的 GPU。 NC24rs v3 組態提供低延遲且高輸送量網路介面，最適合用於緊密結合的平行計算工作負載。 除了 Gpu 以外，NCv3 系列 Vm 也是由 Intel 強式 E5-2690 v4 （Broadwell） Cpu 提供技術支援。

進階儲存體：支援

進階儲存體快取：支援

即時移轉：不支援

記憶體保留更新：不支援

> [!IMPORTANT]
> 針對此 VM 系列，您訂用帳戶中的 vCPU （核心）配額一開始會在每個區域中設定為0。 在[可用區域](https://azure.microsoft.com/regions/services/)中，為此系列[要求增加 vCPU 配額](../azure-supportability/resource-manager-core-quotas-request.md)。
>
| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大未快取的磁片輸送量： IOPS/MBps | 最大 NIC |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = 一張 V100 卡。

\* 支援 RDMA 的 [!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

若要利用 Azure N 系列 Vm 的 GPU 功能，必須安裝 NVIDIA GPU 驅動程式。

[NVIDIA GPU 驅動程式擴充功能](./extensions/hpccompute-gpu-windows.md)會在 N 系列 VM 上安裝適當的 NVIDIA CUDA 或 GRID 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需支援的作業系統和部署步驟，請參閱 [NVIDIA GPU 驅動程式擴充功能文件](./extensions/hpccompute-gpu-windows.md)。 如需有關虛擬機器擴充功能的一般資訊，請參閱 [Azure 虛擬機器擴充功能和功能](./extensions/overview.md)。

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
