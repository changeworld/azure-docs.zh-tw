---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9000cab026ec08bd78d8a8f334c8967a8a747c95
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202290"
---
目前只有 premium Ssd 可以啟用共用磁片。 支援這項功能的磁片大小 P15 和更大。 不同的磁片大小可能會有不同的 `maxShares` 限制，當您設定 `maxShares` 值時，您就不能超過。

針對每個磁片，您可以定義一個 `maxShares` 值，代表可以同時共用磁片的節點數目上限。 例如，如果您打算設定2個節點的容錯移轉叢集，您會設定 `maxShares=2`。 最大值為上限。 只要節點數目低於指定的 `maxShares` 值，節點就可以加入或離開叢集（掛接或卸載磁片）。

> [!NOTE]
> 只有當磁片從所有節點卸離時，才可以設定或編輯 `maxShares` 值。

下表說明根據磁片大小所允許 `maxShares` 的最大值：

|磁碟大小  |maxShares 限制  |
|---------|---------|
|P15、P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁片的 IOPS 和頻寬限制不會受到 `maxShares` 值的影響。 例如，P15 磁片的最大 IOPS 為1100，不論 maxShares = 1 或 maxShares > 1。