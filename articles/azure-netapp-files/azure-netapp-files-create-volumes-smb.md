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
ms.date: 02/05/2020
ms.author: b-juche
ms.openlocfilehash: c65da771dd483b3a79785d4bec2b89cbeefca5c4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049893"
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

* 您使用的系統管理員帳戶必須能夠在您將指定的組織單位（OU）路徑中建立電腦帳戶。  

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

    部署 Azure NetApp Files 所在虛擬網路的位址空間，必須新增至新的或現有的 Active Directory 網站（Azure NetApp Files 可連線的網域控制站）。 

* 必須能夠從 Azure NetApp Files 的[委派子網](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)連線到指定的 DNS 伺服器。  

    如需支援的網路拓撲，請參閱[Azure NetApp Files 網路規劃的指導方針](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。

    網路安全性群組（Nsg）和防火牆必須已適當設定規則，以允許 Active Directory 和 DNS 流量要求。 

* 「Azure NetApp Files 委派的子網」必須能夠連線到網域中的所有 Active Directory Domain Services （新增）網域控制站，包括所有的本機和遠端網域控制站。 否則，可能會發生服務中斷。  

    如果您有無法透過 Azure NetApp Files 委派子網連線的網域控制站，您可以提交 Azure 支援要求，將範圍從**全域**（預設值）變更為**site**。  Azure NetApp Files 只需要與 Azure NetApp Files 委派子網位址空間所在網站中的網域控制站進行通訊。

    請參閱設計關於 AD 網站和服務[的網站拓撲](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)。 
    
如需其他廣告資訊，請參閱 Azure NetApp Files [SMB 常見問題](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)。 

## <a name="create-an-active-directory-connection"></a>建立 Active Directory 連線

1. 從您的 NetApp 帳戶中，按一下 [ **Active Directory**連線]，然後按一下 [**加入**]。  

    ![Active Directory 連接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在 [加入 Active Directory] 視窗中，提供下列資訊：

    * **主要 DNS**  
        這是 Active Directory 網域加入和 SMB 驗證作業所需的 DNS。 
    * **次要 DNS**   
        這是用來確保重複名稱服務的次要 DNS 伺服器。 
    * **網域**  
        這是您想要加入之 Active Directory Domain Services 的功能變數名稱。
    * **SMB 伺服器（電腦帳戶）首碼**  
        這是 Active Directory 中，Azure NetApp Files 將用來建立新帳戶之電腦帳戶的命名前置詞。

        例如，如果您的組織用於檔案伺服器的命名標準是 NAS-01、nas-02 ...、NAS-045，則您會輸入 "NAS" 做為前置詞。 

        服務會視需要在 Active Directory 中建立額外的電腦帳戶。

    * **組織單位路徑**  
        這是將在其中建立 SMB 伺服器電腦帳戶的組織單位（OU）的 LDAP 路徑。 也就是 OU = 第二層，OU = 第一層。 

        如果您使用具有 Azure Active Directory Domain Services 的 Azure NetApp Files，則當您設定 NetApp 帳戶的 Active Directory 時，會 `OU=AADDC Computers` 組織單位路徑。
        
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

2. 按一下 [+ 新增磁碟區] 以建立磁碟區。  
    [建立磁片區] 視窗隨即出現。

3. 在 [建立磁片區] 視窗中，按一下 [**建立**]，並提供下欄欄位的資訊：   
    * **磁片區名稱**      
        為您要建立的磁碟區指定名稱。   

        在每個容量集區中，磁片區名稱都必須是唯一的。 長度至少必須有三個字元。 您可以使用任何英數位元。   

        您不能使用 `default` 做為磁片區名稱。

    * **容量集區**  
        指定您想要在其中建立磁片區的容量集區。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額] 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定您要從中存取磁片區的 Azure 虛擬網路（VNet）。  

        您指定的 VNet 必須具有委派給 Azure NetApp Files 的子網。 Azure NetApp Files 服務只能從相同的 VNet 或透過 VNet 對等互連與磁片區位於相同區域的 VNet 存取。 您也可以透過 Express Route 從內部部署網路存取磁片區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes] 以委派 Azure NetApp Files 的子網路。 在每個 VNet 中，只有一個子網可委派給 Azure NetApp Files。   
 
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

## <a name="next-steps"></a>後續步驟  

* [針對 Windows 或 Linux 虛擬機器掛接或卸載磁片區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [SMB 常見問題](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [了解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [使用 Azure CLI 安裝新的 Active Directory 樹系](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
