---
title: Dv2 和 Dsv2 系列 - Azure 虛擬機器
description: Dv2 和 Dsv2 系列 VM 的規格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164418"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 和 DSv2 系列

Dv2 和 Dsv2 系列是原始 D 系列的後續產品，具有更強大的 CPU 和最佳的 CPU 到記憶體配置，適合大多數生產工作負載。 Dv2 系列比 D 系列快約 35%。 Dv2 系列在英特爾® Xeon ® 8171M 2.1GHz（天湖）、英特爾® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz （哈斯韋爾）處理器上運行，英特爾渦輪增壓技術 2.0。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

## <a name="dv2-series"></a>Dv2 系列

Dv2 系列尺寸在英特爾® Xeon ® 8171M 2.1GHz（Skylake）或英特爾® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz（哈斯韋爾）處理器上運行，英特爾渦輪增壓技術 2.0。

ACU：210 - 250

進階儲存體：不支援

高級存儲緩存：不支援

即時移轉：支援

記憶體保留更新：受支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大臨時存儲輸送量：IOPS/讀取 MBps/寫入 MBps | 最大資料磁碟 | 輸送量：IOPS | 最大 NIC/預期網路頻寬 （Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2 系列

DSv2 系列尺寸在英特爾® Xeon ® 8171M 2.1GHz（Skylake）或英特爾® Xeon® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz （Haswell） 處理器上運行，採用英特爾渦輪增壓技術 2.0 並使用高級存儲。

ACU：210 - 250

進階儲存體：支援

高級存儲緩存：支援

即時移轉：支援

記憶體保留更新：受支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大緩存和臨時存儲輸送量：IOPS/MBps（GiB 中的緩存大小） | 最大未緩存磁片輸送量：IOPS/MBps | 最大 NIC/預期網路頻寬 （Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
