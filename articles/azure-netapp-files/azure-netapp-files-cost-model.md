---
title: Azure NetApp Files 的成本模型 |Microsoft Docs
description: 說明 Azure NetApp Files 用來管理服務費用的成本模型。
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
ms.date: 05/27/2020
ms.author: b-juche
ms.openlocfilehash: 78af9c12fb54b63e1a94c8b41a7ec2ac5c9b4e27
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84142141"
---
# <a name="cost-model-for-azure-netapp-files"></a>適用於Azure NetApp Files 的成本模型 

瞭解 Azure NetApp Files 的成本模型可協助您從服務管理您的費用。

## <a name="calculation-of-capacity-consumption"></a>計算容量耗用量

Azure NetApp Files 會依布建的儲存體容量計費。  布建的容量是藉由建立容量集區來配置。  容量集區是根據每小時增量的 $/provisioned-GiB/month 計費。 單一容量集區的大小下限為 4 TiB，而容量集區則後續會以 1 TiB 的增量擴充。 磁片區是在容量集區內建立的。  每個磁片區都會被指派一個從集區（已布建的容量）中遞減的配額。 可指派給磁片區的配額範圍是從最小值 100 GiB 到最大 100 TiB。  

針對作用中的磁片區，針對配額的容量耗用量是以邏輯（有效）容量為基礎。

如果磁片區的實際容量耗用量超過其儲存配額，磁片區就可以繼續成長。 只要實際磁片區大小小於系統限制（100 TiB），仍會允許寫入。  

容量集區中已使用的總容量（根據其布建的數量）是指派的配額或集區中所有磁片區實際耗用量的總和： 

   ![已使用的總容量計算](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下圖說明這些概念。  
* 我們有一個容量集區，其中有4個 TiB 的布建容量。  集區包含三個磁片區。  
    * 磁片區1指派了 2 TiB 的配額，並具有 800 GiB 的耗用量。  
    * 磁片區2的配額為 1 TiB，而且具有 100 GiB 的耗用量。  
    * 磁片區3被指派了 500 GiB 的配額，但有 800 GiB 的耗用量（超額）。  
* 容量集區會針對容量的 4 TiB （已布建的數量）進行計量。  
    3.8 會耗用容量 TiB （2 TiB 和 1 TiB 磁片區1和2的配額，以及 800 GiB 的磁片區3的實際耗用量）。 還有 200 GiB 的容量。

   ![具有三個磁片區的容量集區](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>容量耗用量的超額部分  

當集區的總使用容量超過其布建的容量時，仍允許資料寫入。  在寬限期（一小時）之後，如果集區的已使用容量仍超過其布建的容量，則集區大小會自動增加1個 TiB，直到布建的容量大於使用的總容量為止。  例如，在上圖中，如果磁片區3持續成長，而實際耗用量達到 1.2 TiB，則在寬限期之後，集區會自動調整為 5 TiB。  結果是布建的集區容量（5 TiB）超過已使用的容量（4.2 TiB）。  

雖然容量集區大小會自動成長以符合磁片區的需求，但不會在磁片區大小減少時自動降低。 如果您想要在磁片區大小減少之後調整容量集區的大小（例如，在資料清除磁片區之後），您需要_手動_減少容量集區大小。

## <a name="manual-changes-of-the-pool-size"></a>手動變更集區大小  

您可以手動增加或減少集區大小。 不過，適用下列條件約束：
* 服務的最小和最大限制  
    請參閱關於[資源限制](azure-netapp-files-resource-limits.md)的文章。
* 第一次 TiB 最低購買後的 TiB 增量
* 一小時的最小計費增量
* 布建的集區大小可能不會減少到小於集區中已使用的總容量。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大大小集區超額的行為   

您可以建立或調整大小的容量集區大小上限為 500 TiB。  當容量集區中已使用的總容量超過 500 TiB 時，將會發生下列情況：
* 仍然允許資料寫入（如果磁片區低於系統最多 100 TiB）。
* 在一小時的寬限期之後，集區會自動調整為 1 TiB 的增量，直到集區布建的容量超過總已使用的容量。
* 額外布建和已計費的集區容量超過 500 TiB 無法用來指派磁片區配額。 它也不能用來擴充效能 QoS 限制。  
    請參閱效能限制和 QoS 大小的[服務層級](azure-netapp-files-service-levels.md)。

下圖說明這些概念：
* 我們有一個具有 Premium 儲存層和 500-TiB 容量的容量集區。 集區包含九個磁片區。
    * 磁片區1到8會獲指派每個 TiB 的配額60。  已使用的總容量為 480 TiB。  
        每個磁片區的 QoS 限制為 3.75 GiB/秒的輸送量（60 TiB * 64 MiB/s）。  
    * 第9卷指派配額為 20 TiB。  
        Volume 9 的 QoS 限制為 1.25 GiB/秒的輸送量（20 TiB * 64 MiB/s）。
* 第9卷是超額的案例。 它有 25 TiB 的實際耗用量。  
    * 一小時寬限期之後，容量集區會調整為 505 TiB。  
        也就是說，已使用的總容量 = 8 * 60-磁片區1到8的 TiB 配額，以及第9卷實際耗用量的 25 TiB。
    * 計費的容量為 505 TiB。
    * 無法增加第9卷的磁片區配額（因為集區的總指派配額不得超過 500 TiB）。
    * 可能不會指派額外的 QoS 輸送量（因為集區的 QoS 總計仍以 500 TiB 為基礎）。

   ![具有9個磁片區的容量集區](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照集的容量耗用量 

Azure NetApp Files 中快照集的容量耗用量是根據父磁片區的配額來計費。  因此，它會與磁片區所屬的容量集區共用相同的計費費率。  不過，不同于使用中的磁片區，快照取耗用量是根據取用的增量容量來測量。  Azure NetApp Files 快照集本質上是差異的。 視資料的變更率而定，快照集通常會耗用比使用中磁片區的邏輯容量少很多的容量。 例如，假設您有一個 500-GiB 磁片區的快照集，其中只包含10個 GiB 的差異資料。 針對該快照集的磁片區配額收費的容量會是 10 GiB，而不是 500 GiB。 

## <a name="next-steps"></a>後續步驟

* [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
