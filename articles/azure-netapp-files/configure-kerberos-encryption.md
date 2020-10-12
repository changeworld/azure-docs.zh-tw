---
title: 為 Azure NetApp Files 設定 Nfsv4.1 4.1 Kerberos 加密 |Microsoft Docs
description: 說明如何為 Azure NetApp Files 設定 Nfsv4.1 4.1 Kerberos 加密，以及對效能的影響。
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
ms.date: 9/29/2020
ms.author: b-juche
ms.openlocfilehash: b683719fa2d0c1e7b5333c2ddf9c93f2797ade9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461473"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>為 Azure NetApp Files 設定 Nfsv4.1 4.1 Kerberos 加密

Azure NetApp Files 支援 Kerberos 模式的 NFS 用戶端加密 (krb5.keytab、krb5i 和 krb5p) 搭配 AES-256 加密。 本文說明搭配 Kerberos 加密使用 Nfsv4.1 4.1 磁片區所需的設定。

## <a name="requirements"></a>需求

下列需求適用于 Nfsv4.1 4.1 用戶端加密： 

* Active Directory Domain Services (AD DS) 連接來協助 Kerberos 票證 
* 針對用戶端和 Azure NetApp Files NFS 伺服器 IP 位址建立 DNS A/PTR 記錄
* Linux 用戶端  
    本文提供 RHEL 和 Ubuntu 用戶端的指導方針。  其他用戶端將使用類似的設定步驟。 
* NTP 伺服器存取  
    您可以使用其中一個常用的 Active Directory 網網域控制站 (AD DC) 網域控制站。

## <a name="create-an-nfs-kerberos-volume"></a>建立 NFS Kerberos 磁片區

1.  遵循 [建立適用于 Azure NetApp Files 的 NFS 磁片](azure-netapp-files-create-volumes.md) 區來建立 nfsv4.1 4.1 磁片區的步驟。   

    在 [建立磁片區] 頁面上，將 [NFS 版本] 設定為 **nfsv4.1 4.1**，並將 [Kerberos] 設定為 [ **啟用**]。

    > [!IMPORTANT] 
    > 建立磁片區之後，您就無法修改 Kerberos 啟用選項。

    ![建立 Nfsv4.1 4.1 Kerberos 磁片區](../media/azure-netapp-files/create-kerberos-volume.png)  

2. 選取 [ **匯出原則** ]，以符合所需的存取和安全性選項層級 (kerberos 5、kerberos 5i 或磁片區的 kerberos 5p) 。   

    如需 Kerberos 的效能影響，請參閱 [nfsv4.1 4.1 上的 Kerberos 效能影響](#kerberos_performance)。  

    您也可以在 [Azure NetApp Files] 流覽窗格中按一下 [匯出原則]，以修改磁片區的 Kerberos 安全性方法。

3.  按一下 [ **審核 + 建立** ] 以建立 nfsv4.1 4.1 磁片區。

## <a name="configure-the-azure-portal"></a>設定 Azure 入口網站 

1.  遵循 [建立 Active Directory 連接](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)中的指示。  

    Kerberos 需要您在 Active Directory 中至少建立一個電腦帳戶。 您提供的帳戶資訊是用來建立 SMB *和* Nfsv4.1 4.1 Kerberos 磁片區的帳戶。 這台電腦是在磁片區建立期間自動建立的帳戶。

2.  在 [ **Kerberos 領域**] 下，輸入 **AD 伺服器名稱** 和 **KDC IP** 位址。

    AD 伺服器和 KDC IP 可以是相同的伺服器。 這項資訊是用來建立 Azure NetApp Files 所使用的 SPN 電腦帳戶。 建立電腦帳戶之後，Azure NetApp Files 將會使用 DNS 伺服器記錄，視需要找出額外的 KDC 伺服器。 

    ![Kerberos 領域](../media/azure-netapp-files/kerberos-realm.png)
 
3.  按一下 [ **加入** ] 以儲存設定。

## <a name="configure-active-directory-connection"></a>設定 Active Directory 連接 

設定 Nfsv4.1 4.1 Kerberos 會在 Active Directory 中建立兩個電腦帳戶：
* 適用于 SMB 共用的電腦帳戶
* Nfsv4.1 4.1 的電腦帳戶--您可以使用前置詞來識別此帳戶 `NFS-` 。 

建立第一個 Nfsv4.1 4.1 Kerberos 磁片區之後，請使用下列 PowerShell 命令來設定加密類型或電腦帳戶：

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>設定 NFS 用戶端 

遵循 [設定適用于 Azure NetApp Files 的 nfs 用戶端](configure-nfs-clients.md) 來設定 nfs 用戶端的指示。  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>掛接 NFS Kerberos 磁片區

1. 在 [ **磁片** 區] 頁面上，選取您要掛接的 NFS 磁片區。

2. 從磁片區選取 **裝載指示** 以顯示指示。

    例如： 

    ![Kerberos 磁片區的掛接指示](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 建立新磁片區 (掛接點) 的目錄。  

4. 將電腦帳戶的預設加密類型設定為 AES 256：  
    `Set-ADComputer $COMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * 您只需針對每個電腦帳戶執行此命令一次。
    * 您可以從網域控制站或安裝了 [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) 的電腦執行此命令。 
    * `$COMPUTERACCOUNT`變數是當您部署 Kerberos 磁片區時，在 Active Directory 中建立的電腦帳戶。 這是前面加上的帳戶 `NFS-` 。 
    * `$ANFSERVICEACCOUNT`變數是不具特殊許可權的 Active Directory 使用者帳戶，具有已建立電腦帳戶之組織單位的委派控制項。 

5. 在主機上掛接磁片區： 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`變數是 `host:/export` 在掛接指令中找到的路徑。
    * `$ANFMOUNTPOINT`變數是 Linux 主機上使用者建立的資料夾。

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Kerberos 在 Nfsv4.1 4.1 上的效能影響 

本節可協助您瞭解 Kerberos 在 Nfsv4.1 4.1 上的效能影響。

### <a name="available-security-options"></a>可用的安全性選項 

Nfsv4.1 4.1 磁片區目前可用的安全性選項如下所示： 

* **sec = sys** 使用本機 UNIX Uid 和 gid，方法是使用 AUTH_SYS 來驗證 NFS 作業。
* **sec = krb5.keytab** 使用 Kerberos V5，而不是本機 UNIX Uid 和 gid 來驗證使用者。
* **sec = krb5i** 使用 Kerberos V5 進行使用者驗證，並使用安全的總和檢查碼來執行 NFS 作業的完整性檢查，以防止資料遭到篡改。
* **sec = krb5p** 使用 Kerberos V5 進行使用者驗證和完整性檢查。 它會將 NFS 流量加密，以防止流量探查。 此選項是最安全的設定，但也牽涉到最多的效能負擔。

### <a name="performance-vectors-tested"></a>已測試效能向量

本節說明各種選項的單一用戶端效能影響 `sec=*` 。

* 效能影響已在兩個層級進行測試：低並行 (低負載) 和高並行 (i/o 和輸送量) 的上限。  
* 測試了三種類型的工作負載：  
    * 使用 FIO) 的小型作業隨機讀取/寫入 (
    * 使用 FIO) 的大型作業順序讀取/寫入 (
    * 應用程式（例如 git）所產生的大量中繼資料工作負載

### <a name="expected-performance-impact"></a>預期的效能影響 

有兩個焦點區域：輕量負載和上限。 下列清單描述安全性設定和案例依案例的效能影響安全性設定。 所有的比較都會針對 `sec=sys` 安全性參數進行。 使用單一用戶端在單一磁片區上進行測試。 

Krb5.keytab 的效能影響：

* 低平行存取 (r/w) ：
    * 連續延遲增加0.3 毫秒。
    * 隨機 i/o 延遲增加0.2 毫秒。
    * 中繼資料 i/o 延遲增加0.2 毫秒。
* 高平行存取 (r/w) ： 
    * 依 krb5.keytab unimpacted 的最大順序輸送量。
    * 在工作負載轉移至單純寫入時，將整體影響降至零的純讀取工作負載，最大隨機 i/o 減少30%。 
    * 最大中繼資料工作負載減少30%。

Krb5i 的效能影響： 

* 低平行存取 (r/w) ：
    * 連續延遲增加0.5 毫秒。
    * 隨機 i/o 延遲增加0.2 毫秒。
    * 中繼資料 i/o 延遲增加0.2 毫秒。
* 高平行存取 (r/w) ： 
    * 無論工作負載混合為何，最大的連續輸送量都會減少70% 的整體輸送量。
    * 當工作負載轉移到純寫入時，將整體影響降至25% 的純讀取工作負載，最大隨機 i/o 可減少50%。 
    * 最大中繼資料工作負載減少30%。

Krb5p 的效能影響：

* 低平行存取 (r/w) ：
    * 連續延遲增加0.8 毫秒。
    * 隨機 i/o 延遲增加0.2 毫秒。
    * 中繼資料 i/o 延遲增加0.2 毫秒。
* 高平行存取 (r/w) ： 
    * 無論工作負載混合為何，最大的連續輸送量都會減少85% 的整體輸送量。 
    * 當工作負載轉移到純寫入時，將整體影響降至43% 的純讀取工作負載，最大隨機 i/o 可減少65%。 
    * 最大中繼資料工作負載減少30%。

## <a name="next-steps"></a>後續步驟  

* [關於 Azure NetApp Files 的常見問題](azure-netapp-files-faqs.md)
* [建立適用於 Azure NetApp Files 的 NFS 磁碟區](azure-netapp-files-create-volumes.md)
* [建立 Active Directory 連線](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [設定 Azure NetApp Files 的 NFS 用戶端](configure-nfs-clients.md) 
