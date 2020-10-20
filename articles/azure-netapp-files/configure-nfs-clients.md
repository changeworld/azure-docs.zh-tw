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
ms.date: 10/19/2020
ms.author: b-juche
ms.openlocfilehash: f4b8b4b56693023ede2ccf8ae7eeac7ed5e16824
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216856"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>設定 Azure NetApp Files 的 NFS 用戶端

本文所述的 NFS 用戶端設定是設定 [nfsv4.1 4.1 Kerberos 加密](configure-kerberos-encryption.md) 或 [建立雙重通訊協定磁片](create-volumes-dual-protocol.md)區時的一部分。 有多種 Linux 發行版本可搭配 Azure NetApp Files 使用。 本文說明兩個較常使用的環境設定： RHEL 8 和 Ubuntu 18.04。 

無論您使用哪一種 Linux 類別，都需要下列設定：
* 設定 NTP 用戶端，以避免發生時間誤差的問題。
* 設定 Linux 用戶端的 DNS 專案以進行名稱解析。  
    此設定必須包含「A」 (向前) 記錄和 PTR (反向) 記錄。 
* 若要加入網域，請在目標 Active Directory 中建立 Linux 用戶端的電腦帳戶， (在 [領域聯結] 命令) 期間建立此帳戶。 
    > [!NOTE] 
    > `$SERVICEACCOUNT`下列命令中所使用的變數應該是具有許可權或委派的使用者帳戶，才能在目標組織單位中建立電腦帳戶。

## <a name="rhel-8-configuration"></a>RHEL 8 設定 

本節說明 Nfsv4.1 4.1 Kerberos 加密和雙重通訊協定所需的 RHEL 設定。  

本節中的範例會使用下列功能變數名稱和 IP 位址：  

* 功能變數名稱： `contoso.com`
* 私人 IP： `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>如果您使用 Nfsv4.1 4.1 Kerberos 加密，則設定 RHEL 8

1. `/etc/resolv.conf`使用適當的 DNS 伺服器設定。  

    例如：  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. 在 dns 伺服器中新增 DNS 正向和反向對應區域的 NFS 用戶端記錄。

3. 若要驗證 DNS，請從 NFS 用戶端使用下列命令：   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. 安裝套件：   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  設定 NTP 用戶端。  

    RHEL 8 預設會使用 chrony。 遵循 [使用 `Chrony` 套件設定 NTP 的設定](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)指導方針。

6.  加入 Active Directory 網域：  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    例如： 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`


7. 重新開機所有 NFS 服務：  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    重新開機可避免在 `“mount.nfs: an incorrect mount option was specified”` Kerberos 掛接期間發生錯誤狀況。

8. 以 `kinit` 使用者帳戶執行命令以取得票證： 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    例如：   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>如果您使用雙重通訊協定，則設定 RHEL 8

下列步驟是選擇性的。 只有當您在 NFS 用戶端使用使用者對應時，才需要執行這些步驟： 

1. [如果您使用 nfsv4.1 4.1 Kerberos 加密](#rhel8_nfsv41_kerberos)區段，請完成 RHEL 8 設定中所述的所有步驟。   

2. 在您的/etc/hosts 檔案中新增靜態 DNS 記錄，以使用 AD 的完整功能變數名稱 (FQDN) ，而不是使用 SSSD 設定檔中的 IP 位址：  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. 新增網域的額外區段，以從 AD LDAP 伺服器解析識別碼：    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. 確定您 `/etc/nsswitch.conf` 有 `sss` 下列專案：   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. 重新開機 `sssd` 服務並清除快取：   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. 測試以確保您的用戶端與 LDAP 伺服器整合：   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu 設定   

本節說明 Nfsv4.1 4.1 Kerberos 加密和雙重通訊協定所需的 Ubuntu 設定。 

本節中的範例會使用下列功能變數名稱和 IP 位址：  

* 功能變數名稱： `contoso.com`
* 私人 IP： `10.6.1.4`

1. `/etc/resolv.conf`使用適當的 DNS 伺服器設定：

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. 在 dns 伺服器中新增 DNS 正向和反向對應區域的 NFS 用戶端記錄。
 
    若要驗證 DNS，請從 NFS 用戶端使用下列命令：

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. 安裝套件：
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    當系統提示時，輸入 `$DOMAIN.NAME` (使用大寫，例如， `CONTOSO.COM`) 作為預設的 Kerberos 領域。

4. 重新開機服務 `rpc-gssd.service` ： 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18.04 預設會使用 chrony。 遵循 Ubuntu Bionic 中的 [設定指導方針：使用 chrony 設定 NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)。

6. 加入 Active Directory 網域：   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    例如：    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. 請向 `kinit` 使用者執行以取得票證： 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    例如：    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>如果您使用雙重通訊協定，則為 Ubuntu 設定  

下列步驟是選擇性的。  只有當您想要在 NFS 用戶端使用使用者對應時，才需要執行這些步驟：  

1. 執行下列命令以升級已安裝的套件：   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    下列範例會使用範例值。 當命令提示您輸入時，您應該根據您的環境提供輸入。 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. 執行下列命令以重新開機並啟用服務：

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

下列範例會從適用于 LDAP 使用者的 Ubuntu LDAP 用戶端查詢 AD LDAP 伺服器 `‘hari1’` ： 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>後續步驟  

* [建立適用於 Azure NetApp Files 的 NFS 磁碟區](azure-netapp-files-create-volumes.md)
* [建立適用于 Azure NetApp Files 的雙重通訊協定磁片區](create-volumes-dual-protocol.md)

