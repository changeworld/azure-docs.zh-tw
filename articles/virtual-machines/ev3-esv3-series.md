---
title: Ev3 系列和 Esv3 系列 - Azure 虛擬機器
description: Ev3 和 Esv3 系列 VM 的規格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164367"
---
# <a name="ev3-and-esv3-series"></a>Ev3 和 Esv3 系列

Ev3 和 Esv3 系列採用英特爾® Xeon ® 8171M 2.1 GHz（天湖）或英特爾® Xeon® E5-2673 v4 2.3 GHz （Broadwell） 處理器，具有超執行緒配置，為大多數通用工作負載提供了更好的價值主張，並帶來了 Ev3與大多數其他雲的通用 VM 保持一致。  除了記憶體已擴充 (從 7 GiB/vCPU 到 8 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Ev3 承襲了 D/Dv2 系列的高記憶體 VM 大小。

## <a name="ev3-series"></a>Ev3 系列

Ev3 系列實例基於英特爾® Xeon ® 8171M 2.1 GHz（天湖）或英特爾®至強® E5-2673 v4 2.3 GHz（布羅德韋爾）處理器和英特爾渦輪增壓技術 2.0 的功能。 Ev3 系列執行個體很適合記憶體密集型的企業應用程式。

資料磁碟儲存體與虛擬機器分開計費。 若要使用進階儲存體磁碟，請使用 ESv3 大小。 ESv3 大小的定價及計費方式與 Ev3 系列相同。

Ev3 系列 VM 的功能英特爾®超執行緒技術。

ACU：160 - 190

進階儲存體：不支援

高級存儲緩存：不支援

即時移轉：支援

記憶體保留更新：受支援

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
| Standard_E64i_v3 <sup>1，2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup>受限型核心尺寸可用。

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

## <a name="esv3-series"></a>Esv3 系列

Esv3 系列實例基於英特爾® Xeon ® 8171M 2.1 GHz（天湖）或英特爾® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）處理器、英特爾渦輪增壓技術 2.0 以及使用高級存儲的功能。 Esv3 系列實例是記憶體密集型企業應用程式的理想選擇。

Esv3 系列 VM 的功能英特爾®超執行緒技術。

ACU：160-190

進階儲存體：支援

高級存儲緩存：支援

即時移轉：支援

記憶體保留更新：受支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大緩存和臨時存儲輸送量：IOPS/MBps（GiB 中的緩存大小） | 最大未緩存磁片輸送量：IOPS/MBps | 最大 NIC/預期網路頻寬 （Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup>受限型核心尺寸可用。

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
