---
title: 適用於 Azure NetApp Files 的計量 | Microsoft Docs
description: Azure NetApp Files 可針對配置的儲存體、實際的儲存體使用量、磁片區 IOPS 及延遲提供度量。 使用這些計量來瞭解使用量和效能。
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
ms.date: 10/07/2020
ms.author: b-juche
ms.openlocfilehash: 80746d7cef4bb0a70c6bb9eb6ff2e053ea6c7a0a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824692"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files 的計量

Azure NetApp Files 可針對配置的儲存體、實際的儲存體使用量、磁片區 IOPS 及延遲提供度量。 藉由分析這些計量，您可以更了解 NetApp 帳戶的使用方式模式與磁碟區效能。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量集區的使用計量

- *集區配置大小*   
    集區的布建大小。

- *配置給磁片區大小的集區*  
    指定容量集區中的磁片區配額 (GiB)  (也就是容量集區中磁片區的布建大小總計) 。  
    此大小是您在建立磁片區時選取的大小。  

- *已耗用的集區大小*  
    在容量集區中的磁片區上， (GiB) 使用的邏輯空間總數。  

- *集區的快照集大小總計*    
    集區中所有磁片區的快照集大小總和。

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>磁碟區的使用計量

<!-- ANF-5023: fixed version: 2020.08, 2020.09
- *Percentage Volume Consumed Size*    
    The percentage of the volume consumed, including snapshots.  
-->
- *磁片區配置大小*   
    布建的磁片區大小
- *磁片區配額大小*    
    磁片區布建 (GiB) 配額大小。   
- *耗用的磁片區大小*   
    磁片區的邏輯大小 (使用的位元組) 。  
    此大小包含作用中的檔案系統和快照集所使用的邏輯空間。  
- *磁片區快照集大小*   
   磁片區中所有快照集的大小。  

## <a name="performance-metrics-for-volumes"></a>磁片區的效能計量

- *平均讀取延遲*   
    從磁片區讀取的平均時間（以毫秒為單位）。
- *平均寫入延遲*   
    從磁片區寫入的平均時間（以毫秒為單位）。
- *讀取 IOPS*   
    每秒磁片區的讀取數目。
- *寫入 IOPS*   
    每秒磁片區的寫入次數。
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>磁片區複寫計量

- *磁片區複寫狀態是否狀況良好*   
    複寫關聯性的條件。 狀況良好的狀態是以表示 `1` 。 狀況不良狀態是以表示 `0` 。

- *磁片區複寫傳送*    
    磁片區複寫的狀態是否為「傳輸中」。 
 
- *磁片區複寫延遲時間*   
    鏡像上的資料延遲來源的時間長度（以秒為單位）。 

- *磁片區複寫上次傳送持續時間*   
    最後一次傳送完成所花費的時間量（以秒為單位）。 

- *磁片區複寫上次傳輸大小*    
    最後一次傳輸中傳輸的位元組總數。 

- *磁片區複寫進度*    
    傳送給目前傳送作業的資料總量。 

- *磁片區複寫總計傳送*   
    針對關聯性傳輸的累計位元組。 

## <a name="next-steps"></a>後續步驟

* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [建立適用於 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)
