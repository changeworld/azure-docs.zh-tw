---
title: Azure NetApp 檔的成本模型 |微軟文檔
description: 描述 Azure NetApp 檔的成本模型，用於管理服務中的費用。
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995106"
---
# <a name="cost-model-for-azure-netapp-files"></a>適用於Azure NetApp Files 的成本模型 

瞭解 Azure NetApp 檔的成本模型可説明您從服務中管理費用。

## <a name="calculation-of-capacity-consumption"></a>容量消耗的計算

Azure NetApp 檔根據預配存儲容量計費。  通過創建容量池分配預配容量。  容量池按小時增量按 $/預配-GiB/月計費。 單個容量池的最小大小為 4 TiB，容量池隨後可以以 1-TiB 增量進行擴展。 卷在容量池中創建。  為每個卷分配一個配額，從池預配的容量中清除。 可分配給卷的配額範圍從至少 100 GiB 到最多 100 TiB。  

對於啟動磁碟區，針對配額的容量消耗基於邏輯（有效）容量。

如果卷的實際容量消耗超過其存儲配額，則卷可以繼續增長。 只要實際卷大小小於系統限制 （100 TiB），仍允許寫入。  

容量池中與其預配量的已用容量總額是池內所有卷的分配配額或實際消耗量的較大的總和： 

   ![使用容量計算總額](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下圖說明瞭這些概念。  
* 我們有一個容量池，具有 4 TiB 的預配容量。  池包含三個卷。  
    * 第 1 卷的配額為 2 TiB，並且具有 800 GiB 的消耗量。  
    * 第 2 卷的配額為 1 TiB，並且具有 100 GiB 的消耗量。  
    * 第 3 卷的配額為 500 GiB，但具有 800 GiB 的消耗量（超額）。  
* 容量池按容量的 4 TiB（預配量）進行計量。  
    3.8 容量消耗的 TiB（第 1 卷和第 2 卷的配額為 2 TiB 和 1 TiB，第 3 卷的實際消耗量為 800 GiB）。 剩餘產能為 200 GiB。

   ![具有三個卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>容量消耗過剩  

當池的總使用容量超過其預配容量時，仍允許寫入資料。  寬限期（一小時）後，如果池的已用容量仍超過其預配容量，則池大小將自動以 1 TiB 的增量增加，直到預配容量大於已配置的總容量。  例如，在上面的插圖中，如果卷 3 繼續增長，實際消耗量達到 1.2 TiB，則寬限期過後，池將自動調整到 5 TiB。  結果是預配池容量 （5 TiB） 超過使用容量 （4.2 TiB）。  

## <a name="manual-changes-of-the-pool-size"></a>手動更改池大小  

您可以手動增加或減小池大小。 但是，以下約束適用：
* 服務最低和最大限制  
    請參閱有關[資源限制](azure-netapp-files-resource-limits.md)的文章。
* 初始 4-TiB 最低購買後 1-TiB 增量
* 一小時最低計費增量
* 預配池大小不能減少到低於池中的總使用容量。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大規模池超額行為   

您可以創建或調整大小的容量池的最大大小為 500 TiB。  當容量池中的總使用容量超過 500 TiB 時，將發生以下情況：
* 仍允許寫入資料（如果卷低於系統最大值 100 TiB）。
* 在一小時的寬限期後，池將自動以 1-TiB 增量調整大小，直到池預配容量超過總使用容量。
* 超過 500 TiB 的額外預配和計費池容量不能用於分配卷配額。 它還不能用於擴充性能 QoS 限制。  
    有關性能限制和 QoS 大小調整的[服務等級](azure-netapp-files-service-levels.md)。

下圖說明瞭以下概念：
* 我們有一個容量池，具有高級存儲層和 500-TiB 容量。 池包含九個卷。
    * 第 1 卷到第 8 卷的配額為 60 TiB。  總使用容量為 480 TiB。  
        每個卷的 QoS 限制為 3.75 GiB/s 的輸送量（60 TiB = 64 MiB/s）。  
    * 第 9 卷的配額為 20 TiB。  
        第 9 卷的 QoS 限制為 1.25 GiB/s 的輸送量（20 TiB = 64 MiB/s）。
* 第 9 卷是一個超額情況。 它有25 TiB的實際消耗。  
    * 在一小時的寬限期後，容量池將調整到 505 TiB。  
        也就是說，第 1 卷至 8 卷的總使用容量 = 8 = 60-TiB 配額，第 9 卷的實際消耗量為 25 TiB。
    * 計費容量為 505 TiB。
    * 無法增加第 9 卷的卷配額（因為池的總分配配額不得超過 500 TiB）。
    * 可能無法分配其他 QoS 輸送量（因為池的總 QoS 仍基於 500 TiB）。

   ![具有九個卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照的容量消耗 

Azure NetApp 檔中快照的容量消耗根據父卷的配額收費。  因此，它與卷所屬的容量池共用相同的計費速率。  但是，與啟動磁碟區不同，快照消耗量是根據消耗的增量容量來衡量的。  Azure NetApp 檔快照本質上是有差異的。 根據資料的更改速率，快照消耗的容量通常比啟動磁碟區的邏輯容量少得多。 例如，假設您有一個僅包含 10 GiB 差異資料的 500-GiB 卷的快照。 根據該快照的卷配額收取的容量為 10 GiB，而不是 500 GiB。 

## <a name="next-steps"></a>後續步驟

* [Azure NetApp 檔定價頁](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
