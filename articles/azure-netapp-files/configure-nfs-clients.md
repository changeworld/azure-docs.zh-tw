---
title: 設定 Azure NetApp Files 的 NFS 用戶端 |Microsoft Docs
description: 說明如何設定 NFS 用戶端以搭配 Azure NetApp Files 使用。
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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449647"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>設定 Azure NetApp Files 的 NFS 用戶端

本文所述的 NFS 用戶端設定是設定 [nfsv4.1 4.1 Kerberos 加密](configure-kerberos-encryption.md) 或 [建立雙重通訊協定磁片](create-volumes-dual-protocol.md)區時的一部分。 有多種 Linux 發行版本可搭配 Azure NetApp Files 使用。 本文說明兩個較常使用的環境設定： RHEL 8 和 Ubuntu 18.04。 

無論您使用哪一種 Linux 類別，都需要下列設定：
* 設定 NTP 用戶端，以避免發生時間誤差的問題。
* 設定 Linux 用戶端的 DNS 專案以進行名稱解析。  
    這種設定包含「A」 (向前) 記錄和 PTR (反向) 記錄。 
* 若要加入網域，請在 [領域聯結] 命令) 期間建立的目標 Active Directory (中建立電腦帳戶。 
    > [!NOTE] 
    > `$SERVICEACCOUNT`下列命令中所使用的變數應該是具有許可權或委派的使用者帳戶，才能在目標組織單位中建立電腦帳戶。
* 讓用戶端能夠掛接 NFS 磁片區和其他相關的監視工具。

## <a name="rhel-8-configuration"></a>RHEL 8 設定 

1. 安裝套件：   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. 設定 NTP 用戶端：  
    RHEL 8 `chrony` 預設使用。  遵循 [使用 Chrony suite 設定 NTP 的設定](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)指導方針。

3. 加入 Active Directory 網域：  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu 設定 
本節說明 NFS 用戶端的 Ubuntu 設定。  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>如果您使用 Nfsv4.1 4.1 Kerberos 加密 

1. 安裝套件：  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. 設定 NTP 用戶端。  
    Ubuntu 18.04 `chrony` 預設使用。  遵循 Ubuntu Bionic 中的 [設定指導方針：使用 chrony 設定 NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)。

3. 加入 Active Directory 網域：  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>如果您使用雙重通訊協定  

1. 執行下列命令以升級已安裝的套件：  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    範例：   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. 執行下列命令以重新開機並啟用服務：   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

下列範例會從適用于 LDAP 使用者的 Ubuntu LDAP 用戶端查詢 AD LDAP 伺服器 `ldapu1` ：   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>後續步驟  

* [建立適用於 Azure NetApp Files 的 NFS 磁碟區](azure-netapp-files-create-volumes.md)
* [建立適用于 Azure NetApp Files 的雙重通訊協定磁片區](create-volumes-dual-protocol.md)

