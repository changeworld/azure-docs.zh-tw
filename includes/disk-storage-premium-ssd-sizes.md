---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837556"
---
| 進階 SSD 的大小 | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| 磁碟大小 (以 GiB 為單位) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,096 | 8,192 | 16,384 | 32,767 |
| 每一磁碟的 IOPS | 120 | 120 | 120 | 120 | 240 | 500 | 1,100 | 2,300 | 5,000 | 7,500 | 7,500 | 16,000 | 18,000 | 20,000 |
| 每一磁碟的輸送量 | 25 MiB/秒 | 25 MiB/秒 | 25 MiB/秒 | 25 MiB/秒 | 50 MiB/秒 | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒| 500 MiB/秒 | 750 MiB/秒 | 900 MiB/秒 |
| 每一磁碟的 IOPS 高載上限** | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 |
| 每一磁碟的輸送量高載上限** | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 |
| 持續時間高載上限** | 30 分鐘  | 30 分鐘  | 30 分鐘  | 30 分鐘  | 30 分鐘  | 30 分鐘  | 30 分鐘  | 30 分鐘  |
| 符合保留資格 | 否  | 否  | 否  | 否  | 否  | 否  | 否  | 否  | 是，最多一年 | 是，最多一年 | 是，最多一年 | 是，最多一年 | 是，最多一年 | 是，最多一年 |

\*表示目前處於預覽中的磁碟大小，如需區域可用性的資訊，請參閱[新磁碟大小：受控和非受控](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged)。  
\*\**表示目前處於預覽狀態的功能，如需詳細資訊，請參閱[磁碟高載](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)。
