---
title: 建立適用于 Azure NetApp Files 的 SMB 磁片區 |Microsoft Docs
description: 說明如何建立適用于 Azure NetApp Files 的 SMB 磁片區。
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
ms.date: 04/30/2020
ms.author: b-juche
ms.openlocfilehash: 7dfc17825fab6c9a5f0d832318cb1d57271c56da
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625513"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的 SMB 磁碟區

Azure NetApp Files 支援 NFS 和 SMBv3 磁片區。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 本文說明如何建立 SMBv3 磁片區。 如果您想要建立 NFS 磁片區，請參閱[建立適用于 Azure NetApp Files 的 nfs 磁片](azure-netapp-files-create-volumes.md)區。 

## <a name="before-you-begin"></a>開始之前 
您必須已經設定容量集區。   
[設定容量集區](azure-netapp-files-set-up-capacity-pool.md)   
子網路必須委派至 Azure NetApp Files。  
[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 連接的需求

 您必須先建立 Active Directory 連線，才能建立 SMB 磁片區。 Active Directory 連接的需求如下所示： 

* 您使用的系統管理員帳戶必須具有在您將指定的組織單位（OU）路徑中建立電腦帳戶的功能。  

* 適當的埠必須在適用的 Windows Active Directory （AD）伺服器上開啟。  
    必要的埠如下所示： 

    |     服務           |     連接埠     |     通訊協定     |
    |-----------------------|--------------|------------------|
    |    AD Web 服務    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    回應回復    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 名稱       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* 目標 Active Directory Domain Services 的網站拓朴必須遵守最佳作法，特別是部署 Azure NetApp Files 的 Azure VNet。  

    部署 Azure NetApp Files 的虛擬網路位址空間必須新增至新的或現有的 Active Directory 網站（Azure NetApp Files 可連線的網域控制站為）。 

* 必須能夠從 Azure NetApp Files 的[委派子網](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)連線到指定的 DNS 伺服器。  

    如需支援的網路拓撲，請參閱[Azure NetApp Files 網路規劃的指導方針](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。

    網路安全性群組（Nsg）和防火牆必須已適當設定規則，以允許 Active Directory 和 DNS 流量要求。 

* 「Azure NetApp Files 委派的子網」必須能夠連線到網域中的所有 Active Directory Domain Services （新增）網域控制站，包括所有的本機和遠端網域控制站。 否則，可能會發生服務中斷。  

    如果您有 Azure NetApp Files 委派子網無法連線的網域控制站，您可以在建立 Active Directory 連線期間指定 Active Directory 網站。  Azure NetApp Files 只需要與 Azure NetApp Files 委派子網位址空間所在網站中的網域控制站進行通訊。

    請參閱設計關於 AD 網站和服務[的網站拓撲](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)。 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

如需其他廣告資訊，請參閱 Azure NetApp Files [SMB 常見問題](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)。 

## <a name="decide-which-domain-services-to-use"></a>決定要使用的網域服務 

Azure NetApp Files 支援 AD 連線的[Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) （新增）和 AZURE ACTIVE DIRECTORY DOMAIN SERVICES （AADDS）。  建立 AD 連線之前，您必須決定是否要使用新增或 AADDS。  

如需詳細資訊，請參閱[比較自我管理的 Active Directory Domain Services、Azure Active Directory 和受控 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)。 

### <a name="active-directory-domain-services"></a>Active Directory 網域服務

您可以針對 Azure NetApp Files 使用慣用的[Active Directory 網站和服務](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)範圍。 此選項可讓您讀取和寫入可[由 Azure NetApp Files 存取](azure-netapp-files-network-topologies.md)的 ACTIVE DIRECTORY DOMAIN SERVICES （新增）網域控制站。 它也會防止服務與不在指定的 Active Directory 網站和服務網站中的網域控制站進行通訊。 

當您使用 [新增] 時，若要尋找您的網站名稱，您可以與組織中負責 Active Directory Domain Services 的系統管理群組聯繫。 下列範例顯示網站名稱顯示所在的 Active Directory 網站和服務外掛程式： 

![Active Directory 站台及服務](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

當您設定 Azure NetApp Files 的 AD 連線時，可以在 [ **AD 網站名稱**] 欄位的 [範圍] 中指定網站名稱。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

如 Azure Active Directory Domain Services （AADDS）設定和指導方針，請參閱[Azure AD Domain Services 檔](https://docs.microsoft.com/azure/active-directory-domain-services/)。

適用于 Azure NetApp Files 的其他 AADDS 考慮： 

* 請確定 AADDS 部署所在的 VNet 或子網與 Azure NetApp Files 部署位於相同的 Azure 區域中。
* 如果您在 Azure NetApp Files 部署所在的區域中使用另一個 VNet，您應該在兩個 Vnet 之間建立對等互連。
* Azure NetApp Files 支援`user`和`resource forest`類型。
* 針對 [同步處理類型]， `All`您`Scoped`可以選取或。   
    如果您選取`Scoped`，請確定已選取正確的 Azure AD 群組來存取 SMB 共用。  如果您不確定，可以使用`All`同步處理類型。
* 需要使用 Enterprise 或 Premium SKU。 不支援標準 SKU。

當您建立 Active Directory 連線時，請注意下列 AADDS 的詳細資訊：

* 您可以在 [AADDS] 功能表中找到**主要 dns**、**次要 DNS**和**AD DNS 功能變數名稱**的資訊。  
針對 DNS 伺服器，會使用兩個 IP 位址來設定 Active Directory 連接。 
* **組織單位路徑**為`OU=AADDC Computers`。  
此設定是在 [ **NetApp Account**] 下的 [ **Active Directory**連線] 中設定：

  ![組織單位路徑](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* 使用者**名稱**認證可以是 Azure AD 群組**Azure AD DC 系統管理員**成員的任何使用者。


## <a name="create-an-active-directory-connection"></a>建立 Active Directory 連線

1. 從您的 NetApp 帳戶中，按一下 [ **Active Directory**連線]，然後按一下 [**加入**]。  

    ![Active Directory 連接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在 [加入 Active Directory] 視窗中，根據您想要使用的網域服務，提供下列資訊：  

    如需您所使用之網域服務的特定資訊，請參閱[決定要使用的網域服務](#decide-which-domain-services-to-use)。 

    * **主要 DNS**  
        這是 Active Directory 網域加入和 SMB 驗證作業所需的 DNS。 
    * **次要 DNS**   
        這是用來確保重複名稱服務的次要 DNS 伺服器。 
    * **AD DNS 功能變數名稱**  
        這是您想要加入之 Active Directory Domain Services 的功能變數名稱。
    * **AD 網站名稱**  
        這是網域控制站探索將受限於的網站名稱。
    * **SMB 伺服器（電腦帳戶）首碼**  
        這是 Active Directory 中，Azure NetApp Files 將用來建立新帳戶之電腦帳戶的命名前置詞。

        例如，如果您的組織用於檔案伺服器的命名標準是 NAS-01、nas-02 ...、NAS-045，則您會輸入 "NAS" 做為前置詞。 

        服務會視需要在 Active Directory 中建立額外的電腦帳戶。

    * **組織單位路徑**  
        這是將在其中建立 SMB 伺服器電腦帳戶的組織單位（OU）的 LDAP 路徑。 也就是 OU = 第二層，OU = 第一層。 

        如果您使用具有 Azure Active Directory Domain Services 的 Azure NetApp Files，則當您設定 NetApp `OU=AADDC Computers`帳戶的 Active Directory 時，組織單位路徑就會是。
        
    * 認證，包括您的使用者**名稱**和**密碼**

    ![加入 Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. 按一下 [ **加入**]。  

    您建立的 Active Directory 連接隨即出現。

    ![Active Directory 連接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> 儲存 Active Directory 連接之後，您可以編輯 [使用者名稱] 和 [密碼] 欄位。 儲存連接之後，就無法編輯其他值。 如果您需要變更任何其他值，您必須先刪除任何已部署的 SMB 磁片區，然後刪除並重新建立 Active Directory 連接。

## <a name="add-an-smb-volume"></a>新增 SMB 磁片區

1. 按一下 [容量集區] 分頁中的 [**磁片**區] 分頁。 

    ![流覽至磁片區](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 按一下 [+ 新增磁碟區]**** 以建立磁碟區。  
    [建立磁片區] 視窗隨即出現。

3. 在 [建立磁片區] 視窗中，按一下 [**建立**]，並提供下欄欄位的資訊：   
    * **磁片區名稱**      
        為您要建立的磁碟區指定名稱。   

        在每個容量集區中，磁片區名稱都必須是唯一的。 長度至少必須有三個字元。 您可以使用任何英數位元。   

        您不能`default`使用做為磁片區名稱。

    * **容量集區**  
        指定您想要在其中建立磁片區的容量集區。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額]**** 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定您要從中存取磁片區的 Azure 虛擬網路（VNet）。  

        您指定的 VNet 必須具有委派給 Azure NetApp Files 的子網。 Azure NetApp Files 服務只能從相同的 VNet 或透過 VNet 對等互連與磁片區位於相同區域的 VNet 存取。 您也可以透過 Express Route 從內部部署網路存取磁片區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網，您可以按一下 [建立磁片區] 頁面上的 [**建立新**的]。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes]**** 以委派 Azure NetApp Files 的子網路。 在每個 VNet 中，只有一個子網可委派給 Azure NetApp Files。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 按一下 [**通訊協定**] 並完成下列資訊：  
    * 選取 [ **SMB** ] 作為磁片區的通訊協定類型。 
    * 從下拉式清單中選取您的**Active Directory**連接。
    * 在 [**共用名稱**] 中，指定共用磁片區的名稱。

    ![指定 SMB 通訊協定](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 按一下 [**檢查 + 建立**] 以查看磁片區詳細資料。  然後按一下 [**建立**] 來建立 SMB 磁片區。

    您所建立的磁片區會出現在 [磁片區] 頁面中。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="control-access-to-an-smb-volume"></a>控制對 SMB 磁片區的存取  

SMB 磁片區的存取權是透過許可權來管理。  

### <a name="share-permissions"></a>共用權限  

根據預設，新的磁片區具有 [**所有人]/[完全控制**] 共用許可權。 Domain Admins 群組的成員可以在用於 Azure NetApp Files 磁片區的電腦帳戶上，使用 [電腦管理] 來變更共用許可權。

![SMB 掛接路徑](../media/azure-netapp-files/smb-mount-path.png) 
![設定共用許可權](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS 檔案和資料夾許可權  

您可以使用 Windows SMB 用戶端中物件屬性的 [**安全性**] 索引標籤，設定檔案或資料夾的許可權。
 
![設定檔案和資料夾許可權](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>後續步驟  

* [對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [SMB 常見問題](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [瞭解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [使用 Azure CLI 安裝新的 Active Directory 樹系](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
