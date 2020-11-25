---
title: 建立適用于 Azure NetApp Files 的 NFS 磁片區 |Microsoft Docs
description: 本文說明如何在 Azure NetApp Files 中建立 NFS 磁片區。 瞭解考慮，例如要使用的版本和最佳作法。
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
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: e88cf0c063b3ba42dadd72c19c23be8fcf919b86
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006605"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的 NFS 磁碟區

Azure NetApp Files 支援使用 NFS 建立磁片區 (NFSv3 和 Nfsv4.1 4.1) 、SMBv3 或雙重通訊協定 (NFSv3 和 SMB) 。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 本文說明如何建立 NFS 磁片區。 

## <a name="before-you-begin"></a>開始之前 
* 您必須已經設定容量集區。  
    請參閱 [設定容量集](azure-netapp-files-set-up-capacity-pool.md)區。   
* 子網路必須委派至 Azure NetApp Files。  
    請參閱 [將子網委派給 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)。

## <a name="considerations"></a>考量 

* 決定要使用的 NFS 版本  
  NFSv3 可以處理各種使用案例，而且通常會部署在大部分的企業應用程式中。 您應驗證應用程式所需)  (NFSv3 或 Nfsv4.1 4.1 版本，並使用適當的版本建立您的磁片區。 例如，如果您使用 [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)，建議使用 nfsv4.1 4.1 的檔案鎖定，而不是 NFSv3。 

* 安全性  
  NFSv3 和 Nfsv4.1 4.1 支援 UNIX 模式位 (讀取、寫入和執行) 。 NFS 用戶端上需要有根層級的存取權，才能掛接 NFS 磁片區。

* Nfsv4.1 4.1 的本機使用者/群組和 LDAP 支援  
  目前，Nfsv4.1 4.1 僅支援存取磁片區的根目錄。 請參閱 [為 Azure NetApp Files 設定 nfsv4.1 4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)。 

## <a name="best-practice"></a>最佳做法

* 確定您使用的是適當的磁片區掛接指示。  請參閱 [為 Windows 或 Linux 虛擬機器掛接或卸載磁片](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)區。

* NFS 用戶端應該與 Azure NetApp Files 磁片區位於相同的 VNet 或對等互連 VNet 中。 支援從 VNet 外部連接;不過，它會導致額外的延遲並降低整體效能。

* 確定 NFS 用戶端為最新狀態，並執行作業系統的最新更新。

## <a name="create-an-nfs-volume"></a>建立 NFS 磁碟區

1.  從 [容量集區] 刀鋒視窗按一下 [磁碟區] 刀鋒視窗。 按一下 [+ 新增磁碟區] 以建立磁碟區。 

    ![導覽至磁碟區](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  在 [建立磁片區] 視窗中，按一下 [ **建立**]，然後在 [基本] 索引標籤底下提供下欄欄位的資訊：   
    * **磁碟區名稱**      
        為您要建立的磁碟區指定名稱。   

        在每個容量集區中，磁碟區名稱必須是唯一的。 長度至少必須有三個字元。 您可以使用任何英數字元。   

        您無法使用 `default` 或 `bin` 做為磁片區名稱。

    * **容量集區**  
        指定您想要在其中建立磁碟區的容量集區。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額] 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **輸送量 (MiB/秒)**   
        如果磁片區是在手動 QoS 容量集區中建立的，請指定您要用於磁片區的輸送量。   

        如果磁片區是建立在自動 QoS 容量集區中，則此欄位中顯示的值會是 (配額 x 服務層級輸送量) 。   

    * **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (VNet)。  

        您指定的 Vnet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 Vnet 存取，或透過 Vnet 對等互連與磁碟區位於相同區域的 Vnet 存取。 您也可以透過 Express Route 從內部部署網路存取磁碟區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes] 以委派 Azure NetApp Files 的子網路。 在每個 Vnet 中，只有一個子網可委派給 Azure NetApp Files。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果您想要將現有的快照集原則套用到磁片區，請按一下 [顯示最上層] **區段** 來展開它，指定是否要隱藏快照集路徑，然後在下拉式功能表中選取快照集原則。 

        如需建立快照集原則的詳細資訊，請參閱 [管理快照](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)集原則。

        ![顯示 advanced selection](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. 按一下 [通訊協定]  ，然後完成下列動作：  
    * 選取 [NFS]  作為磁碟區的通訊協定類型。   
    * 指定將用來建立新磁片區匯出路徑的檔案 **路徑** 。 匯出路徑會用來掛接和存取磁碟區。

        檔案路徑名稱只能包含字母、數字和連字號 ("-")。 長度必須介於 16 到 40 個字元之間。 

        檔案路徑在每個訂用帳戶和每個區域內必須是唯一的。 

    * 選取磁碟區的 NFS 版本 (**NFSv3** 或 **NFSv4.1**)。  

    * 如果您使用的是 Nfsv4.1 4.1，請指出您是否要為磁片區啟用 **Kerberos** 加密。  

        如果您搭配使用 Kerberos 與 Nfsv4.1 4.1，則需要額外的設定。 遵循 [設定 nfsv4.1 4.1 Kerberos 加密](configure-kerberos-encryption.md)中的指示。

    * （選擇性） [設定 NFS 磁片區的匯出原則](azure-netapp-files-configure-export-policy.md)。

    ![指定 NFS 通訊協定](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. 按一下 [檢閱 + 建立]，以檢閱磁碟區詳細資料。  然後按一下 [ **建立** ] 以建立磁片區。

    建立的磁碟區會出現在 [磁碟區] 頁面中。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。


## <a name="next-steps"></a>後續步驟  

* [針對 Azure NetApp Files 設定 NFSv4.1 預設網域](azure-netapp-files-configure-nfsv41-domain.md)
* [設定 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md)
* [對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [設定 NFS 磁碟區的匯出原則](azure-netapp-files-configure-export-policy.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [了解 Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)