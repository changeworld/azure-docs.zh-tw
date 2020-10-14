---
title: Ev4 和 Esv4 系列-Azure 虛擬機器
description: Ev4 和 Esv4 系列 Vm 的規格。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: b8f6768ebf988ff568739c86303b52ea5b0a9234
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018707"
---
# <a name="ev4-and-esv4-series"></a>Ev4 和 Esv4 系列

Ev4 和 Esv4 系列是在 Intel 8272CL 白金級 &reg; &reg; (在超執行緒設定中的串聯式 Lake) 處理器上執行，適用于各種需要大量記憶體的企業應用程式和功能，最高可達504GIB 的 RAM。 它具備 3.4 GHz 的所有核心 Turbo 頻率速度。

> [!NOTE]
> 關於常見問題，請參閱  [沒有本機暫存磁片的 AZURE VM 大小](azure-vms-no-temp-disk.md)。

## <a name="ev4-series"></a>Ev4 系列

Ev4 系列大小會在 Intel &reg; 8272CL (Cascade Lake) 上執行。 Ev4 系列實例很適合記憶體密集型企業應用程式。 Ev4 系列 Vm 具備 Intel &reg; Hyper-Threading 技術的特色。

遠端資料磁碟儲存體與虛擬機器分開計費。 若要使用 premium 儲存體磁片，請使用 Esv4 大小。 Esv4 大小的定價和計費方式與 Ev4 系列相同。

[ACU](acu.md)： 195-210<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | 僅限遠端存放 | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | 僅限遠端存放 | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | 僅限遠端存放 | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | 僅限遠端存放 | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | 僅限遠端存放 | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | 僅限遠端存放 | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | 僅限遠端存放 | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | 僅限遠端存放 | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4 系列

Esv4 系列大小會在 Intel &reg; &reg; 8272CL (Cascade Lake) 上執行。 Esv4 系列實例很適合記憶體密集型企業應用程式。 Evs4 系列 Vm 具備 Intel &reg; Hyper-Threading 技術的特色。 遠端資料磁碟儲存體與虛擬機器分開計費。

[ACU](acu.md)：195-210<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | 僅限遠端存放 | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | 僅限遠端存放 | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | 僅限遠端存放 | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | 僅限遠端存放 | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | 僅限遠端存放 | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | 僅限遠端存放 | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | 僅限遠端存放 | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| 僅限遠端存放 | 32 | 80000/1200 | 8|30000 |

<sup>1</sup>個 [可用的有限核心大小](./constrained-vcpu.md)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

定價計算機： [定價計算機](https://azure.microsoft.com/pricing/calculator/)

磁片類型的詳細資訊： [磁片類型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
