---
title: 建立適用於 Azure NetApp Files 的 SMB | Microsoft Docs
description: 本文說明如何在 Azure NetApp Files 中建立 SMBv3 磁片區。 瞭解 Active Directory 連接和網域服務的需求。
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
ms.openlocfilehash: e2c487b62813bc4480786daa08666fe6471bd18d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325703"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的 SMB 磁碟區

Azure NetApp Files 支援使用 NFS 建立磁片區 (NFSv3 和 Nfsv4.1 4.1) 、SMBv3 或雙重通訊協定 (NFSv3 和 SMB) 。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 本文說明如何建立 SMBv3 磁碟區。

## <a name="before-you-begin"></a>開始之前 
您必須已經設定容量集區。   
[設定容量集區](azure-netapp-files-set-up-capacity-pool.md)   
子網路必須委派至 Azure NetApp Files。  
[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 連線的需求

 您必須先建立 Active Directory 連線，然後再建立 SMB 磁碟區。 Active Directory 連線的需求如下所示： 

* 您使用的系統管理員帳戶必須可在您將指定的組織單位 (OU) 路徑中，建立電腦帳戶。  

* 必須在適當的 Windows Active Directory (AD) 伺服器上開啟適用的連接埠。  
    需要的連接埠如下所示： 

    |     服務           |     連接埠     |     通訊協定     |
    |-----------------------|--------------|------------------|
    |    AD Web 服務    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Echo 回覆    |
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

* 目標 Active Directory Domain Services 的站台拓撲必須遵守指導方針，尤其是部署 Azure NetApp Files 所在的 Azure VNet。  

    部署 Azure NetApp Files 的虛擬網路位址空間必須新增至新的或現有的 Active Directory 站台 (Azure NetApp Files 可從中連線的網域控制站)。 

* 指定的 DNS 伺服器必須可從 Azure NetApp Files 的[委派子網路](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)連線。  

    如需了解支援的網路拓撲，請參閱[適用於 Azure NetApp Files 網路方案的指導方針](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。

    網路安全性群組 (NSG) 和防火牆必須已適當設定規則，以允許 Active Directory 和 DNS 流量要求。 

* Azure NetApp Files 委派子網路必須可連線網域中的所有 Active Directory Domain Services (ADDS) 網域控制站，包括所有本機和遠端的網域控制站。 否則，可能會發生服務中斷。  

    如果您有 Azure NetApp Files 委派子網路無法連線的網域控制站，您可以在建立 Active Directory 連線期間，指定 Active Directory 站台。  Azure NetApp Files 只需要與 Azure NetApp Files 委派子網路位址空間所在網站的網域控制站進行通訊。

    如需 AD 站台和服務的相關資訊，請參閱[設計站台拓撲](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)。 
    
* 您可以勾選 [[聯結 Active Directory](#create-an-active-directory-connection) ] 視窗中的 [ **aes 加密**] 方塊，為 SMB 磁片區啟用 aes 加密。 Azure NetApp Files 支援 DES、Kerberos AES 128 和 Kerberos AES 256 加密類型， (從最安全到最安全的) 。 如果您啟用 AES 加密，用來聯結 Active Directory 的使用者認證必須啟用最高對應的帳戶選項，以符合您的 Active Directory 啟用的功能。    

    例如，如果您的 Active Directory 只有 AES-128 功能，您必須為使用者認證啟用 AES-128 帳戶選項。 如果您的 Active Directory 具有 AES-256 功能，您必須啟用 AES-256 帳戶選項 (這也支援 AES-128) 。 如果您的 Active Directory 沒有任何 Kerberos 加密功能，則 Azure NetApp Files 預設會使用 DES。  

    您可以在 Active Directory 消費者和電腦 Microsoft Management Console (MMC) 的屬性中啟用帳戶選項：   

    ![Active Directory 消費者和電腦 MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files 支援 [ldap 簽署](https://docs.microsoft.com/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server)，可在 Azure NetApp files 服務與目標 [Active Directory 網域控制站](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)之間，安全地傳輸 ldap 流量。 如果您遵循 Microsoft 諮詢[ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023)進行 ldap 簽署的指導方針，則應勾選 [[聯結 Active Directory](#create-an-active-directory-connection) ] 視窗中的 [ **ldap 簽署**] 方塊，以啟用 Azure NetApp Files 中的 ldap 簽署功能。 

    [LDAP 通道](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) 系結設定不會影響 Azure NetApp Files 服務。 

如需其他 AD 資訊，請參閱 Azure NetApp Files [SMB 常見問題](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)。 

## <a name="decide-which-domain-services-to-use"></a>決定要使用的網域服務 

對於 AD 連線，Azure NetApp Files 支援 [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) 和 Azure Active Directory Domain Services (AADDS)。  建立 AD 連線之前，您必須決定要使用 ADDS 或 AADDS。  

如需詳細資訊，請參閱[比較自我管理 Active Directory Domain Services、Azure Active Directory 和受控 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)。 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

您可以針對 Azure NetApp Files 使用慣用的 [Active Directory 站台及服務](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)範圍。 此選項可讓您讀取和寫入 [Azure NetApp Files 可存取](azure-netapp-files-network-topologies.md)的  Active Directory Domain Services (ADDS) 網域控制站。 它也可以防止服務與不在指定的 Active Directory 站台及服務網站中的網域控制站進行通訊。 

若要尋找您的網站名稱，您可以與組織中負責 Active Directory Domain Services 的管理群組聯繫。 下列範例顯示網站名稱顯示所在的 Active Directory 站台及服務外掛程式： 

![Active Directory 站台及服務](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

當您為 Azure NetApp Files 設定 AD 連線時，請在 [AD 站台名稱] 欄位的範圍內，指定站台名稱。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

如需 Azure Active Directory Domain Services (AADDS) 設定和指導方針，請參閱 [Azure AD Domain Services 文件](https://docs.microsoft.com/azure/active-directory-domain-services/)。

適用於 Azure NetApp Files 的其他 AADDS 考量： 

* 請確定部署 AADDS 所在的 VNet 或子網路與 Azure NetApp Files 部署位於相同的 Azure 區域中。
* 如果您在部署 Azure NetApp Files 所在的區域中使用另一個 VNet，您應該在兩個 VNet 之間建立對等互連。
* Azure NetApp Files 支援 `user` 和 `resource forest` 類型。
* 對於同步處理類型，您可以選取 `All` 或 `Scoped`。   
    如果您選取 `Scoped`，請確定已選取正確的 Azure AD 群組來存取 SMB 共用。  如果您不確定，則可以使用 `All` 同步處理類型。
* 需要使用企業或進階 SKU。 不支援標準 SKU。

建立 Active Directory 連線時，請注意下列 AADDS 的詳細資訊：

* 您可以在 AADDS 功能表中，找到**主要 DNS**、**次要 DNS**，以及 **AD DNS 網域名稱**的資訊。  
對於 DNS 伺服器，將使用兩個 IP 位址來設定 Active Directory 連線。 
* **組織單位路徑**為 `OU=AADDC Computers`。  
此設定是在 [NetApp 帳戶] 下的 [Active Directory 連線] 中設定：

  ![組織單位路徑](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **使用者名稱**認證可以是 Azure AD 群組 **Azure AD DC 管理員**成員的任何使用者。


## <a name="create-an-active-directory-connection"></a>建立 Active Directory 連線

1. 從您的 NetApp 帳戶中，按一下 [Active Directory 連線]，然後按一下 [加入]。  

    ![Active Directory 連線](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在 [加入 Active Directory] 視窗中，根據您想要使用的網域服務，提供下列資訊：  

    如需您使用的網域服務具體資訊，請參閱[決定要使用的網域服務](#decide-which-domain-services-to-use)。 

    * **主要 DNS**  
        這是 Active Directory 網域加入和 SMB 驗證作業所需的 DNS。 
    * **次要 DNS**   
        這是用於確保備援名稱服務的次要 DNS 伺服器。 
    * **AD DNS 網域名稱**  
        這是您想要加入的 Active Directory Domain Services 的網域名稱。
    * **AD 站台名稱**  
        這是網域控制站探索將受限於的網站名稱。
    * **SMB 伺服器 (電腦帳戶) 前置詞**  
        這是 Active Directory 中電腦帳戶的命名前置詞，Azure NetApp Files 會使用該前置詞來建立新帳戶。

        例如，如果貴組織用於檔案伺服器的命名標準是 NAS-01、NAS-02...、NAS-045，則您會輸入「NAS」做為前置詞。 

        服務會視需要在 Active Directory 中建立額外的電腦帳戶。

        > [!IMPORTANT] 
        > 在建立 Active Directory 連線之後重新命名 SMB 伺服器會造成干擾。 重新命名 SMB 伺服器前置詞之後，您必須重新掛接現有的 SMB 共用。

    * **組織單位路徑**  
        這是組織單位 (OU) 的 LDAP 路徑，將在這裡建立 SMB 伺服器電腦帳戶。 也就是 OU = 第二層，OU = 第一層。 

        如果您將 Azure NetApp Files 與 Azure Active Directory Domain Services 搭配使用，當您為 NetApp 帳戶設定 Active Directory 時，組織單位路徑是 `OU=AADDC Computers`。

    ![加入 Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES 加密**   
        選取此核取方塊以啟用 SMB 磁片區的 AES 加密。 請參閱需求 [的 Active Directory 連接需求](#requirements-for-active-directory-connections) 。 

        ![Active Directory AES 加密](../media/azure-netapp-files/active-directory-aes-encryption.png)

        **AES 加密**功能目前為預覽狀態。 如果這是您第一次使用這項功能，請先註冊該功能再加以使用： 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        檢查功能註冊的狀態： 

        > [!NOTE]
        > 在變更為之前， **>registrationstate** 可能會處於 `Registering` 最多60分鐘的狀態 `Registered` 。 等到狀態 **註冊** 後再繼續。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        您也可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` ，並 `az feature show` 註冊功能並顯示註冊狀態。 

    * **LDAP 簽署**   
        選取此核取方塊以啟用 LDAP 簽署。 這種功能可在 Azure NetApp Files 服務和使用者指定的 [Active Directory Domain Services 網域控制站](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services)之間啟用安全的 LDAP 查閱。 如需詳細資訊，請參閱 [ADV190023 |啟用 LDAP 通道系結和 LDAP 簽署的 Microsoft 指導](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023)方針。  

        ![Active Directory LDAP 簽署](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        **LDAP 簽署**功能目前為預覽狀態。 如果這是您第一次使用這項功能，請先註冊該功能再加以使用： 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        檢查功能註冊的狀態： 

        > [!NOTE]
        > 在變更為之前， **>registrationstate** 可能會處於 `Registering` 最多60分鐘的狀態 `Registered` 。 等到狀態 **註冊** 後再繼續。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        您也可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` ，並 `az feature show` 註冊功能並顯示註冊狀態。 

     * **備份原則使用者**  
        您可以將需要更高權限的其他帳戶，納入為了使用 Azure NetApp Files 而建立的電腦帳戶。 指定的帳號可允許在檔案或資料夾層級變更 NTFS 權限。 例如，您可以指定非特殊權限服務帳戶，用來將資料移轉至 Azure NetApp Files 中的 SMB 檔案共用。  

        ![Active Directory 備份原則使用者](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        **備份原則使用者**功能目前為預覽狀態。 如果這是您第一次使用這項功能，請先註冊該功能再加以使用： 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        檢查功能註冊的狀態： 

        > [!NOTE]
        > 在變更為之前， **>registrationstate** 可能會處於 `Registering` 最多60分鐘的狀態 `Registered` 。 等到狀態 **註冊** 後再繼續。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        您也可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` ，並 `az feature show` 註冊功能並顯示註冊狀態。 

    * 認證，包括您的**使用者名稱**和**密碼**

        ![Active Directory 認證](../media/azure-netapp-files/active-directory-credentials.png)

3. 按一下 [ **加入**]。  

    隨即顯示您建立的 Active Directory 連線。

    ![建立 Active Directory 連接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>新增 SMB 磁碟區

1. 從 [容量集區] 刀鋒視窗按一下 [磁碟區] 刀鋒視窗。 

    ![導覽至磁碟區](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 按一下 [+ 新增磁碟區] 以建立磁碟區。  
    隨即顯示 [建立磁碟區] 視窗。

3. 在 [建立磁片區] 視窗中，按一下 [基本] 索引標籤底下的 [ **建立** ] 並提供下欄欄位的資訊：   
    * **磁碟區名稱**      
        為您要建立的磁碟區指定名稱。   

        在每個容量集區中，磁碟區名稱必須是唯一的。 長度至少必須有三個字元。 您可以使用任何英數字元。   

        您無法使用 `default` 或 `bin` 做為磁片區名稱。

    * **容量集區**  
        指定您想要在其中建立磁碟區的容量集區。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額] 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **輸送量 (MiB/秒) **   
        如果磁片區是在手動 QoS 容量集區中建立的，請指定您要用於磁片區的輸送量。   

        如果磁片區是建立在自動 QoS 容量集區中，則此欄位中顯示的值會是 (配額 x 服務層級輸送量) 。   

    * **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (VNet)。  

        您指定的 VNet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 VNet 存取，或透過 VNet 對等互連與磁碟區位於相同區域的 VNet 存取。 您也可以透過 Express Route 從內部部署網路存取磁碟區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes] 以委派 Azure NetApp Files 的子網路。 在每個 VNet 中，只有一個子網路可委派給 Azure NetApp Files。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果您想要將現有的快照集原則套用到磁片區，請按一下 [顯示最上層] **區段** 來展開它，指定是否要隱藏快照集路徑，然後在下拉式功能表中選取快照集原則。 

        如需建立快照集原則的詳細資訊，請參閱 [管理快照](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)集原則。

        ![顯示 advanced selection](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. 按一下 [通訊協定]，然後完成下列資訊：  
    * 選取 [SMB] 做為磁碟區的通訊協定類型。 
    * 從下拉式清單中，選取您的 **Active Directory** 連線。
    * 在 [共用名稱] 中，指定共用磁碟區的名稱。

    ![指定 SMB 通訊協定](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 按一下 [檢閱 + 建立]，以檢閱磁碟區詳細資料。  然後按一下 [建立]，以建立 SMB 磁碟區。

    建立的磁碟區會出現在 [磁碟區] 頁面中。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="control-access-to-an-smb-volume"></a>控制對 SMB 磁碟區的存取  

SMB 磁碟區的存取是透過權限來管理。  

### <a name="share-permissions"></a>共用權限  

根據預設，新磁碟區具有**所有人/完整控制**共用權限。 Domain Admins 群組的成員可以在用於 Azure NetApp Files 磁碟區的電腦帳戶上，使用 [電腦管理] 來變更共用權限。

![SMB 掛接路徑](../media/azure-netapp-files/smb-mount-path.png) 
![設定共用權限](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS 檔案和資料夾權限  

您可以在 Windows SMB 用戶端中，使用物件屬性的 [安全性] 索引標籤，來設定檔案或資料夾的權限。
 
![設定檔案和資料夾權限](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>後續步驟  

* [對 Windows 或 Linux 虛擬機器掛接或取消掛接磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [SMB 常見問題集](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [了解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [使用 Azure CLI 安裝新的 Active Directory 樹系](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
