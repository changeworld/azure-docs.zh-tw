---
title: 為 Azure NetApp 檔創建 NFS 卷 |微軟文檔
description: 描述如何為 Azure NetApp 檔創建 NFS 卷。
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274082"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的 NFS 磁碟區

Azure NetApp 檔支援 NFS（NFSv3 和 NFSv4.1）和 SMBv3 卷。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 本文介紹如何創建 NFS 卷。 如果要創建 SMB 卷，請參閱為[Azure NetApp 檔創建 SMB 卷](azure-netapp-files-create-volumes-smb.md)。 

## <a name="before-you-begin"></a>開始之前 
您必須已經設定容量集區。   
[設置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子網路必須委派至 Azure NetApp Files。  
[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>考量 

* 決定使用哪個 NFS 版本  
  NFSv3 可以處理各種用例，通常部署在大多數企業應用程式中。 您應該驗證應用程式需要的版本（NFSv3 或 NFSv4.1），並使用適當的版本創建卷。 例如，如果使用 Apache [ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)，建議通過 NFSv3 鎖定帶有 NFSv4.1 的檔。 

* 安全性  
  NFSv3 和 NFSv4.1 支援 UNIX 模式位（讀取、寫入和執行）。 NFS 用戶端上需要根級訪問才能裝載 NFS 卷。

* 本地使用者/組和 LDAP 支援 NFSv4.1  
  目前，NFSv4.1 僅支援對卷的根訪問。 請參閱[為 Azure NetApp 檔配置 NFSv4.1 預設域](azure-netapp-files-configure-nfsv41-domain.md)。 

## <a name="best-practice"></a>最佳做法

* 應確保對卷使用正確的裝載說明。  請參閱[為 Windows 或 Linux 虛擬機器安裝或卸載卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。

* NFS 用戶端應與 Azure NetApp 檔卷位於同一 VNet 或對等 VNet 中。 支援從 VNet 外部進行連接;但是，它會帶來額外的延遲並降低整體性能。

* 應確保 NFS 用戶端是最新的，並運行作業系統的最新更新。

## <a name="create-an-nfs-volume"></a>創建 NFS 卷

1.  按一下"容量池"邊欄選項卡中的 **"卷**"邊欄選項卡。 

    ![導航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  按一下 [+ 新增磁碟區]**** 以建立磁碟區。  
    將顯示"創建卷"視窗。

3.  在"創建卷"視窗中，按一下"**創建**並提供有關以下欄位的資訊"：   
    * **卷名稱**      
        為您要建立的磁碟區指定名稱。   

        卷名稱在每個容量池中必須是唯一的。 長度至少必須有三個字元。 您可以使用任何字母數位字元。   

        不能用作`default`卷名稱。

    * **容量池**  
        指定要創建卷的容量池。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額]**** 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (Vnet)。  

        您指定的 Vnet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 Vnet 存取，或透過 Vnet 對等互連與磁碟區位於相同區域的 Vnet 存取。 您還可以通過快速路由從本地網路訪問卷。   

    * **子**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]****。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes]**** 以委派 Azure NetApp Files 的子網路。 在每個 Vnet 中，只能將一個子網委派給 Azure NetApp 檔。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 按一下 [通訊協定]****，然後完成下列動作：  
    * 選取 [NFS]**** 作為磁碟區的通訊協定類型。   
    * 指定將用於為新卷創建匯出路徑**的檔路徑**。 匯出路徑會用來掛接和存取磁碟區。

        檔案路徑名稱只能包含字母、數字和連字號 ("-")。 長度必須介於 16 到 40 個字元之間。 

        檔路徑在每個訂閱和每個區域中必須是唯一的。 

    * 選取磁碟區的 NFS 版本 (**NFSv3** 或 **NFSv4.1**)。  
    * 或者，[為 NFS 卷設定匯出策略](azure-netapp-files-configure-export-policy.md)。

    ![指定 NFS 協定](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. 按一下 **"查看 + 創建**"以查看卷詳細資訊。  然後按一下 **"創建**"以創建 NFS 卷。

    您創建的卷將顯示在"卷"頁中。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。


## <a name="next-steps"></a>後續步驟  

* [針對 Azure NetApp Files 設定 NFSv4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* [對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [設定 NFS 磁碟區的匯出原則](azure-netapp-files-configure-export-policy.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [瞭解 Azure 服務的虛擬網路集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
