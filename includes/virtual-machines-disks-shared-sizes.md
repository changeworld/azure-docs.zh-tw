---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471673"
---
目前，只有高級 SSD 才能啟用共用磁片。 支援此功能的磁片大小為 P15 和更高。 不同的磁片大小可能具有不同的`maxShares`限制，在設置值時不能超過該`maxShares`限制。

對於每個磁片，可以定義一個`maxShares`值，該值表示可以同時共用磁片的最大節點數。 例如，如果您計畫設置 2 節點容錯移轉叢集，則設置`maxShares=2`。 最大值是上限。 只要節點數低於指定`maxShares`值，節點就可以加入或離開群集（裝載或卸載磁片）。

> [!NOTE]
> 僅當`maxShares`磁片與所有節點分離時，才能設置或編輯該值。

下表說明了按磁片大小表示的`maxShares`允許最大值：

|磁碟大小  |最大共用限制  |
|---------|---------|
|P15， P20     |2         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁片的 IOPS 和頻寬限制不受`maxShares`該值的影響。 例如，P15 磁片的最大 IOPS 是 1100，無論是最大共用 = 1 還是最大共用> 1。