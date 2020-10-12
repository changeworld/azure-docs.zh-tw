---
title: 顯示 Azure NetApp Files 複寫關聯性的健全狀況狀態 |Microsoft Docs
description: 說明如何查看來源磁片區或 Azure NetApp Files 的目的地磁片區上的複寫狀態。
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 95c1202fb56e882554d40926e9d5ecec7be49086
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708584"
---
# <a name="display-health-status-of-replication-relationship"></a>顯示複寫關聯性的健全狀態 

您可以查看來源磁片區或目的地磁片區上的複寫狀態。  

## <a name="display-replication-status"></a>顯示覆寫狀態

1. 從來源磁片區或目的地磁片區，針對任一磁片 **區按一下 [** 儲存體服務] 下的 [複寫]。

    以下是顯示的複寫狀態和健康情況資訊：  
    * **結束點類型** –識別磁片區是否為複寫的來源或目的地。
    * **健全狀況** –顯示覆寫關聯性的健全狀況狀態。
    * **鏡像狀態** –顯示下列其中一個值：
        * *未初始化*：  
            這是建立對等互連關聯性時的初始和預設狀態。 在初始化成功完成之前，狀態會保持未初始化。
        * *鏡像*：   
            目的地磁片區已初始化，並已準備好接收鏡像更新。
        * 已*中斷*：   
            這是中斷對等互連關聯性之後的狀態。 目的地磁片區是 `‘RW’` ，而且快照集存在。
    * **關聯性狀態** –顯示下列其中一個值： 
        * *閒置*：  
            沒有進行中的傳輸作業，而且未來的傳輸未停用。
        * *傳送*：  
            傳送作業正在進行中，且未來的傳輸未停用。
    * 複寫**排程**：顯示初始化 (基準複製) 完成時，將執行累加式鏡像更新的頻率。

    * **總進度** -顯示傳送給目前傳送作業的總數據量（以位元組為單位）。 此數量是傳輸的實際位數，可能與來源和目的地磁片區報告的邏輯空間不同。  

    ![複寫健全狀況狀態](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> 如果先前的複寫作業未完成，則複寫關聯性會將健全狀況狀態顯示為狀況 *不良* 。 此狀態是以較低的傳輸視窗傳輸大型磁片區的結果 (例如，大型磁片區) 的10分鐘傳輸時間。 在此情況下，關聯性狀態會顯示 [ *傳輸* 中] 和 [健康情況] 顯示狀況 *不良*。

## <a name="next-steps"></a>後續步驟  

* [跨區域複寫](cross-region-replication-introduction.md)
* [管理災害復原](cross-region-replication-manage-disaster-recovery.md)
* [磁片區複寫計量](azure-netapp-files-metrics.md#replication)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)

