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
ms.openlocfilehash: 68cd6673283362380fc5a1f4b780f0a22aa53402
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783507"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 和 Dsv4 系列

Dv4 和 Dsv4 系列會在超執行緒設定的 Intel 最高 &reg; &reg; 白金8272CL （Cascade Lake）處理器上執行，為大部分一般用途的工作負載提供更好的價值主張。 它的特色是維持所有核心 Turbo 主頻 3.4 GHz 的速度。 

> [!NOTE]
> 如需常見問題，請參閱[沒有本機暫存磁片的 AZURE VM 大小](azure-vms-no-temp-disk.md)。
## <a name="dv4-series"></a>Dv4 系列

Dv4 系列大小是在 Intel 最 &reg; 強 &reg; 白金8272CL （Cascade Lake）上執行。 Dv4 系列大小提供 vCPU、記憶體和遠端儲存體選項的組合，適用于大多數生產環境工作負載。 Dv4 系列 Vm 功能[Intel &reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。

遠端資料磁碟儲存體與虛擬機器分開計費。 若要使用 premium 儲存體磁片，請使用 Dsv4 大小。 Dsv4 大小的定價和計費方式與 Dv4 系列相同。


> [!IMPORTANT]
> 這些新的大小目前僅限公開預覽。 您可以在[這裡](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)註冊這些 Dv4 和 Dsv4 系列。 


ACU：195-210

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大 NIC/預期的網路頻寬 (Mbps) |
|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | 僅限遠端存放 | 4 | 2/1000 |
| Standard_D4_v4 | 4 | 16  | 僅限遠端存放 | 8 | 2/2000 |
| Standard_D8_v4 | 8 | 32 | 僅限遠端存放 | 16 | 4/4000 |
| Standard_D16_v4 | 16 | 64 | 僅限遠端存放 | 32 | 8/8000 |
| Standard_D32_v4 | 32 | 128 | 僅限遠端存放 | 32 | 8/16000 |
| Standard_D48_v4 | 48 | 192 | 僅限遠端存放 | 32 | 8/24000 |
| Standard_D64_v4 | 64 | 256 | 僅限遠端存放 | 32 | 8/30000 |

## <a name="dsv4-series"></a>Dsv4 系列

Dsv4 系列大小是在 Intel 最 &reg; 強 &reg; 白金8272CL （Cascade Lake）上執行。 Dv4 系列大小提供 vCPU、記憶體和遠端儲存體選項的組合，適用于大多數生產環境工作負載。 Dsv4 系列 Vm 功能[Intel &reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)。 遠端資料磁碟儲存體與虛擬機器分開計費。

> [!IMPORTANT]
> 這些新的大小目前僅限公開預覽。 您可以在[這裡](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)註冊這些 Dv4 和 Dsv4 系列。 

ACU：195-210

進階儲存體：支援

進階儲存體快取：支援

即時移轉：支援

記憶體保留更新：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取輸送量： IOPS/MBps （GiB 中的快取大小） | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | 僅限遠端存放 | 4 | 19000/120 （50） | 3000/48 | 2/1000 |
| Standard_D4s_v4 | 4 | 16 | 僅限遠端存放 | 8 | 38500/242 （100） | 6400/96 | 2/2000 |
| Standard_D8s_v4 | 8 | 32 | 僅限遠端存放 | 16 | 77000/485 （200） | 12800/192 | 4/4000 |
| Standard_D16s_v4 | 16 | 64  | 僅限遠端存放 | 32 | 154000/968 （400） | 25600/384 | 8/8000 |
| Standard_D32s_v4 | 32 | 128 | 僅限遠端存放 | 32 | 308000/1936 （800） | 51200/768 | 8/16000 |
| Standard_D48s_v4 | 48 | 192 | 僅限遠端存放 | 32 | 462000/2904 （1200） | 76800/1152 | 8/24000 |
| Standard_D64s_v4 | 64 | 256 | 僅限遠端存放 | 32 | 615000/3872 （1600） | 80000/1200 | 8/30000 |
