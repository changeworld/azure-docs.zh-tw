---
title: Azure NetApp Files 的服務層級 | Microsoft Docs
description: 描述 Azure NetApp Files 之服務層級的輸送量效能。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832588"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files 的服務等級
服務等級是容量池的屬性。 服務等級根據分配給卷的配額，根據容量池中允許的最大輸送量定義和區分。

## <a name="supported-service-levels"></a>支援的服務等級

Azure NetApp 檔支援三個服務等級：*超*、*高級*和*標準*。 

* <a name="Ultra"></a>超存儲

    Ultra 存儲層提供高達 128 MiB/s 的輸送量每 1 TiB 分配的容量配額。 

* <a name="Premium"></a>高級存儲

    高級存儲層提供最多 64 MiB/s 的輸送量每 1 TiB 分配的卷配額。 

* <a name="Standard"></a>標準存儲

    標準存儲層提供最多 16 MiB/s 的輸送量每 1 TiB 分配的卷配額。

## <a name="throughput-limits"></a>輸送量限制

卷的輸送量限制由以下因素的組合決定：
* 卷所屬的容量池的服務等級
* 分配給卷的配額  

這一概念如下圖所示：

![服務等級圖](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

在上面的示例 1 中，分配了 2 TiB 配額的高級存儲層的容量池中的卷將被分配為 128 MiB/s （2 TiB = 64 MiB/s）。 無論容量池大小或實際體積消耗量如何，此方案都適用。

在上面的示例 2 中，分配了 100 GiB 配額的高級存儲層的容量池中的卷將分配 6.25 MiB/s （0.09765625 TiB = 64 MiB/s） 的輸送量限制。 無論容量池大小或實際體積消耗量如何，此方案都適用。

## <a name="next-steps"></a>後續步驟

- 如需不同服務層級的價格，請參閱 [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
- 有關計算容量池中的容量消耗，請參閱[Azure NetApp 檔的成本模型](azure-netapp-files-cost-model.md) 
- [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
- 請參閱[Azure NetApp 檔的服務等級協定 （SLA）](https://azure.microsoft.com/support/legal/sla/netapp/)