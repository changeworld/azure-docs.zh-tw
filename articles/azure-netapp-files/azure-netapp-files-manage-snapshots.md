---
title: 使用 Azure NetApp Files 管理快照集 | Microsoft Docs
description: 說明如何使用 Azure NetApp Files 建立磁碟區的快照集，或從快照集還原至新的磁碟區。
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: ed13c61646bd2a6672b613964507d291a69a6821
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483596"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>使用 Azure NetApp Files 管理快照集

您可以使用 Azure NetApp Files 手動建立磁片區的隨選快照集，或從快照集還原至新的磁片區。 Azure NetApp Files 服務不會自動建立磁片區快照集。  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>建立磁碟區的隨選快照集

您只能以隨選的方式建立快照集。 目前不支援快照集原則。

1.  從 [磁碟區] 刀鋒視窗，按一下 [快照集]****。

    ![瀏覽快照集](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  按一下 [+ 新增快照集]**** 以為磁碟區建立隨選快照集。

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  在 [新增快照集] 視窗中，為您要建立的新快照集提供名稱。   

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. 按一下 [確定]。 

## <a name="restore-a-snapshot-to-a-new-volume"></a>將快照集還原至新磁碟區

目前，您只能將快照集還原至新的磁碟區。 
1. 從 [磁碟區] 刀鋒視窗移至 [管理快照集]**** 刀鋒視窗，以顯示快照集清單。 
2. 選取要還原的快照集。  
3. 以滑鼠右鍵按一下快照集名稱，然後從功能表選項中選取 [還原至新磁碟區]****。  

    ![將快照集還原至新磁碟區](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. 在 [新增磁碟區] 視窗中，提供新磁碟區的資訊：  
    * **檔案名**   
        為您要建立的磁碟區指定名稱。  
        
        名稱在資源群組內必須是唯一的。 長度至少必須有三個字元。  可以使用任何英數字元。

    * **檔案路徑**     
        指定要用來建立新磁碟區匯出路徑的檔案路徑。 匯出路徑會用來掛接和存取磁碟區。   
        
        掛接目標是 NFS 服務 IP 位址的端點。 這是自動產生的。   
        
        檔案路徑名稱只能包含字母、數字和連字號 ("-")。 長度必須介於 16 到 40 個字元之間。 

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額] 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。

    *   **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (Vnet)。  
        您指定的 Vnet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 Vnet 存取，或透過 Vnet 對等互連從與磁碟區位於相同區域的 Vnet 存取。 您可以透過 Express Route 從內部部署網路存取磁片區。 

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files 服務。 您可以透過選取子網路欄位下的 [新建]**** 來建立新的子網路。  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. 按一下 [確定]。   
    快照集還原到的新磁碟區會出現在 [磁碟區] 刀鋒視窗中。

## <a name="next-steps"></a>後續步驟

[了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
