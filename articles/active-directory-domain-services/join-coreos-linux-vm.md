---
title: 將 CoreOS VM 加入 Azure AD 域服務 |微軟文件
description: 瞭解如何配置 CoreOS 虛擬機並將其加入 Azure AD 網域服務託管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 63dfe39b986125abc9cacf6c1a6556876bbd3a99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655203"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>將 CoreOS 虛擬機器加入 Azure AD 網域服務託管域

要允許使用者使用一組認證登錄到 Azure 中的虛擬機器 (VM),可以將 VM 加入 Azure 活動目錄域服務 (AD DS) 託管域。 將 VM 加入 Azure AD DS 託管域時,可以使用網域中的使用者帳戶和認證登錄和管理伺服器。 Azure AD DS 託管域中的組成員身份也應用於允許您控制對 VM 上檔或服務的訪問。

本文介紹如何將 CoreOS VM 加入 Azure AD DS 託管域。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 屬於 Azure AD DS 託管域的使用者帳戶。

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>建立並連線到 CoreOS Linux VM

如果在 Azure 中具有現有的 CoreOS Linux VM,請使用 SSH 連線到它,然後繼續執行下一步[以開始配置 VM](#configure-the-hosts-file)。

如果需要創建 CoreOS Linux VM,或者想要建立用於本文的測試 VM,可以使用以下方法之一:

* [Azure 入口網站](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

建立 VM 時,請注意虛擬網路設定,以確保 VM 能夠與 Azure AD DS 託管域通訊:

* 將 VM 部署到啟用 Azure AD 網域服務的相同或對等虛擬網路。
* 將 VM 部署到與 Azure AD 網域服務實例不同的子網中。

部署 VM 後,按照步驟使用 SSH 連接到 VM。

## <a name="configure-the-hosts-file"></a>設定 hosts 檔案

要確保為託管域正確設定 VM 主機名稱,請編輯 */etc/主機*檔案並設定主機名稱:

```console
sudo vi /etc/hosts
```

在*主機*檔案中,更新*本地主機*位址。 在下例中︰

* *aaddscontoso.com*是 Azure AD DS 託管域的 DNS 功能變數名稱。
* *coreos*是要加入託管域的 CoreOS VM 的主機名。

使用您自己的值更新這些名稱:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

完成後,使用編輯器`:wq`的命令儲存並退出*主機*檔案。

## <a name="configure-the-sssd-service"></a>設定 SSSD 服務

更新 */etc/ssd/ssd.conf* SSSD 配置。

```console
sudo vi /etc/sssd/sssd.conf
```

為以下參數指定您自己的 Azure AD DS 託管網域名稱:

* 所有上部案例中的*網域*
* *[網域/AADDS]* 其中 AADDS 位於所有上部案例中
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *所有*上部案例中的krb5_realm

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>將 VM 加入託管域

更新 SSSD 設定檔後,現在將虛擬機器加入託管域。

1. 首先,使用`adcli info`命令驗證可以看到有關 Azure AD DS 託管域的資訊。 下面的範例獲取域*AADDSCONTOSO.COM*的資訊。 在「所有上寫」中指定您自己的 Azure AD DS 託管網域名稱:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   如果`adcli info`命令找不到 Azure AD DS 託管域,請查看以下故障排除步驟:

    * 確保可以從 VM 聯繫到域。 嘗試`ping aaddscontoso.com`查看是否返回了肯定的答覆。
    * 檢查 VM 是否部署到 Azure AD DS 託管域可用的同一虛擬網路或對等虛擬網路。
    * 確認虛擬網路的 DNS 伺服器設定已更新以指向 Azure AD DS 託管域的域控制器。

1. 現在使用`adcli join`命令將 VM 加入 Azure AD DS 託管域。 指定屬於 Azure AD DS 託管域的使用者。 如果需要[,將使用者帳戶新增到 Azure AD 中的群組](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同樣,必須在所有上部操作中輸入 Azure AD DS 託管域名稱。 在下面的範例中,命名的`contosoadmin@aaddscontoso.com`帳戶用於初始化 Kerberos。 輸入屬於 Azure AD DS 託管域的使用者帳戶。

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    當`adcli join`VM 成功加入 Azure AD DS 託管域時,該命令不會返回任何資訊。

1. 要應用網域聯接設定,請啟動 SSSD 服務:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用網域帳號登入 VM

要驗證 VM 已成功加入 Azure AD DS 託管域,請使用網域使用者帳戶啟動新的 SSH 連接。 確認已創建主目錄,並應用域中的組成員身份。

1. 從主控台創建新的 SSH 連接。 使用`ssh -l`指令使用屬於託管域的網域帳號,例如`contosoadmin@aaddscontoso.com`,然後輸入 VM 的位址,如*coreos.aaddscontoso.com*。 如果使用 Azure 雲外殼,請使用 VM 的公共 IP 位址,而不是內部 DNS 名稱。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. 當檢查群組成員的身份是否得到正確解決:

    ```console
    id
    ```

    您應該從 Azure AD DS 託管域中看到組成員身份。

## <a name="next-steps"></a>後續步驟

如果在將 VM 連接到 Azure AD DS 託管域或使用網域帳戶登入時遇到問題,請參閱[排除網域聯接問題](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
