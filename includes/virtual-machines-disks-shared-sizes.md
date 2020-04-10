---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008319"
---
目前,只有超磁碟和高級 SSD 才能啟用共用磁碟。 不同的磁碟大小可能具有不同的`maxShares`限制,在設置值時不能超過`maxShares`該 限制。 對於先進 SSD,支援共享其磁碟的磁碟大小為 P15 和更高。

對於每個磁碟,可以定義一個`maxShares`值,該值表示可以同時共用磁碟的最大節點數。 例如,如果您計劃設定 2 節點故障轉移群集,`maxShares=2`則設定 。 最大值是上限。 只要節點數低於指定`maxShares`值,節點就可以加入或離開群集(裝載或卸載磁碟)。

> [!NOTE]
> 僅當`maxShares`磁碟與所有節點分離時,才能設置或編輯該值。

### <a name="premium-ssd-ranges"></a>進階 SSD 範圍

下表說明了按進階磁碟大小允許`maxShares`的最大 值:

|磁碟大小  |最大分享限制  |
|---------|---------|
|P15, P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁碟的 IOPS 和頻寬`maxShares`限制不受 該值的影響。 例如,P15 磁碟的最大 IOPS 是 1100,無論是最大共用 = 1 還是最大共用> 1。

### <a name="ultra-disk-ranges"></a>超磁碟範圍

最小`maxShares`值為 1,最大`maxShares`值為 5。 超磁碟的大小限制,任何大小的超磁碟都可以使用任何值`maxShares`,最高包括最大值。