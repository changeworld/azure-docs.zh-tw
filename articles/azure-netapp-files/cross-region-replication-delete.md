---
title: 刪除 Azure NetApp Files 跨區域複寫的磁片區複製或磁片區 |Microsoft Docs
description: 描述如何刪除來源和目的地磁片區之間不再需要的複寫連接。
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249883"
---
# <a name="delete-volume-replications-or-volumes"></a>刪除磁片區複製或磁片區

本文說明如何刪除磁片區複製。 它也會說明如何刪除來源或目的地磁片區。

## <a name="delete-volume-replications"></a>刪除磁片區複製

您可以藉由刪除磁片區複寫，在來源與目的地磁片區之間終止複寫連接。 您必須從目的地磁片區刪除複寫。 刪除作業只會移除複寫的授權;它不會移除來源或目的地磁片區。 

1. 刪除磁片區複寫之前，請確定複寫對等互連已中斷。 若要中斷複寫對等互連： 

    1. 選取 *目的地* 磁片區。 按一下 **[** 儲存體服務] 下的 [複寫]。  

    2.  繼續之前，請先檢查下欄欄位：  
        * 確定鏡像狀態顯示 [**鏡像**]。   
            如果鏡像狀態顯示 _Uninitialized *，請勿嘗試中斷複寫對等互連。
        * 確定關聯性狀態顯示 [**閒置**]。   
            如果關聯性狀態顯示 _Transferring *，請勿嘗試中斷複寫對等互連。   

        請參閱 [顯示覆寫關聯性的健全狀況狀態](cross-region-replication-display-health-status.md)。 

    3.  按一下 [ **中斷對等互連**]。  

    4.  出現提示時，請輸入 Yes，然後按一下 [**中斷** **]** 。 

        ![中斷複寫對等互連](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. 若要刪除磁片區複寫，請選取來源或目的地磁片 **區的複寫** 。  

2. 按一下 **[刪除]** 。    

3. 輸入 **[是]** ，然後按一下 [ **刪除**]，以確認刪除。   

    ![刪除複寫](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>刪除來源或目的地磁片區

如果您想要刪除來源或目的地磁片區，您必須依照描述的循序執行下列步驟。 否則， `Volume with replication cannot be deleted` 就會發生此錯誤。  

1. 從目的地磁片區 [刪除磁片](#delete-volume-replications)區複寫。   

2. 視需要刪除目的地或來源磁片區，方法是以滑鼠右鍵按一下磁片區名稱，然後選取 [ **刪除**]。   

    ![顯示磁片區之右鍵功能表的螢幕擷取畫面。](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>後續步驟  

* [跨區域複寫](cross-region-replication-introduction.md)
* [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)
* [顯示複寫關聯性的健全狀態](cross-region-replication-display-health-status.md)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)

