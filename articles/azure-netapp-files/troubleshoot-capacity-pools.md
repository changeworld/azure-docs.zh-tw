---
title: 針對 Azure NetApp Files 的容量集區問題進行疑難排解 |Microsoft Docs
description: 描述管理容量集區時可能發生的潛在問題，並提供問題的解決方案。
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
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: b-juche
ms.openlocfilehash: ff1899fbc89ab4a78bf793a133a7aa9a8f03dd2a
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337554"
---
# <a name="troubleshoot-capacity-pool-issues"></a>針對容量集區問題進行疑難排解

本文說明管理容量集區時可能發生的問題（包括集區變更作業）的解決方式。 

## <a name="issues-managing-a-capacity-pool"></a>管理容量集區的問題 

|     錯誤狀況    |     解決方案    |
|-|-|
| 建立容量集區的問題 |  請確定容量集區計數不超過限制。 請參閱 [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)。  如果計數小於限制，而且您仍然遇到問題，請提出支援票證，並指定容量集區名稱。 |
| 刪除容量集區的問題  |  確定您已在嘗試刪除容量集區的訂用帳戶中，移除所有 Azure NetApp Files 磁片區和快照集。 <br> 如果您已移除所有磁片區和快照集，但仍然無法刪除容量集區，則資源的參考可能仍會存在，而不會顯示在入口網站中。 在此情況下，請提出支援票證，並指定您已執行上述的建議步驟。 |
| 磁片區建立或修改失敗，發生 `Requested throughput not available` 錯誤 | 磁片區的可用輸送量取決於其容量集區的大小和服務層級。 如果沒有足夠的輸送量，您應該增加集區大小或調整現有的磁片區輸送量。 | 

## <a name="issues-moving-a-capacity-pool"></a>移動容量集區的問題 
|     錯誤狀況    |     解決方案    |
|-|-|
| 不允許變更磁片區的容量集區。 | 您可能尚未取得使用這項功能的授權。 <br> 將磁片區移至另一個容量集區的功能目前為預覽狀態。 如果您是第一次使用這項功能，則必須先註冊該功能，並加以設定 `-FeatureName ANFTierChange` 。 請參閱中的註冊步驟，以 [動態方式變更磁片區的服務層級](dynamic-change-volume-service-level.md)。 |
| 容量集區大小對磁片區大小總計而言太小。 |  此錯誤是因為目的地容量集區沒有移動磁片區的可用容量。  <br> 增加目的地集區的大小，或選擇較大的另一個集區。  請參閱 [調整容量集區或磁片](azure-netapp-files-resize-capacity-pools-or-volumes.md)區的大小。   |
|  無法完成集區變更，因為 `'{source pool name}'` 目標集區中已有呼叫的磁片區 `'{target pool name}'` | 發生此錯誤的原因是目標容量集區中已有相同名稱的磁片區。  選取另一個沒有相同名稱之磁片區的容量集區。   | 

## <a name="next-steps"></a>後續步驟  

* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [管理手動 QoS 容量集區](manage-manual-qos-capacity-pool.md)
* [動態變更磁碟區的服務等級](dynamic-change-volume-service-level.md)
* [調整容量集區或磁碟區的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)
