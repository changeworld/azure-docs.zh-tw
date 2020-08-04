---
title: 針對 Azure NetApp Files 設定 NFSv 4.1 Kerberos 加密 |Microsoft Docs
description: 說明如何針對 Azure NetApp Files 設定 NFSv 4.1 Kerberos 加密，以及對效能的影響。
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
ms.date: 07/27/2020
ms.author: b-juche
ms.openlocfilehash: 05d173b715a8bc060e2f4d9cdcc7e3aef5630109
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535341"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>設定適用于 Azure NetApp Files 的 NFSv 4.1 Kerberos 加密

Azure NetApp Files 支援 Kerberos 模式（krb5、krb5i 和 krb5p）的 NFS 用戶端加密，使用 AES-256 加密。 本文說明搭配 Kerberos 加密使用 NFSv 4.1 磁片區所需的設定。

## <a name="requirements"></a>需求

下列需求適用于 NFSv 4.1 用戶端加密： 

* Active Directory Domain Services （AD DS）連接以協助 Kerberos 票證 
* 用戶端和 Azure NetApp Files NFS 伺服器 IP 位址的 DNS A/PTR 記錄建立
* Linux 用戶端  
    本文提供 RHEL 和 Ubuntu 用戶端的指引。  其他用戶端將會使用類似的設定步驟。 
* NTP 伺服器存取  
    您可以使用其中一個常用的 Active Directory 網網域控制站（AD DC）網域控制站。

## <a name="create-an-nfs-kerberos-volume"></a>建立 NFS Kerberos 磁片區

1.  依照[建立適用于 Azure NetApp Files 的 NFS 磁片](azure-netapp-files-create-volumes.md)區中的步驟來建立 nfsv 4.1 磁片區。   

    在 [建立磁片區] 頁面上，將 [NFS 版本] 設定為**nfsv 4.1**，並將 [Kerberos] 設定為 [**已啟用**]。

    > [!IMPORTANT] 
    > 建立磁片區之後，您就無法修改 Kerberos 啟用選項。

    ![建立 NFSv 4.1 Kerberos 磁片區](../media/azure-netapp-files/create-kerberos-volume.png)  

2. 選取 [**匯出原則**]，以符合磁片區所需的存取和安全性選項層級（kerberos 5、kerberos 5I 或 kerberos 5p）。   

    如需 Kerberos 的效能影響，請參閱[nfsv 4.1 上 kerberos 的效能影響](#kerberos_performance)。  

    您也可以在 Azure NetApp Files 流覽窗格中，按一下 [匯出原則] 來修改磁片區的 Kerberos 安全性方法。

3.  按一下 [**檢查 + 建立**] 以建立 nfsv 4.1 磁片區。

## <a name="configure-the-azure-portal"></a>設定 Azure 入口網站 

1.  遵循[建立 Active Directory 連接](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)中的指示。  

    Kerberos 要求您在 Active Directory 中至少建立一個電腦帳戶。 您提供的帳戶資訊可用來建立 SMB*和*Nfsv 4.1 Kerberos 磁片區的帳戶。 此機器是在磁片區建立期間自動建立的帳戶。

2.  在 [ **Kerberos 領域**] 底下，輸入**AD 伺服器名稱**和**KDC IP**位址。

    AD 伺服器和 KDC IP 可以是相同的伺服器。 這項資訊是用來建立 Azure NetApp Files 所使用的 SPN 機器帳戶。 建立機器帳戶之後，Azure NetApp Files 會視需要使用 DNS 伺服器記錄來尋找額外的 KDC 伺服器。 

    ![Kerberos 領域](../media/azure-netapp-files/kerberos-realm.png)
 
3.  按一下 [**加入**] 以儲存設定。

## <a name="configure-active-directory-connection"></a>設定 Active Directory 連線 

設定 NFSv 4.1 Kerberos 會在 Active Directory 中建立兩個電腦帳戶：
* SMB 共用的電腦帳戶
* NFSv 4.1 的電腦帳戶--您可以藉由前置詞來識別此帳戶 `NFS-` 。 

建立第一個 NFSv 4.1 Kerberos 磁片區之後，請使用下列 PowerShell 命令來設定加密類型或電腦帳戶：

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>設定 NFS 用戶端 

依照[設定適用于 Azure NetApp Files 的 nfs 客戶](configure-nfs-clients.md)端中的指示來設定 nfs 用戶端。  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>掛接 NFS Kerberos 磁片區

1. 從 [**磁片**區] 頁面中，選取您想要掛接的 NFS 磁片區。

2. 從磁片區選取 [**掛接指示**] 以顯示指示。

    例如： 

    ![Kerberos 磁片區的掛接指示](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 建立新磁片區的目錄（掛接點）。  

4. 針對電腦帳戶，將預設加密類型設定為 AES 256：  
    `Set-ADComputer $COMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * 您只需要針對每個電腦帳戶執行此命令一次。
    * 您可以從網域控制站或安裝[RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems)的電腦執行此命令。 
    * `$COMPUTERACCOUNT`變數是當您部署 Kerberos 磁片區時，在 Active Directory 中建立的電腦帳戶。 這是前面加上的帳戶 `NFS-` 。 
    * `$ANFSERVICEACCOUNT`變數是一個不具特殊許可權的 Active Directory 使用者帳戶，其具有已建立電腦帳戶之組織單位上的委派控制項。 

5. 在主機上裝載磁片區： 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`變數是 `host:/export` 在掛接指示中找到的路徑。
    * `$ANFMOUNTPOINT`變數是 Linux 主機上使用者建立的資料夾。

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Kerberos on NFSv 4.1 的效能影響 

本節可協助您瞭解 Kerberos 在 NFSv 4.1 上的效能影響。

### <a name="available-security-options"></a>可用的安全性選項 

NFSv 4.1 磁片區目前可用的安全性選項如下所示： 

* **sec = sys**藉由使用 AUTH_SYS 來驗證 NFS 作業，使用本機 UNIX Uid 與 gid。
* **sec = krb5**使用 Kerberos V5，而不是本機 UNIX Uid 與 gid 來驗證使用者。
* **sec = krb5i**使用 Kerberos V5 進行使用者驗證，並使用安全的總和檢查碼來執行 NFS 作業的完整性檢查，以防止資料遭到篡改。
* **sec = krb5p**使用 Kerberos V5 進行使用者驗證和完整性檢查。 它會將 NFS 流量加密，以防止流量探查。 此選項是最安全的設定，但也牽涉到最多的效能負擔。

### <a name="performance-vectors-tested"></a>效能向量已測試

本節說明各種選項的單一用戶端效能影響 `sec=*` 。

* 效能影響已在兩個層級上進行測試：低平行存取（低負載）和高平行存取（i/o 和輸送量的上限）。  
* 已測試三種類型的工作負載：  
    * 小型運算隨機讀取/寫入（使用 FIO）
    * 大型運算順序讀取/寫入（使用 FIO）
    * 以應用程式（如 git）產生的中繼資料繁重工作負載

### <a name="expected-performance-impact"></a>預期的效能影響 

有兩個焦點區域： [淺色負載] 和 [上限]。 下列清單說明安全性設定和案例依據案例的效能影響安全性設定。 所有的比較都會針對 `sec=sys` 安全性參數進行。

Krb5 的效能影響：

* 低平行存取（r/w）：
    * 連續延遲增加0.3 毫秒。
    * 隨機 i/o 延遲已增加0.2 毫秒。
    * 中繼資料 i/o 延遲已增加0.2 毫秒。
* 高平行存取（r/w）： 
    * Krb5 會 unimpacted 最大順序輸送量。
    * 在工作負載轉移到純寫入的情況中，將純讀取工作負載的最大隨機 i/o 減少為30%，整體影響會降到零。 
    * 最大中繼資料工作負載減少30%。

Krb5i 的效能影響： 

* 低平行存取（r/w）：
    * 連續延遲增加0.5 毫秒。
    * 隨機 i/o 延遲已增加0.2 毫秒。
    * 中繼資料 i/o 延遲已增加0.2 毫秒。
* 高平行存取（r/w）： 
    * 無論工作負載混合為何，最大的連續輸送量都會減少70% 的整體。
    * 當工作負載轉移到純寫入時，整體影響降低為25% 的單純讀取工作負載的隨機 i/o 上限為50%。 
    * 最大中繼資料工作負載減少30%。

Krb5p 的效能影響：

* 低平行存取（r/w）：
    * 連續延遲增加0.8 毫秒。
    * 隨機 i/o 延遲已增加0.2 毫秒。
    * 中繼資料 i/o 延遲已增加0.2 毫秒。
* 高平行存取（r/w）： 
    * 無論工作負載混合為何，最大的連續輸送量都會減少85% 的整體。 
    * 當工作負載轉移到純寫入時，整體影響降低為43% 的單純讀取工作負載，最大隨機 i/o 已減少65%。 
    * 最大中繼資料工作負載減少30%。

## <a name="next-steps"></a>後續步驟  

* [關於 Azure NetApp Files 的常見問題](azure-netapp-files-faqs.md)
* [建立適用於 Azure NetApp Files 的 NFS 磁碟區](azure-netapp-files-create-volumes.md)
* [建立 Active Directory 連線](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [設定適用于 Azure NetApp Files 的 NFS 用戶端](configure-nfs-clients.md) 
