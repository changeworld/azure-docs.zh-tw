---
title: 建立適用于 Azure NetApp Files 的雙重通訊協定 (NFSv3 和 SMB) 磁片區 |Microsoft Docs
description: 說明如何建立使用 NFSv3 和 SMB 雙重通訊協定的磁片區，並提供 LDAP 使用者對應的支援。
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
ms.date: 01/12/2020
ms.author: b-juche
ms.openlocfilehash: c914ab007f482e4d2b560b1cb461e27d4f4442ec
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133152"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>建立適用于 Azure NetApp Files 的雙重通訊協定 (NFSv3 和 SMB) 磁片區

Azure NetApp Files 支援使用 NFS (NFSv3 和 Nfsv4.1 4.1) 、SMB3 或雙協定來建立磁片區。 本文說明如何建立使用 NFSv3 和 SMB 雙重通訊協定的磁片區，並提供 LDAP 使用者對應的支援。  


## <a name="before-you-begin"></a>開始之前 

* 您必須已建立容量集區。  
    請參閱 [設定容量集](azure-netapp-files-set-up-capacity-pool.md)區。   
* 子網路必須委派至 Azure NetApp Files。  
    請參閱 [將子網委派給 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)。

## <a name="considerations"></a>考量

* 確定您符合 [Active Directory 連接的需求](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)。 
* 在 DNS 伺服器上建立反向對應區域，然後在該反向對應區域中新增 AD 主機電腦的指標 (PTR) 記錄。 否則，建立雙重通訊協定磁片區將會失敗。
* 請確定 NFS 用戶端為最新狀態，並執行作業系統的最新更新。
* 確定 ad) 的 Active Directory (AD LDAP 伺服器已啟動且正在執行。 您可以在 AD 電腦上安裝並設定 [Active Directory 輕量型目錄服務 (AD LDS) ](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) 角色來這麼做。
* 雙通訊協定磁片區目前不支援 Azure Active Directory Domain Services (AADDS) 。  
* 雙通訊協定磁片區所使用的 NFS 版本是 NFSv3。 因此，適用下列考慮：
    * 雙重通訊協定不支援 NFS 用戶端的 Windows ACL 延伸屬性 `set/get` 。
    * NFS 用戶端無法變更 NTFS 安全性樣式的許可權，而且 Windows 用戶端無法變更 UNIX 樣式雙重通訊協定磁片區的許可權。   

    下表說明安全性樣式及其效果：  
    
    | 安全性樣式    | 可以修改許可權的用戶端   | 用戶端可以使用的許可權  | 產生的有效安全性樣式    | 可以存取檔案的用戶端     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 模式位   | UNIX  | NFS 和 Windows   |
    | `Ntfs`    | Windows   | NTFS Acl     | NTFS  |NFS 和 Windows|
* 使用 NFS 裝載 NTFS 安全性樣式磁片區的 UNIX 使用者，將會被驗證為 `root` 適用于 UNIX 的 Windows 使用者 `root` 和 `pcuser` 所有其他使用者。 使用 NFS 時，請確定這些使用者帳戶都存在於您的 Active Directory 中。 


## <a name="create-a-dual-protocol-volume"></a>建立雙重通訊協定磁碟區

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
    * 選取 [ **雙通訊協定 (NFSv3] 和 [SMB)** ] 作為磁片區的通訊協定類型。   

    * 指定磁片區的 **磁片區路徑** 。   
    此磁片區路徑是共用磁片區的名稱。 名稱必須以字母字元開頭，而且在每個訂用帳戶和每個區域內必須是唯一的。  

    * 指定要使用的 **安全性樣式** ： NTFS (預設) 或 UNIX。

    * （選擇性） [設定磁片區的匯出原則](azure-netapp-files-configure-export-policy.md)。

    ![指定雙協定](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. 按一下 [檢閱 + 建立]，以檢閱磁碟區詳細資料。 然後按一下 [ **建立** ] 以建立磁片區。

    建立的磁碟區會出現在 [磁碟區] 頁面中。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="manage-ldap-posix-attributes"></a>管理 LDAP POSIX 屬性

您可以使用 Active Directory 消費者和電腦 MMC 嵌入式管理單元來管理 POSIX 屬性，例如 UID、Home 目錄和其他值。  下列範例顯示 Active Directory 屬性編輯器：  

![Active Directory 屬性編輯器](../media/azure-netapp-files/active-directory-attribute-editor.png) 

您必須為 LDAP 使用者和 LDAP 群組設定下列屬性： 
* LDAP 使用者的必要屬性：   
    `uid`： Alice、 `uidNumber` ：139、 `gidNumber` ：555、 `objectClass` ： posixAccount
* LDAP 群組的必要屬性：   
    `objectClass`： "posixGroup"、 `gidNumber` ：555

## <a name="configure-the-nfs-client"></a>設定 NFS 用戶端 

遵循 [設定適用于 Azure NetApp Files 的 nfs 用戶端](configure-nfs-clients.md) 來設定 nfs 用戶端的指示。  

## <a name="next-steps"></a>後續步驟  

* [設定 Azure NetApp Files 的 NFS 用戶端](configure-nfs-clients.md)
* [針對雙重通訊協定磁碟區進行疑難排解](troubleshoot-dual-protocol-volumes.md)
