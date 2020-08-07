---
title: Ddv4 和 Ddsv4 系列
description: Dv4、Ddv4、Dsv4 與 Ddsv4 系列 VM 的規格。
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: f6d03b289dce3987d6b939fdaf545d6206766e02
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852866"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 和 Ddsv4 系列

Ddv4 與 Ddsv4 系列在採用超執行緒設定的 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 處理器上執行，可為大部分一般用途的工作負載提供更好的價值主張。 具備全核心滿載可維持 3.4 GHz 的渦輪時脈速度、[Intel&reg; 渦輪加速技術 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)與 [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 它們也支援[Intel &reg; 深度學習提升](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)。 相較於 [Dv3/Dsv3](./dv3-dsv3-series.md) 大小搭配使用[第 2 代 VM](./linux/generation-2.md)，這些新 VM 大小的本機儲存體加大 50%，而且讀取和寫入時的本機磁碟 IOPS 更佳。

D 系列使用案例包括企業級應用程式、關聯式資料庫、記憶體內部快取及分析。

## <a name="ddv4-series"></a>Ddv4 系列

Ddv4 系列大小是在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上執行。 Ddv4 系列提供一系列適用於大多數生產工作負載的 vCPU、記憶體與暫存磁碟組合。

新的 Ddv4 VM 大小包括快速、更大的本機 SSD 儲存體 (最多 2,400 GiB)，專為需要快速讀取/寫入暫存儲存體，或需要暫存儲存體以用於快取和暫存檔案等等，可受益於低延遲、高速本機儲存體的應用程式而設計。 您可以將標準 SSD 與標準 HDD 儲存體連結至 Ddv4 VM。 遠端資料磁碟儲存體與虛擬機器分開計費。

ACU：195-210

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

## <a name="ddsv4-series"></a>Ddsv4 系列

Ddv4 系列是在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上執行。 Ddsv4 系列提供一系列適用於大多數生產工作負載的 vCPU、記憶體與暫存磁碟組合。

新的 Ddsv4 VM 大小包括快速、更大的本機 SSD 儲存體 (最多 2,400 GiB)，專為需要快速讀取/寫入暫存儲存體，或需要暫存儲存體以用於快取和暫存檔案等等，可受益於低延遲、高速本機儲存體的應用程式而設計。 

 > [!NOTE]
 >Ddsv4 大小的定價和計費方式與 Ddv4 系列相同。

ACU：195-210

進階儲存體：支援

進階儲存體快取：支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： GiB 中的 IOPS/MBps (快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

定價計算機：[定價計算機](https://azure.microsoft.com/pricing/calculator/)

磁片類型的詳細資訊：[磁片類型](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
