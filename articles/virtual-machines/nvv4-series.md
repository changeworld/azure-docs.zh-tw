---
title: NVv4 系列
description: NVv4 系列 Vm 的規格。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: bff754211cb1a8bf9336a9ada4db2c8189d13a21
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919675"
---
# <a name="nvv4-series"></a>NVv4 系列 

NVv4 系列虛擬機器是由 [Amd Radeon 直覺 MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 和 Amd EPYC 7V12 (羅馬) cpu 提供技術支援。 使用 NVv4 系列 Azure 時，會介紹具有部分 Gpu 的虛擬機器。 針對 GPU 加速圖形應用程式和虛擬桌面，從 GPU 1/8 開始挑選適用于 gpu 加速圖形應用程式和虛擬桌面的虛擬機器大小，並以2個 GiB 框架緩衝區至具有 16 GiB 框架緩衝區的完整 GPU。 NVv4 虛擬機器目前僅支援 Windows 客體作業系統。

<br>

[ACU](acu.md)：230-260<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
[加速網路](../virtual-network/create-vm-accelerated-networking-cli.md)：支援<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC/預期的網路頻寬 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> 個 NVv4 系列 VM 功能 AMD 同時多執行緒技術

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

若要利用執行 Windows 的 Azure NVv4 系列 Vm 的 GPU 功能，必須安裝 AMD GPU 驅動程式。

若要手動安裝 AMD GPU 驅動程式，請參閱適用于 [Windows 的 N 系列 AMD GPU 驅動程式設定](./windows/n-series-amd-driver-setup.md) ，以取得支援的作業系統、驅動程式、安裝和驗證步驟。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
