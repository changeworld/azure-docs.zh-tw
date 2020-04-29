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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460427"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files 的計量

Azure NetApp Files 提供有關已配置儲存體、實際儲存體使用量、磁片區 IOPS 和延遲的計量。 藉由分析這些計量，您可以更了解 NetApp 帳戶的使用方式模式與磁碟區效能。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量集區的使用計量

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *配置給磁片區大小的集區*  
    指定容量集區中的磁片區配額（GiB）總數（也就是容量集區中磁片區的布建大小總計）。  
    此大小是您在建立磁片區時所選取的大小。  
- *已耗用的集區大小*  
    在容量集區中的磁片區上使用的邏輯空間（GiB）總數。  
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
- *磁片區已耗用大小*   
    磁片區中使用的總邏輯空間（GiB）。  
    此大小包含作用中的檔案系統和快照集所使用的邏輯空間。  
- *磁片區快照集大小*   
   磁片區中的快照集所使用的累加邏輯空間。  

## <a name="performance-metrics-for-volumes"></a>磁片區的效能計量

- *AverageReadLatency*   
    從磁片區讀取的平均時間（以毫秒為單位）。
- *AverageWriteLatency*   
    從磁片區寫入的平均時間（以毫秒為單位）。
- *ReadIops*   
    每秒磁片區的讀取次數。
- *WriteIops*   
    每秒磁片區的寫入數目。

## <a name="next-steps"></a>後續步驟

* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [建立適用於 Azure NetApp Files 的磁碟區](azure-netapp-files-create-volumes.md)
