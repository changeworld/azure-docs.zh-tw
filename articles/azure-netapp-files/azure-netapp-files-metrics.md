---
title: 適用於 Azure NetApp Files 的計量 | Microsoft Docs
description: 說明 Azure NetApp Files 的計量。
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460427"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files 的計量

Azure NetApp 檔提供有關已分配存儲、實際存儲使用方式、卷 IOPS 和延遲的指標。 藉由分析這些計量，您可以更了解 NetApp 帳戶的使用方式模式與磁碟區效能。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量集區的使用計量

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *分配給卷大小的池*  
    給定容量池中的卷配額 （GiB） 總數（即容量池中卷的預配大小總計）。  
    此大小是創建卷期間選擇的大小。  
- *池消耗大小*  
    在容量池中跨卷使用的邏輯空間 （GiB） 的總和。  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>磁碟區的使用計量

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *體積消耗大小*   
    卷 （GiB） 中使用的總邏輯空間。  
    此大小包含作用中的檔案系統和快照集所使用的邏輯空間。  
- *卷快照大小*   
   卷中快照使用的增量邏輯空間。  

## <a name="performance-metrics-for-volumes"></a>卷的性能指標

- *平均讀取延遲*   
    從卷讀取的平均時間（以毫秒為單位）。
- *平均寫入延遲*   
    從卷寫入的平均時間（以毫秒為單位）。
- *閱讀*   
    每秒讀取卷的次數。
- *寫入*   
    每秒寫入卷的次數。

## <a name="next-steps"></a>後續步驟

* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [建立適用於 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)
