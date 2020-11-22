---
title: 建立適用于 Azure NetApp Files 的磁片區複寫 |Microsoft Docs
description: 說明如何建立 Azure NetApp Files 的磁片區複寫對等互連，以設定跨區域複寫。
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
ms.openlocfilehash: 412724a072bfc03a67bf1005173702f757c6fdf4
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249932"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>建立適用于 Azure NetApp Files 的磁片區複寫

> [!IMPORTANT]
> 跨區域複寫功能目前處於公開預覽狀態。 您必須透過 [Azure NetApp Files 跨區域複寫等候清單提交頁面](https://aka.ms/anfcrrpreviewsignup)提交等候清單要求，以存取此功能。 使用跨區域複寫功能之前，請先從 Azure NetApp Files 團隊等候官方確認電子郵件。

本文說明如何藉由建立複寫對等互連來設定跨區域複寫。 

設定複寫對等互連可讓您以非同步方式將資料從 Azure NetApp Files 磁片區 (來源) 複製到另一個 Azure NetApp Files 磁片區 (目的地) 。 來源磁片區和目的地磁片區必須部署在不同的區域中。 目的地容量集區的服務等級可以與來源容量集區的服務層級相符，也可以選取不同的服務等級。   

Azure NetApp Files 複寫目前不支援多個訂用帳戶;所有的複寫都必須在單一訂用帳戶下執行。

開始之前，請確定您已複習 [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)。  

## <a name="locate-the-source-volume-resource-id"></a>找出來源磁片區資源識別碼  

您必須取得您想要複寫之來源磁片區的資源識別碼。 

1. 移至來源磁片區，然後在 [設定] 底下選取 [ **屬性** ]，以顯示來源磁片區資源識別碼。   
    ![找出來源磁片區資源識別碼](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. 將資源識別碼複製到剪貼簿。  稍後您將會用到此資訊。

## <a name="create-the-data-replication-volume-the-destination-volume"></a> (目的地磁片區建立資料複寫磁片區) 

您必須建立目的地磁片區，以從中複寫來源磁片區的資料。  您必須在目的地區域中擁有 NetApp 帳戶和容量集區，才能建立目的地磁片區。 

1. 目的地帳戶必須與來源磁片區區域位於不同的區域。 如有必要，請遵循 [建立 netapp 帳戶](azure-netapp-files-create-netapp-account.md)中的步驟，在 Azure 區域中建立要用於複寫的 NetApp 帳戶。   
您也可以在不同的區域中選取現有的 NetApp 帳戶。  

2. 必要時，請遵循 [設定容量集](azure-netapp-files-set-up-capacity-pool.md)區中的步驟，在新建立的 NetApp 帳戶中建立容量集區。   

    您也可以選取現有的容量集區來裝載複寫目的地磁片區。  

    目的地容量集區的服務等級可以與來源容量集區的服務層級相符，也可以選取不同的服務等級。

3. 遵循將 [子網委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)中的步驟，在區域中委派要用於複寫的子網。

4. 選取目的地 NetApp 帳戶中儲存體服務下的 **磁片** 區，以建立資料複寫磁片區。 然後按一下 [ **+ 新增資料** 複寫] 按鈕。  

    ![新增資料複寫](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. 在出現的 [建立磁片區] 頁面中，在 [ **基本** ] 索引標籤底下完成下欄欄位：
    * 磁碟區名稱
    * 容量集區
    * 磁片區配額
        > [!NOTE] 
        > 建議您對來源磁片區的磁片區配額大小進行鏡像。
    * 虛擬網路 
    * 子網路

    如需欄位的詳細資訊，請參閱 [建立 NFS 磁片](azure-netapp-files-create-volumes.md#create-an-nfs-volume)區。 

6. 在 [ **通訊協定** ] 索引標籤下，選取與來源磁片區相同的通訊協定。  
針對 NFS 通訊協定，請確定匯出原則規則符合將存取匯出的遠端網路中任何主機的需求。  

7. 在 [ **標記** ] 索引標籤底下，視需要建立機碼/值組。  

8. 在 [ **複寫** ] 索引標籤下，貼上您在中取得的來源磁片區資源識別碼，以 [找出來源磁片區資源識別碼](#locate-the-source-volume-resource-id)，然後選取所需的複寫排程。 複寫排程的選項包括：每隔10分鐘、每小時、每日、每週和每月。  

    ![建立磁片區複寫](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. 按一下 [ **審核 + 建立**]，然後按一下 [ **建立** ] 以建立資料複寫磁片區。   

    ![檢查和建立複寫](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>授權來源磁片區的複寫  

若要授與複寫的授權，您需要取得複寫目的地磁片區的資源識別碼，並將它貼到複寫來源磁片區的授權欄位中。 

1. 在 Azure 入口網站中，流覽至 Azure NetApp Files。

2. 移至複寫目的地磁片區所在的目的地 NetApp 帳戶和目的地容量集區。

3. 選取複寫目的地磁片區，移至 [設定 **] 下的** [內容]，然後找出目的地磁片區的 **資源識別碼** 。 將目的地磁片區資源識別碼複製到剪貼簿。

    ![屬性資源識別碼](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. 在 Azure NetApp Files 中，移至複寫來源帳戶和來源容量集區。 

5. 找出複寫來源磁片區，然後選取它。 前往 [儲存體服務 **] 下的** [複寫]，然後按一下 [ **授權**]。

    ![授權複寫](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. 在 [授權] 欄位中，貼上您在步驟3取得的目的地複寫磁片區資源識別碼，然後按一下 **[確定]**。

## <a name="next-steps"></a>後續步驟  

* [跨區域複寫](cross-region-replication-introduction.md)
* [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)
* [顯示複寫關聯性的健全狀態](cross-region-replication-display-health-status.md)
* [磁片區複寫計量](azure-netapp-files-metrics.md#replication)
* [管理災害復原](cross-region-replication-manage-disaster-recovery.md)
* [刪除磁片區複製或磁片區](cross-region-replication-delete.md)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)

