---
title: Fsv2 系列 - Azure 虛擬機器
description: Fsv2 系列 VM 的規格。
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164843"
---
# <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列基於英特爾® Xeon ®白金 8168 處理器。 它具有持續全芯渦輪時脈速度為 3.4 GHz 和最大單核渦輪頻率為 3.7 GHz。 英特爾® AVX-512 指令是英特爾可擴展處理器上的新指令。 這些指令在單精確度和雙精度浮點操作上為向量處理工作負載提供高達 2 倍的性能提升。 換句話說，對於任何計算工作負載來說，它們都非常快。

Fsv2 系列 VM 採用英特爾®超執行緒技術。

ACU：195 - 210

進階儲存體：支援

高級存儲緩存：支援

即時移轉：支援

記憶體保留更新：受支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大緩存和臨時存儲輸送量：IOPS/MBps（GiB 中的緩存大小） | 最大未緩存磁片輸送量：IOPS/MBps | 最大 NIC/預期網路頻寬 （Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1， 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup>使用超過 64 個 vCPU 需要以下支援的客體作業系統之一：

- Windows 伺服器 2016 或更高版本
- Ubuntu 16.04 LTS 或更高版本，使用 Azure 調諧內核（4.15 內核或更高版本）
- SLES 12 SP2 或更高版本
- RHEL 或 CentOS 版本 6.7 到 6.10，安裝了 Microsoft 提供的 LIS 套裝軟體 4.3.1（或更高版本）
- RHEL 或 CentOS 版本 7.3，安裝了 Microsoft 提供的 LIS 套裝軟體 4.2.1（或更高版本）
- RHEL 或 CentOS 版本 7.6 或更高版本
- 帶有 UEK4 或更高版本的 Oracle Linux
- Debian 9 與後埠內核， Debian 10 或更高版本
- 具有 4.14 個或更高版本的 CoreOS

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
