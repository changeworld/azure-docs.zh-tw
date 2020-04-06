---
title: 為 Azure NetApp 檔案建立 SMB 卷 |微軟文件
description: 介紹如何為 Azure NetApp 檔案創建 SMB 卷。
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: c4e7566eeb28bc5709acd60ced9fcdffb7e8a725
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668011"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的 SMB 磁碟區

Azure NetApp 檔案支援 NFS 和 SMBv3 卷。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 本文介紹如何創建 SMBv3 卷。 如果要建立 NFS 卷,請參閱為[Azure NetApp 檔案建立 NFS 卷](azure-netapp-files-create-volumes.md)。 

## <a name="before-you-begin"></a>開始之前 
您必須已經設定容量集區。   
[設定容量池](azure-netapp-files-set-up-capacity-pool.md)   
子網路必須委派至 Azure NetApp Files。  
[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>活動目錄連線的要求

 在建立 SMB 卷之前,您需要建立活動目錄連接。 活動目錄連線的要求如下: 

* 您使用的管理員帳戶必須能夠創建要指定的組織單位 (OU) 路徑中的電腦帳戶。  

* 必須在適用的 Windows 活動目錄 (AD) 伺服器上打開正確的埠。  
    所需的埠如下所示: 

    |     服務           |     連接埠     |     通訊協定     |
    |-----------------------|--------------|------------------|
    |    AD Web 服務    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    回聲回覆    |
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

* 目標活動目錄域服務的網站拓撲必須符合最佳做法,尤其是部署 Azure NetApp 檔的 Azure VNet。  

    部署 Azure NetApp 檔案虛擬網路的位址空間必須添加到新的或現有的活動目錄網站(Azure NetApp 檔案可存取網域控制器所在的網站)。 

* 指定的 DNS 伺服器必須從 Azure NetApp 檔的[委派子網](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)進行。  

    請參閱[Azure NetApp 檔網路規劃指南](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies),瞭解支援的網路拓撲。

    網路安全組 (NSG) 和防火牆必須具有適當配置的規則,以允許活動目錄和 DNS 流量請求。 

* Azure NetApp 檔案委派子網必須能夠覆蓋網域中的所有活動目錄域服務 (ADDS) 網域控制器,包括所有本地域和遠端域控制器。 否則,可能會發生服務中斷。  

    如果 Azure NetApp 檔委派子網無法存取的網域控制器,則可以在創建活動目錄連接期間指定活動目錄網站。  Azure NetApp 檔只需要與 Azure NetApp 檔委派子網位址空間所在的網站中的域控制器通信。

    請參考設計有關 AD 網站與服務[的網站拓撲](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)。 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

有關其他 AD 資訊,請參閱 Azure NetApp 檔案[SMB 常見問題解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)。 

## <a name="decide-which-domain-services-to-use"></a>決定使用哪些域服務 

Azure NetApp 檔案支援 AD 連接[的活動目錄域服務](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)(ADDS) 和 Azure 活動目錄域服務 (AADDS)。  在建立 AD 連接之前,您需要決定是使用 ADDS 還是 AADDS。  

有關詳細資訊,請參閱[比較自管理的活動目錄域服務、Azure 活動目錄和託管 Azure 活動目錄域服務](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)。 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

您可以將首選[的活動目錄網站和服務](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)範圍用於 Azure NetApp 檔。 此選項支援讀取和寫入[Azure NetApp 檔案可存取的](azure-netapp-files-network-topologies.md)活動目錄域服務 (ADDS) 網域控制器。 它還阻止服務與不在指定活動目錄網站和服務網站中的域控制器通信。 

要在使用 ADDS 時查找網站名稱,可以聯繫組織中負責活動目錄域服務的管理組。 下面的範例顯示了顯示網站名稱的活動目錄網站和服務外掛程式: 

![Active Directory 站台及服務](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

為 Azure NetApp 檔配置 AD 連接時,請在**AD 網站名稱**欄位的作用網域中指定網站名稱。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

有關 Azure 活動目錄網域服務 (AADDS) 設定和指導,請參考[Azure AD 網域服務文件](https://docs.microsoft.com/azure/active-directory-domain-services/)。

其他 AADDS 注意事項適用於 Azure NetApp 檔案: 

* 確保部署 AADDS 的 VNet 或子網與 Azure NetApp 檔部署位於相同的 Azure 區域中。
* 如果在部署 Azure NetApp 檔的區域中使用另一個 VNet,則應在兩個 VNet 之間創建對等互連。
* Azure`user`NetApp`resource forest`檔案 支援和類型。
* 對於同步型態,您可以`All`選擇`Scoped`或 。   
    如果選擇`Scoped`,請確保為訪問 SMB 共用選擇了正確的 Azure AD 組。  如果不確定,可以使用`All`同步類型。
* 需要使用企業版或高級 SKU。 不支持標準 SKU。

建立活動目錄連接時,請注意 AADDS 的以下細節:

* 您可以在 AADDS 功能表中找到**主 DNS、****輔助 DNS**和 AD **DNS 功能變數名稱**的資訊。  
對於 DNS 伺服器,將使用兩個 IP 位址來設定活動目錄連接。 
* **組織單位路徑**為`OU=AADDC Computers`。  
這個設定在**NetApp 帳號**下的 **「動作目錄連線**」中設定:

  ![組織單位路徑](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **使用者名**認證可以是 Azure AD 組**Azure AD DC 管理員**的成員的任何使用者。


## <a name="create-an-active-directory-connection"></a>建立活動目錄連線

1. 從 NetApp 帳戶中,按一下 **「活動目錄連接**」,然後單擊「**加入**」。 。  

    ![活動目錄連線](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在「加入活動目錄」視窗中,根據要使用的域服務提供以下資訊:  

    有關特定於您使用的網域服務的資訊,請參閱[決定使用哪些網域服務](#decide-which-domain-services-to-use)。 

    * **主 DNS**  
        這是活動目錄域聯接和 SMB 身份驗證操作所需的 DNS。 
    * **輔助 DNS**   
        這是用於確保冗餘名稱服務的輔助 DNS 伺服器。 
    * **AD DNS 網域名稱**  
        這是要加入的活動目錄域服務的功能變數名稱。
    * **AD 網站名稱**  
        這是域控制器發現將限制的網站名稱。
    * **SMB 伺服器(電腦帳戶)前置字串**  
        這是 Azure NetApp 檔案將用於創建新帳戶的活動目錄中的電腦帳戶的命名首碼。

        例如,如果組織用於檔案伺服器的命名標準為 NAS-01、NAS-02...、NAS-045,則首碼將輸入"NAS"。 

        該服務將根據需要在活動目錄中創建其他計算機帳戶。

    * **組織單位路徑**  
        這是將創建 SMB 伺服器電腦帳戶的組織單位 (OU) 的 LDAP 路徑。 也就是說,OU=第二級,OU=第一級。 

        如果將 Azure NetApp 檔與 Azure 活動目錄域服務`OU=AADDC Computers`一起使用,則組織單位路徑是為 NetApp 帳戶配置活動目錄時。
        
    * 認證,包括**您的使用者名稱和密碼****password**

    ![加入活動目錄](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. 按一下 [ **加入**]。  

    將顯示您建立的活動目錄連接。

    ![活動目錄連線](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> 儲存活動目錄連接後,可以編輯使用者名和密碼欄位。 保存連接後,無法編輯任何其他值。 如果需要更改任何其他值,必須首先刪除任何已部署的 SMB 卷,然後刪除並重新創建 Active Directory 連接。

## <a name="add-an-smb-volume"></a>新增 SMB 卷

1. 按下「容量池」邊欄選項卡中的 **「捲**」邊欄選項卡。 

    ![導覽到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 按一下 [+ 新增磁碟區]**** 以建立磁碟區。  
    將顯示"創建卷"

3. 在「建立卷」視窗中,按下「**建立**並提供有關以下欄位的資訊」:   
    * **遮片名稱**      
        為您要建立的磁碟區指定名稱。   

        卷名稱在每個容量池中必須是唯一的。 長度至少必須有三個字元。 您可以使用任何字母數字字元。   

        不能用作`default`卷名稱。

    * **容量池**  
        指定要創建卷的容量池。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額]**** 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定要從中存取卷的 Azure 虛擬網路 (VNet)。  

        指定的 VNet 必須將子網委派給 Azure NetApp 檔。 Azure NetApp 檔案服務只能從同一 VNet 或透過 VNet 對等互連的卷位於與卷相同的區域的 VNet 訪問。 您還可以透過快速路由從本地網路存取卷。   

    * **子**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果尚未委派子網,則可以在"創建卷"頁上按一下 **"創建新**"。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes]**** 以委派 Azure NetApp Files 的子網路。 在每個 VNet 中,只能將一個子網委派給 Azure NetApp 檔。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 單擊 **"協定"** 並完成以下資訊:  
    * 選擇**SMB**作為卷的協議類型。 
    * 從下拉清單中選擇**活動目錄**連接。
    * 在**共用名稱**中指定共用卷的名稱。

    ![指定 SMB 協定](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 按下 **「查看 + 創建**」以查看卷詳細資訊。  然後按下「**創建**」 以創建 SMB 卷。

    您建立的卷將顯示在「捲」頁中。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="next-steps"></a>後續步驟  

* [對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [中小企業常見問題解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [瞭解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [使用 Azure CLI 安裝新的活動目錄](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
