---
title: Mv2 系列-Azure 虛擬機器
description: Mv2 系列 Vm 的規格。
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 7df8dd439008258ea1b4986054660fb0fb9070ce
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204180"
---
# <a name="mv2-series"></a>Mv2 系列

Mv2 系列具有高輸送量、低延遲的平臺，其執行于超執行緒 Intel®®的 8180M 2.5 GHz （Skylake）處理器，其核心基本頻率為 2.5 GHz，最大為 3.8 GHz。 所有 Mv2 系列的虛擬機器大小都可以同時使用標準和 premium 持續性磁片。 Mv2 系列實例是記憶體優化的 VM 大小，可提供無與倫比的計算效能，以支援大型記憶體內部資料庫和工作負載，並具有高記憶體對 CPU 比例，適用于關係資料庫伺服器、大型快取和記憶體內部分析。

Mv2 系列 VM 的功能 Intel®超執行緒技術

進階儲存體：支援

進階儲存體快取：支援

即時移轉：不支援

記憶體保留更新：不支援

寫入加速器：[支援](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Size | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 （7040） | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 （7040） | 40000/1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 （14080） | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 （14080） | 80000/2000 | 8 / 32000 |

<sup>1</sup> Mv2 系列 vm 僅限第2代，並支援第2代支援映射的子集。 如需 Mv2 系列所支援映射的完整清單，請參閱下文。 如果您使用 Linux，請參閱[Azure 上的第2代 Vm 支援](./linux/generation-2.md)，以取得如何尋找和選取映射的指示。 如果您使用的是 Windows，請參閱[Azure 上第2代 vm 的支援](./windows/generation-2.md)，以取得如何尋找及選取映射的指示。 

- Windows Server 2019 或更新版本
- SUSE Linux Enterprise Server 12 SP4 和更新版本，或 SUSE Linux Enterprise Server 15 SP1 和更新版本
- Red Hat Enterprise Linux 7.6、7.7、8.1 或更新版本 
- Oracle Enterprise Linux 7.7 或更新版本



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
