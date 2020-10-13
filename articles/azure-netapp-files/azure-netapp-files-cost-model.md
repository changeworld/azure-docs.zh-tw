---
title: Azure NetApp Files 的成本模型 |Microsoft Docs
description: 說明用來管理服務費用的 Azure NetApp Files 成本模型。
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325601"
---
# <a name="cost-model-for-azure-netapp-files"></a>適用於Azure NetApp Files 的成本模型 

瞭解 Azure NetApp Files 的成本模型可協助您管理服務的費用。 

如需跨區域複寫的專用成本模型，請參閱 [跨區域複寫的成本模型](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)。

## <a name="calculation-of-capacity-consumption"></a>容量耗用量的計算

Azure NetApp Files 的計費依據是布建的儲存體容量。  布建的容量是藉由建立容量集區來配置。  容量集區會依每小時遞增的 $/provisioned-GiB/month 計費。 單一容量集區的大小下限為 4 TiB，容量集區則可在 1 TiB 的增量下展開。 磁片區是在容量集區中建立的。  每個磁片區都會被指派一個從集區布建容量遞減的配額。 可以指派給磁片區的配額範圍從最小 100 GiB 到最多 100 TiB。  

針對使用中的磁片區，依配額的容量耗用量是以邏輯 (有效) 容量為基礎。

如果磁片區的實際容量耗用量超過其儲存體配額，則磁片區會持續成長。 只要實際的磁片區大小小於系統限制 (100 TiB) ，仍會允許寫入。  

容量集區中已使用的總容量與其布建數量的總和是在集區內指派配額或實際耗用量的總和： 

   ![使用的總容量計算](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下圖說明這些概念。  
* 容量集區具有 4 TiB 的布建容量。  集區包含三個磁片區。  
    * 磁片區1指派了 2 TiB 的配額，且具有 800 GiB 的耗用量。  
    * 磁片區2的配額指派為 1 TiB，且具有 100 GiB 的耗用量。  
    * 磁片區3指派了 500 GiB 的配額，但有 800 GiB 的耗用量 (超額) 。  
* 容量集區會以 4 TiB 的容量計費， (布建的數量) 。  
    3.8 TiB 容量的取用 (2 TiB 和1個磁片區1和2的配額 TiB，以及磁片區 3) 實際耗用量的 800 GiB。 和 200 GiB 的容量是剩餘的。

   ![具有三個磁片區的容量集區](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>容量耗用量的超額部分  

當集區的總使用容量超過其布建的容量時，仍允許資料寫入。  在寬限期 (一小時) 之後，如果集區的已使用容量仍超過其布建容量，則會自動以 1 TiB 增量增加集區大小，直到布建的容量大於總使用容量。  例如，在上圖中，如果磁片區3持續成長，且實際耗用量達到 1.2 TiB，則在寬限期之後，集區會自動調整大小為 5 TiB。  結果是布建的集區容量 (5 TiB) 超過使用的容量 (4.2 TiB) 。  

雖然容量集區大小會自動成長以符合磁片區的需求，但不會在磁片區大小減少時自動縮減。 如果您想要在磁片區大小減少之後調整容量集區大小 (例如，在磁片區) 的資料清除之後，您需要 _手動_ 縮減容量集區大小。

## <a name="manual-changes-of-the-pool-size"></a>變更集區大小的手動變更  

您可以手動增加或減少集區大小。 但是，下列條件約束適用：
* 服務的最小和最大限制  
    請參閱有關 [資源限制](azure-netapp-files-resource-limits.md)的文章。
* 在初始 4 TiB 的最小購買之後的 1 TiB 增量
* 一小時的最小計費增量
* 布建的集區大小可能不會減少為小於集區中的總使用容量。
* 對於具有手動 QoS 的容量集區，只有在大小和服務層級提供的輸送量高於所有磁片區的實際指派輸送量時，才能減少集區大小。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大大小集區超額的行為   

您可以建立或調整大小的容量集區大小上限為 500 TiB。  當容量集區中的總使用容量超過 500 TiB 時，將會發生下列情況：
* 如果磁片區低於系統最大的 100 TiB) ，仍會允許資料寫入 (。
* 在一小時的寬限期之後，集區會自動以 1 TiB 增量調整大小，直到集區布建容量超過總使用容量為止。
* 超過 500 TiB 的額外布建和計費集區容量不能用來指派磁片區配額。 也不能用來擴充效能 QoS 限制。  
    請參閱有關效能限制和 QoS 大小調整的 [服務層級](azure-netapp-files-service-levels.md) 。

下圖說明這些概念：
* 我們有一個具有 Premium 儲存層和 500 TiB 容量的容量集區。 集區包含九個磁片區。
    * 磁片區1到8會指派配額 60 TiB。  已使用的總容量為 480 TiB。  
        每個磁片區具有 3.75 GiB/秒輸送量的 QoS 限制 (60 TiB * 64 MiB/s) 。  
    * Volume 9 指派配額 20 TiB。  
        磁片區9有 1.25 GiB/秒的 QoS 限制 (20 TiB * 64 MiB/s) 。
* Volume 9 是超額案例。 它有 25 TiB 的實際耗用量。  
    * 在一小時的寬限期內，容量集區會調整大小為 505 TiB。  
        亦即，使用的總容量 = 8 * 60-磁片區1到8的 TiB 配額，以及磁片區9的實際耗用量 TiB 25。
    * 計費的容量為 505 TiB。
    * 無法增加磁片區9的磁片區配額 (，因為集區的總指派配額可能不會超過 500 TiB) 。
    * 可能無法指派額外的 QoS 輸送量 (因為集區的 QoS 總計仍以 500 TiB) 為基礎。

   ![具有九個磁片區的容量集區](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照集的容量耗用量 

Azure NetApp Files 中快照集的容量耗用量會依父磁片區的配額收費。  因此，它會與磁片區所屬的容量集區共用相同的計費費率。  不過，與使用中的磁片區不同的是，快照耗用量是根據取用的增量容量來測量。  Azure NetApp Files 快照集本質上是差異。 根據資料的變更率，快照集的容量通常會比使用中磁片區的邏輯容量更少。 例如，假設您有一個 500 GiB 磁片區的快照集，其中只包含10個 GiB 的差異資料。 針對該快照集的磁片區配額收費的容量為 10 GiB，而不是 500 GiB。 

## <a name="next-steps"></a>後續步驟

* [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [跨區域複寫的成本模型](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
