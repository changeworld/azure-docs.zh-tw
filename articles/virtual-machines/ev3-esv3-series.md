---
title: Ev3 系列和 Esv3 系列
description: Ev3 和 Esv3 系列 Vm 的規格。
author: DavidCBerry13
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: daberry
ms.openlocfilehash: 07564955e9cfa6931e461df00ef50a237a66b9b3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500677"
---
# <a name="ev3-and-esv3-series"></a>Ev3 和 Esv3 系列

Ev3 和 Esv3 系列執行于 Intel®) ®白金級 8272CL (Cascade Lake、或 Intel®) ® 8171M 2.1 GHz (Skylake，或 Intel®® E5-2673 v4 2.3 GHz (Broadwell) 處理器在超執行緒設定中，為大部分的一般用途工作負載提供更好的價值主張，並讓 Ev3 與大部分其他雲端的一般用途 Vm 保持一致。  除了記憶體已擴充 (從 7 GiB/vCPU 到 8 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Ev3 承襲了 D/Dv2 系列的高記憶體 VM 大小。

## <a name="ev3-series"></a>Ev3 系列

Ev3 系列實例是在 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) ，或 Intel®® E5-2673 v4 2.3 g h (Broadwell) 處理器，以及功能 Intel Turbo 加速技術2.0。 Ev3 系列執行個體很適合記憶體密集型的企業應用程式。

資料磁碟儲存體與虛擬機器分開計費。 若要使用進階儲存體磁碟，請使用 ESv3 大小。 ESv3 大小的定價及計費方式與 Ev3 系列相同。

Ev3 系列 VM 的功能 Intel® Hyper-Threading 技術。

[ACU](acu.md)： 160-190<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大 NIC / 網路頻寬 |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1、2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> 個可用的有限核心大小。

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

## <a name="esv3-series"></a>Esv3 系列

Esv3 系列實例是在 Intel®) ®白金級 8272CL (串聯 Lake、Intel®® 8171M 2.1 GHz (Skylake) ，或 Intel®® E5-2673 v4 2.3 GHz (Broadwell) 處理器，搭載 Intel Turbo 加速技術2.0 並使用 premium 儲存體。 Esv3 系列實例很適合記憶體密集型企業應用程式。

Esv3 系列 VM 的功能 Intel® Hyper-Threading 技術。

[ACU](acu.md)：160-190<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 高載快取和暫存儲存體輸送量： IOPS/MBps<sup>3</sup> | 最大取消快取的磁碟輸送量：IOPS/MBps |  高載未快取的磁片輸送量： IOPS/MBps<sup>3</sup>| 最大 Nic/預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3                | 2  | 16  | 32  | 4  | 4000/32 (50)        | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)       | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)     | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)     | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)     | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)     | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)    | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600)  | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600)  | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup> 個可用的有限核心大小。

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

<sup>3</sup> Esv3 系列 vm 可以提高磁片效能，並最多可達30分鐘的最高 [負載](./disk-bursting.md) 。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)
- [定價計算機](https://azure.microsoft.com/pricing/calculator/)
- 如需磁片類型的詳細資訊，請參閱 [Azure 中有哪些可用的磁片類型？](disks-types.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。