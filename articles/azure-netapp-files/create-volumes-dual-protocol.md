---
title: 建立適用于 Azure NetApp Files 的雙重通訊協定 (NFSv3 和 SMB) 磁片區 |Microsoft Docs
description: 描述如何建立磁片區，以使用 NFSv3 和 SMB 的雙重通訊協定，並支援 LDAP 使用者對應。
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
ms.date: 8/11/2020
ms.author: b-juche
ms.openlocfilehash: f4cc253de0de9d099cfc4881f48182cf9b2a1616
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134576"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>建立適用于 Azure NetApp Files 的雙重通訊協定 (NFSv3 和 SMB) 磁片區

Azure NetApp Files 支援使用 NFS (NFSv3 和 NFSv 4.1) 、SMBv3 或雙重通訊協定來建立磁片區。 本文說明如何建立磁片區，以使用 NFSv3 和 SMB 的雙重通訊協定，並支援 LDAP 使用者對應。  


## <a name="before-you-begin"></a>開始之前 

* 您必須已經設定容量集區。  
    請參閱[設定容量](azure-netapp-files-set-up-capacity-pool.md)集區。   
* 子網路必須委派至 Azure NetApp Files。  
    請參閱[將子網委派給 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)。

## <a name="considerations"></a>考量

* 請確定您符合[Active Directory 連接的需求](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)。 
* 在 DNS 伺服器上建立反向對應區域，然後在該反向對應區域中新增 AD 主機電腦 (PTR) 記錄的指標。 否則，雙重通訊協定磁片區的建立將會失敗。
* 請確定 NFS 用戶端為最新狀態，並執行作業系統的最新更新。

## <a name="create-a-dual-protocol-volume"></a>建立雙重通訊協定磁碟區

1.  從 [容量集區] 刀鋒視窗按一下 [磁碟區] 刀鋒視窗。 按一下 [+ 新增磁碟區] 以建立磁碟區。 

    ![導覽至磁碟區](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  在 [建立磁片區] 視窗中，按一下 [**建立**]，然後在 [基本] 索引標籤下提供下欄欄位的資訊：   
    * **磁碟區名稱**      
        為您要建立的磁碟區指定名稱。   

        在每個容量集區中，磁碟區名稱必須是唯一的。 長度至少必須有三個字元。 您可以使用任何英數字元。   

        您不能使用 `default` 做為磁片區名稱。

    * **容量集區**  
        指定您想要在其中建立磁碟區的容量集區。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額] 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (VNet)。  

        您指定的 Vnet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 Vnet 存取，或透過 Vnet 對等互連與磁碟區位於相同區域的 Vnet 存取。 您也可以透過 Express Route 從內部部署網路存取磁碟區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]****。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes] 以委派 Azure NetApp Files 的子網路。 在每個 Vnet 中，只有一個子網可委派給 Azure NetApp Files。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果您想要將現有的快照集原則套用到磁片區，請按一下 [**顯示 advanced] 區段**將它展開，然後選取下拉式功能表中的快照集原則。 

        如需建立快照集原則的相關資訊，請參閱[管理快照集原則](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)。

        ![顯示先進的選擇](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. 按一下 [通訊協定]  ，然後完成下列動作：  
    * 選取 [**雙重通訊協定 (NFSv3] 和 [SMB) ** ] 作為磁片區的通訊協定類型。   

    * 從下拉式清單中選取 [ **Active Directory**連接]。  
    您使用的 Active Directory 必須有伺服器根 CA 憑證。 

    * 指定磁片區的**磁片區路徑**。   
    此磁片區路徑是共用磁片區的名稱。 名稱的開頭必須是字母字元，而且在每個訂用帳戶和每個區域中必須是唯一的。  

    * 指定要使用的**安全性樣式**： NTFS (預設) 或 UNIX。

    * （選擇性）[設定磁片區的匯出原則](azure-netapp-files-configure-export-policy.md)。

    ![指定雙重通訊協定](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. 按一下 [檢閱 + 建立]，以檢閱磁碟區詳細資料。 然後按一下 [**建立**] 以建立磁片區。

    建立的磁碟區會出現在 [磁碟區] 頁面中。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>上傳 Active Directory 憑證授權單位單位公用根憑證  

1.  依照[[安裝憑證授權單位](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority)單位]，安裝並設定 [新增] 憑證授權單位單位。 

2.  遵循[[使用 mmc 嵌入式管理單元來查看憑證](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)]，以使用 mmc 嵌入式管理單元和憑證管理員工具。  
    使用 [憑證管理員] 嵌入式管理單元來尋找本機裝置的根或頒發證書。 您應該從下列其中一個設定執行 [憑證管理] 嵌入式管理單元命令：  
    * 已加入網域並已安裝根憑證的 Windows 用戶端 
    * 網域中包含根憑證的另一部電腦  

3. 匯出根憑證。  
    請確定憑證是以 Base-64 編碼的 x.509 ( 匯出。CER) 格式： 

    ![憑證匯出精靈](../media/azure-netapp-files/certificate-export-wizard.png)

4. 移至雙重通訊協定磁片區的 NetApp 帳戶，按一下 [ **Active Directory**連線]，然後使用 [**加入 Active Directory** ] 視窗上傳根 CA 憑證：  

    ![伺服器根 CA 憑證](../media/azure-netapp-files/server-root-ca-certificate.png)

    確定 DNS 可以解析憑證授權單位單位名稱。 此名稱是憑證上的「發行者」或「簽發者」欄位：  

    ![憑證資訊](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>管理 LDAP POSIX 屬性

您可以使用 [Active Directory 使用者和電腦] MMC 嵌入式管理單元來管理 POSIX 屬性，例如 UID、主目錄和其他值。  下列範例顯示 Active Directory 屬性編輯器：  

![Active Directory 屬性編輯器](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>設定 NFS 用戶端 

依照[設定適用于 Azure NetApp Files 的 nfs 客戶](configure-nfs-clients.md)端中的指示來設定 nfs 用戶端。  

## <a name="next-steps"></a>後續步驟  

* [雙重通訊協定常見問題](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [設定 Azure NetApp Files 的 NFS 用戶端](configure-nfs-clients.md) 
