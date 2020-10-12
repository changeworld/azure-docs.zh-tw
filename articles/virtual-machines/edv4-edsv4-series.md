---
title: Edv4 和 Edsv4 系列
description: Ev4、Edv4、Esv4 與 Edsv4 系列 VM 的規格。
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4c69f78ababedde30e9cb41613b8e9537c738b5f
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945629"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 和 Edsv4 系列

Edv4 與 Edsv4 系列可在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 處理器上在超執行緒設定下執行，而且適用於各種記憶體密集型企業應用程式，且具備最高可達 504 GiB 的 RAM、[Intel&reg; 渦輪增壓技術 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和 [Intel&reg; 先進向量延伸模組 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 它們也支援 [Intel &reg; 深度學習提升](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)。 這些新的 VM 大小會有50% 的本機儲存體，以及與[Gen2 vm](./linux/generation-2.md)的[Ev3/Esv3](./ev3-esv3-series.md)大小相比，可進行讀取和寫入的較佳本機磁片 IOPS。 它具備 3.4 GHz 的所有核心 Turbo 頻率速度。 

## <a name="edv4-series"></a>Edv4 系列

Edv4 系列大小是在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 處理器上執行。 除了快速和大型本機 SSD 儲存體 (最多 2400 GiB) 之外，Edv4 虛擬機器大小具備最多可達 504 GiB 的 RAM。 這些虛擬機器非常適合記憶體密集型的企業應用程式，以及可受益於低延遲、高速本機儲存體的應用程式。 您可以將標準 SSD 與標準 HDD 磁碟儲存體連結至 Edv4 VM。 

[ACU](acu.md)： 195-210<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


## <a name="edsv4-series"></a>Edsv4-series

Edsv4 系列大小是在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 處理器上執行。 除了快速和大型本機 SSD 儲存體 (最多 2400 GiB) 之外，Edsv4 虛擬機器大小具備最多可達 504 GiB 的 RAM。 這些虛擬機器非常適合記憶體密集型的企業應用程式，以及可受益於低延遲、高速本機儲存體的應用程式。

[ACU](acu.md)：195-210<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [可用限制核心大小)](./constrained-vcpu.md) \(部分機器翻譯\)。


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
