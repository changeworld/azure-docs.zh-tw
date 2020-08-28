---
title: Ev4 和 Esv4 系列-Azure 虛擬機器
description: Ev4 和 Esv4 系列 Vm 的規格。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 6e35e32c92535a408c8df22d7306895150a59519
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050143"
---
# <a name="ev4-and-esv4-series"></a>Ev4 和 Esv4 系列

Ev4 和 Esv4 系列是在 Intel 8272CL 白金級 &reg; &reg; (在超執行緒設定中的串聯式 Lake) 處理器上執行，適用于各種需要大量記憶體的企業應用程式和功能，最高可達504GIB 的 RAM。 它有一種持續的核心 Turbo 頻率速度 3.4 GHz。

> [!NOTE]
> 關於常見問題，請參閱  [沒有本機暫存磁片的 AZURE VM 大小](azure-vms-no-temp-disk.md)。

## <a name="ev4-series"></a>Ev4 系列

Ev4 系列大小會在 Intel &reg; 8272CL (Cascade Lake) 上執行。 Ev4 系列實例很適合記憶體密集型企業應用程式。 Ev4 系列 Vm 功能 Intel &reg; 超執行緒技術。

遠端資料磁碟儲存體與虛擬機器分開計費。 若要使用 premium 儲存體磁片，請使用 Esv4 大小。 Esv4 大小的定價和計費方式與 Ev4 系列相同。

> [!IMPORTANT]
> 這些新的大小目前僅處於公開預覽狀態。 您可以在 [這裡](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)註冊這些 Ev4 和 Esv4 系列。 

ACU：195 - 210

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | 僅限遠端存放 | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | 僅限遠端存放 | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | 僅限遠端存放 | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | 僅限遠端存放 | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | 僅限遠端存放 | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | 僅限遠端存放 | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | 僅限遠端存放 | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | 僅限遠端存放 | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4 系列

Esv4 系列大小會在 Intel &reg; &reg; 8272CL (Cascade Lake) 上執行。 Esv4 系列實例很適合記憶體密集型企業應用程式。 Evs4 系列 Vm 功能 Intel &reg; 超執行緒技術。 遠端資料磁碟儲存體與虛擬機器分開計費。

> [!IMPORTANT]
> 這些新的大小目前僅處於公開預覽狀態。 您可以在 [這裡](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)註冊這些 Ev4 和 Esv4 系列。 

ACU：195-210

進階儲存體：支援

進階儲存體快取：支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | 僅限遠端存放 | 4 | 19000/120 (50)  | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | 僅限遠端存放 | 8 | 38500/242 (100)  | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | 僅限遠端存放 | 16 | 77000/485 (200)  | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | 僅限遠端存放 | 32 | 154000/968 (400)  | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | 僅限遠端存放 | 32 | 193000/1211 (500)  | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | 僅限遠端存放 | 32 | 308000/1936 (800)  | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | 僅限遠端存放 | 32 | 462000/2904 (1200)  | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| 僅限遠端存放 | 32 | 615000/3872 (1600)  | 80000/1200 | 8|30000 |

<sup>1</sup>個 [可用的有限核心大小](./constrained-vcpu.md)。

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
