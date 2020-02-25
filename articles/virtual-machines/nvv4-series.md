---
title: NVv4 系列-Azure 虛擬機器
description: NVv4 系列 Vm 的規格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3b1deceb8d5c6e2a22254b2719449e5adb069def
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565392"
---
# <a name="nvv4-series-preview"></a>NVv4 系列（預覽）

NVv4 系列虛擬機器是由[Amd Radeon 直覺 MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 和 Amd EPYC 7V12 （羅馬） cpu 提供技術支援。 在 NVv4 系列中，Azure 引進了具有部分 Gpu 的虛擬機器。 針對 GPU 加速圖形應用程式和虛擬桌面，從具有2個 GiB 畫面格緩衝區的 GPU 1/第8部，挑選正確大小的虛擬機器，並使用16個 GiB 框架緩衝區的完整 GPU。 NVv4 虛擬機器目前僅支援 Windows 客體作業系統。

[於預覽期間註冊並存取這些機器](https://aka.ms/nvv4signup)。
<br>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup>個 NVv4 系列 VM 功能 AMD 同時多執行緒技術

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

若要利用執行 Windows 的 Azure N 系列 Vm 的 GPU 功能，必須安裝 NVIDIA 或 AMD GPU 驅動程式。

[NVIDIA GPU 驅動程式擴充](/extensions/hpccompute-gpu-windows.md)功能會在 Windows N 系列 VM 上安裝適當的 NVIDIA CUDA 或 GRID 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需支援的作業系統和部署步驟，請參閱 [NVIDIA GPU 驅動程式擴充功能文件](/extensions/hpccompute-gpu-windows.md)。 如需有關虛擬機器擴充功能的一般資訊，請參閱 [Azure 虛擬機器擴充功能和功能](/extensions/overview.md)。

如果您選擇手動安裝 NVIDIA GPU 驅動程式，請參閱適用于[Windows 的 N 系列 GPU 驅動程式設定](/windows/n-series-driver-setup.md)，以瞭解支援的作業系統、驅動程式、安裝和驗證步驟。

若要手動安裝 AMD GPU 驅動程式，請參閱適用于[Windows 的 N 系列 AMD GPU 驅動程式設定](/windows/n-series-amd-driver-setup.md)，以瞭解支援的作業系統、驅動程式、安裝和驗證步驟。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。