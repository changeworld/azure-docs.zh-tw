---
title: Ev3 系列和 Esv3 系列-Azure 虛擬機器
description: Ev3 和 Esv3 系列 Vm 的規格。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164367"
---
# <a name="ev3-and-esv3-series"></a>Ev3 和 Esv3 系列

Ev3 and Esv3 系列的功能是 Intel®的® 8171M 2.1 GHz （Skylake）或 Intel®的® E5-2673 v4 2.3 g h z （Broadwell）處理器，在超執行緒設定中，為大部分的一般用途工作負載提供更好的價值主張，並讓 Ev3 與大多數其他雲端的一般用途 Vm 保持一致。  除了記憶體已擴充 (從 7 GiB/vCPU 到 8 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Ev3 承襲了 D/Dv2 系列的高記憶體 VM 大小。

## <a name="ev3-series"></a>Ev3 系列

Ev3 系列實例是以 Intel®® 8171M 2.1 GHz （Skylake）或 Intel®的® E5-2673 v4 2.3 g h z （Broadwell）處理器和 Intel Turbo 加速技術2.0 的功能為基礎。 Ev3 系列執行個體很適合記憶體密集型的企業應用程式。

資料磁碟儲存體與虛擬機器分開計費。 若要使用進階儲存體磁碟，請使用 ESv3 大小。 ESv3 大小的定價及計費方式與 Ev3 系列相同。

Ev3 系列 VM 的功能 Intel®超執行緒技術。

ACU：160 - 190

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：支援

記憶體保留更新：支援

| Size | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大 NIC / 網路頻寬 |
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

<sup>1</sup>可用的限制核心大小。

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

## <a name="esv3-series"></a>Esv3 系列

Esv3 系列實例是以 Intel®® 8171M 2.1 GHz （Skylake）或 Intel®的® E5-2673 v4 2.3 g h z （Broadwell）處理器、Intel Turbo 加速技術2.0 和使用 premium 儲存體的功能為基礎。 Esv3 系列實例很適合記憶體密集型的企業應用程式。

Esv3 系列 VM 的功能 Intel®超執行緒技術。

ACU：160-190

進階儲存體：支援

進階儲存體快取：支援

即時移轉：支援

記憶體保留更新：支援

| Size | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps （GiB 中的快取大小） | 最大未快取的磁片輸送量： IOPS/MBps | 最大 Nic/預期的網路頻寬（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 （50） | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 （100）   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 （200） | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 （400） | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 （400） | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 （800）    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 （1200）   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 （1600） | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 （1600） | 80000/1200 | 8/30000 |

<sup>1</sup>可用的限制核心大小。

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

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
