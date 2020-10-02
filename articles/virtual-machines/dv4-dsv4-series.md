---
title: Dv4 和 Dsv4 系列-Azure 虛擬機器
description: Dv4 和 Dsv4 系列 Vm 的規格。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 59c00b06f1fef613b00ade13c7cacc0b88e61bf1
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649541"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 和 Dsv4 系列

Dv4 和 Dsv4 系列是在 Intel 8272CL &reg; &reg; 白金級 (串聯 Lake) 處理器上的超執行緒設定中執行，為大部分的一般用途工作負載提供更好的價值主張。 它有一種持續的核心 Turbo 頻率速度 3.4 GHz。 

> [!NOTE]
> 關於常見問題，請參閱  [沒有本機暫存磁片的 AZURE VM 大小](azure-vms-no-temp-disk.md)。
## <a name="dv4-series"></a>Dv4 系列

Dv4 系列大小會在 Intel &reg; &reg; 8272CL (Cascade Lake) 上執行。 Dv4 系列大小提供 vCPU、記憶體和遠端儲存體選項的組合，適用于大多數生產工作負載。 Dv4 系列 Vm 功能 [Intel &reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。

遠端資料磁碟儲存體與虛擬機器分開計費。 若要使用 premium 儲存體磁片，請使用 Dsv4 大小。 Dsv4 大小的定價和計費方式與 Dv4 系列相同。

[ACU](acu.md)：195-210<br>
[進階儲存體](premium-storage-performance.md)：不支援<br>
[進階儲存體](premium-storage-performance.md)快取：不支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | 僅限遠端存放 | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | 僅限遠端存放 | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | 僅限遠端存放 | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | 僅限遠端存放 | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | 僅限遠端存放 | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | 僅限遠端存放 | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | 僅限遠端存放 | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4 系列

Dsv4 系列大小會在 Intel &reg; &reg; 8272CL (Cascade Lake) 上執行。 Dv4 系列大小提供 vCPU、記憶體和遠端儲存體選項的組合，適用于大多數生產工作負載。 Dsv4 系列 Vm 功能 [Intel &reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。 遠端資料磁碟儲存體與虛擬機器分開計費。

[ACU](acu.md)：195-210<br>
[進階儲存體](premium-storage-performance.md)：支援<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：支援<br>
[記憶體保留更新](maintenance-and-updates.md)：支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>
<br>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | 僅限遠端存放 | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | 僅限遠端存放 | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | 僅限遠端存放 | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | 僅限遠端存放 | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | 僅限遠端存放 | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | 僅限遠端存放 | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | 僅限遠端存放 | 32 | 80000/1200 | 8|30000 |
