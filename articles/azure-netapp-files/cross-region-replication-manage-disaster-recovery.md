---
title: 使用 Azure NetApp Files 跨區域複寫來管理災難復原 |Microsoft Docs
description: 說明如何使用 Azure NetApp Files 跨區域複寫來管理嚴重損壞修復。
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
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708580"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>使用跨區域複寫來管理嚴重損壞修復 

來源和目的地磁片區之間進行中的複寫 (請參閱建立複寫對 [等互連](cross-region-replication-create-peering.md)) 讓您做好損毀修復事件的準備。 

發生這類事件時，您可以 [容錯移轉至目的地磁片](#break-replication-peering-to-activate-the-destination-volume)區，讓用戶端能夠讀取和寫入目的地磁片區。 

在嚴重損壞修復之後，您可以使用重新 [同步](#resync-replication-to-reactivate-the-source-volume) 作業來容錯回復至來源磁片區，以使用目的地磁片區資料來覆寫來源磁片區資料。  接著，您會重新 [建立來源對目的地](#reestablish-source-to-destination-replication) 複寫，並重新掛接來源磁片區，讓用戶端存取。 

詳細資料如下所述。 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>中斷複寫對等互連以啟用目的地磁片區

當您需要啟動目的地磁片區時 (例如，當您想要容錯移轉至目的地區域時) ，您需要中斷複寫對等互連，然後掛接目的地磁片區。  

1. 若要中斷複寫對等互連，請選取目的地磁片區。 按一下 **[** 儲存體服務] 下的 [複寫]。  

2.  繼續之前，請先檢查下欄欄位：  
    * 確定鏡像狀態顯示為 [ ***鏡像***]。   
        如果鏡像狀態顯示為 *未初始化*，請勿嘗試中斷複寫對等互連。
    * 確定關聯性狀態顯示為 [ ***閒置***]。   
        如果關聯性狀態顯示為「 *傳輸*」，則請勿嘗試中斷複寫對等互連。   

    請參閱 [顯示覆寫關聯性的健全狀況狀態](cross-region-replication-display-health-status.md)。 

3.  按一下 [ **中斷對等互連**]。  

4.  出現提示時，請輸入 Yes，然後按一下 [**中斷** **]** 按鈕。 

    ![中斷複寫對等互連](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  遵循 [為 Windows 或 Linux 虛擬機器掛接或取消掛接磁片](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)區中的步驟，以掛接目的地磁片區。   
    此步驟可讓用戶端存取目的地磁片區。

## <a name="resync-replication-to-reactivate-the-source-volume"></a>重新同步複寫以重新啟用來源磁片區   

在嚴重損壞修復之後，您可以執行重新同步作業來重新啟用來源磁片區。  重新同步作業會反轉複寫程式，並將目的地磁片區的資料同步處理至來源磁片區。  

> [!IMPORTANT] 
> 重新同步作業會以目的地磁片區資料覆寫來源磁片區資料。  UI 會警告您可能發生資料遺失的情況。 在作業開始之前，系統會提示您確認重新同步處理動作。

1. 若要重新同步複寫，請選取 *來源* 磁片區。 按一下 **[** 儲存體服務] 下的 [複寫]。 然後按一下 [重新 **同步**]。  

2. 出現提示時，請輸入 **Yes** ，然後按一下 [重新 **同步** 處理] 按鈕。 
 
    ![重新同步複寫](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. 依照 [ [顯示覆寫關聯性的健全狀況狀態](cross-region-replication-display-health-status.md)] 中的步驟，監視來源磁片區健全狀況狀態。   
    當來源磁片區健全狀況狀態顯示下列值時，重新同步作業已完成，而且在目的地磁片區上所做的變更現在會在來源磁片區上被捕獲：   

    * 鏡像狀態： *鏡像*  
    * 傳輸狀態： *閒置*  

## <a name="reestablish-source-to-destination-replication"></a>重新建立來源對目的地複寫

從目的地至來源的重新同步作業完成之後，您必須再次中斷複寫對等互連，以重新建立來源對目的地複寫。 您也應該重新掛接來源磁片區，讓用戶端可以存取它。  

1. 中斷複寫對等互連：  
    a. 選取 *目的地* 磁片區。 按一下 **[** 儲存體服務] 下的 [複寫]。  
    b. 繼續之前，請先檢查下欄欄位：   
    * 確定鏡像狀態顯示為 [ ***鏡像***]。   
    如果鏡像狀態顯示為 *未初始化*，請勿嘗試中斷複寫對等互連。  
    * 確定關聯性狀態顯示為 [ ***閒置***]。   
    如果關聯性狀態顯示為「 *傳輸*」，則請勿嘗試中斷複寫對等互連。    

        請參閱 [顯示覆寫關聯性的健全狀況狀態](cross-region-replication-display-health-status.md)。 

    c. 按一下 [ **中斷對等互連**]。   
    d. 出現提示時，請輸入 Yes，然後按一下 [**中斷** **]** 按鈕。  

2. 重新同步處理來源磁片區與目的地磁片區：  
    a. 選取 *目的地* 磁片區。 按一下 **[** 儲存體服務] 下的 [複寫]。 然後按一下 [重新 **同步**]。   
    b. 出現提示時，請輸入 **Yes** ，然後按一下 [重新 **同步** 處理] 按鈕。

3. 遵循 [為 Windows 或 Linux 虛擬機器掛接或取消掛接磁片](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)區中的步驟，重新掛接來源磁片區。  
    此步驟可讓用戶端存取來源磁片區。

## <a name="next-steps"></a>後續步驟  

* [跨區域複寫](cross-region-replication-introduction.md)
* [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)
* [顯示複寫關聯性的健全狀態](cross-region-replication-display-health-status.md)
* [磁片區複寫計量](azure-netapp-files-metrics.md#replication)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)

