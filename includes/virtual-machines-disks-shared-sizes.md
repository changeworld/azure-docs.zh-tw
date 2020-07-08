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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81008319"
---
目前只有 ultra 磁片和 premium Ssd 可以啟用共用磁片。 不同的磁片大小可能會有不同的 `maxShares` 限制，當您設定值時，您就不能超過此限制 `maxShares` 。 針對高階 Ssd，支援共用其磁片的磁片大小會 P15 和更高。

針對每個磁片，您可以定義一個 `maxShares` 值，代表可以同時共用磁片的節點數目上限。 例如，如果您打算設定2個節點的容錯移轉叢集，您會設定 `maxShares=2` 。 最大值為上限。 只要節點數目低於指定的值，節點就可以加入或離開叢集（掛接或卸載磁片） `maxShares` 。

> [!NOTE]
> `maxShares`只有當磁片從所有節點卸離時，才可以設定或編輯此值。

### <a name="premium-ssd-ranges"></a>進階 SSD 範圍

下表說明 premium 磁片大小所允許的最大值 `maxShares` ：

|磁碟大小  |maxShares 限制  |
|---------|---------|
|P15、P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁片的 IOPS 和頻寬限制不會受到值的影響 `maxShares` 。 例如，P15 磁片的最大 IOPS 為1100，不論 maxShares = 1 或 maxShares > 1。

### <a name="ultra-disk-ranges"></a>Ultra 磁片範圍

最小 `maxShares` 值為1，而最大 `maxShares` 值為5。 Ultra 磁片沒有大小限制，任何大小的 ultra 磁片都可以使用任何的值 `maxShares` ，最多可包含最大值。