---
title: NVv4 系列 - Azure 虛擬機器
description: NVv4 系列 VM 的規格。
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273851"
---
# <a name="nvv4-series"></a>NVv4 系列 

NVv4 系列虛擬機器由[AMD Radeon 本能 MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 和 AMD EPYC 7V12（羅馬）CPU 提供支援。 借助 NVv4 系列 Azure，Azure 正在引入具有部分 GPU 的虛擬機器。 選擇適合 GPU 加速圖形應用程式和虛擬桌面的合適大小的虛擬機器，從 GPU 的 1/8 開始，使用 2 個 GiB 框架緩衝區，到具有 16 個 GiB 框架緩衝區的完整 GPU。 NVv4 虛擬機器目前僅支援 Windows 客體作業系統。

<br>

進階儲存體：支援

高級存儲緩存：支援

即時移轉：不支援

記憶體保留更新：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4 系列 VM 採用 AMD 同步多執行緒技術

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

要利用運行 Windows 的 Azure NVv4 系列 VM 的 GPU 功能，必須安裝 AMD GPU 驅動程式。

要手動安裝 AMD GPU 驅動程式，請參閱[適用于 Windows 的 N 系列 AMD GPU 驅動程式設置](./windows/n-series-amd-driver-setup.md)，瞭解支援的作業系統、驅動程式、安裝和驗證步驟。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
