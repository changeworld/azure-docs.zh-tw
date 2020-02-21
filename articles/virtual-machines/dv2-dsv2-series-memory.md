---
title: 記憶體優化 Dv2 和 DSv2 系列 Vm-Azure 虛擬機器
description: Dv2 和 DSv2 系列 Vm 的規格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1ba13733fdf5cf8aa83cbf71a23d013bf4ae90d4
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493783"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>記憶體優化 Dv2 和 Dsv2 系列

Dv2 和 Dsv2 系列是原始 D 系列的後續操作，具備功能更強大的 CPU。 DSv2 系列大小執行于 Intel®的® 8171M 2.1 GHz （Skylake）或 Intel®的® E5-2673 v4 2.3 g h z （Broadwell）或 Intel®的® E5-2673 v3 2.4 GHz （Haswell）處理器上。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

## <a name="dv2-series-11-15"></a>Dv2 系列 11-15

Dv2 系列大小執行于 Intel®的® 8171M 2.1 GHz （Skylake）或 Intel®的® E5-2673 v4 2.3 g h z （Broadwell）或 Intel®的® E5-2673 v3 2.4 GHz （Haswell）處理器上。

ACU：210 - 250

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大資料磁片/輸送量： IOPS | 最大 Nic/預期的網路頻寬（Mbps） |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。
<sup>2</sup> 25000 Mbps (含加速網路)。

## <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

DSv2 系列大小執行于 Intel®的® 8171M 2.1 GHz （Skylake）或 Intel®的® E5-2673 v4 2.3 g h z （Broadwell）或 Intel®的® E5-2673 v3 2.4 GHz （Haswell）處理器上。

ACU：210 - 250 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps （GiB 中的快取大小） | 最大未快取的磁片輸送量： IOPS/MBps | 最大 Nic/預期的網路頻寬（Mbps） |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 （72）    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 （144） | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 （288） | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 （576） | 51200/768 | 8/12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 （720） | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> DSv2 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。  如需詳細資訊，請參閱[為高效能而設計](./windows/premium-storage-performance.md) \(英文\)。
<sup>2</sup>實例會隔離至 Intel Haswell 型硬體，並專屬於單一客戶。  
<sup>3</sup> 可用限制核心大小。  
<sup>4</sup> 25000 Mbps (含加速網路)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。