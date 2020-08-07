---
title: Fsv2 系列
description: Fsv2 系列 Vm 的規格。
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 101aae2ab200d5d78ab617f63c6955dc0547310f
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825645"
---
# <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列會在 Intel®的®白金 8272CL (串聯 Lake) 處理器和 Intel®可®白金級 8168 (Skylake) 處理器上執行。 它的特色是將所有核心 Turbo 主頻的速度維持在 3.4 GHz，而最大單一核心 turbo 頻率為 3.7 g h z。 Intel® AVX-512 指示是 Intel 可擴充的處理器上的新技術。 這些指示可為單一和雙精確度浮點運算上的向量處理工作負載，提供最多2倍的效能提升。 換句話說，對於任何計算工作負載來說，這些都是非常快速的。

Fsv2 系列 Vm 功能 Intel®超執行緒技術。

ACU：195 - 210

進階儲存體：支援

進階儲存體快取：支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： GiB 中的 IOPS/MBps (快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)        | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)        | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)     | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)     | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)     | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)     | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024)  | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1、2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520)  | 80000/1100 | 8|30000 |

<sup>1</sup>使用超過 64 vCPU 時，需要下列其中一種支援的客體作業系統：

- Windows Server 2016 或更新版本
- Ubuntu 16.04 LTS 或更新版本，其中包含 Azure 微調的核心 (4.15 核心或更新版本) 
- SLES 12 SP2 或更新版本
- RHEL 或 CentOS 版本6.7 至6.10，並已安裝 Microsoft 提供的 .LIS 套件 4.3.1 (或更新版本) 
- RHEL 或 CentOS 7.3 版，並已安裝 Microsoft 提供的 .LIS 套件 4.2.1 (或更新版本) 
- RHEL 或 CentOS 7.6 版或更新版本
- 使用 UEK4 或更新版本的 Oracle Linux
- 具有反向移植核心 Debian 10 或更新版本的 Debian 9
- 使用4.14 核心或更新版本的 CoreOS

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

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