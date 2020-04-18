---
title: 加入 Ubuntu VM 到 Azure AD 域服務 |微軟文件
description: 瞭解如何設定 Ubuntu Linux 虛擬機並將其加入 Azure AD 域服務託管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 74af841b777494744c72ed219bacd3b3835d41ac
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617559"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>將 Ubuntu Linux 虛擬機器加入 Azure AD 網域服務託管域

要允許使用者使用一組認證登錄到 Azure 中的虛擬機器 (VM),可以將 VM 加入 Azure 活動目錄域服務 (AD DS) 託管域。 將 VM 加入 Azure AD DS 託管域時,可以使用網域中的使用者帳戶和認證登錄和管理伺服器。 Azure AD DS 託管域中的組成員身份也應用於允許您控制對 VM 上檔或服務的訪問。

本文介紹如何將 Ubuntu Linux VM 加入 Azure AD DS 託管域。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 屬於 Azure AD DS 受控網域一部分的使用者帳戶。

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>建立並連接到 Ubuntu Linux VM

如果在 Azure 中具有現有的 Ubuntu Linux VM,請使用 SSH 連線到它,然後繼續執行下一步[以開始配置 VM](#configure-the-hosts-file)。

如果需要創建 Ubuntu Linux VM,或者想要建立用於本文的測試 VM,可以使用以下方法之一:

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
* *ubuntu*是要加入託管域的 Ubuntu VM 的主機名。

使用您自己的值更新這些名稱:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

完成後,使用編輯器`:wq`的命令儲存並退出*主機*檔案。

## <a name="install-required-packages"></a>安裝必要的套件

VM 需要一些其他包才能將 VM 加入 Azure AD DS 託管域。 要安裝與設定這些套件,請使用`apt-get`

在 Kerberos 安裝期間 *,krb5 使用者*包會提示所有 UPPERCASE 中的網域名稱。 例如,如果 Azure AD DS 託管域的名稱*aaddscontoso.com,**則 AADDSCONTOSO.COM*作為域輸入。 安裝在`[realm]`*/etc/krb5.conf*設定檔中寫`[domain_realm]`入 和節。 請確保指定所有「大寫」的域:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>設定網路時間協定 (NTP)

要使域通信正常工作,Ubuntu VM 的日期和時間必須與 Azure AD DS 託管域同步。 將 Azure AD DS 託管域的 NTP 主機名添加到 */etc/ntp.conf*檔案中。

1. 使用編輯器開啟*ntp.conf*檔案:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. 在*ntp.conf*檔中,創建一行以添加 Azure AD DS 託管域的 DNS 名稱。 在下面的示例中,將添加*aaddscontoso.com*的條目。 使用您自己的 DNS 名稱:

    ```console
    server aaddscontoso.com
    ```

    完成後,使用編輯器`:wq`的命令保存並退出*ntp.conf*檔。

1. 要確保 VM 與 Azure AD DS 託管域同步,需要執行以下步驟:

    * 停止 NTP 伺服器
    * 從託管域更新日期和時間
    * 啟動 NTP 服務

    運行以下命令以完成這些步驟。 將您自己的 DNS`ntpdate`名稱與 指令一起使用:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>將 VM 加入託管域

現在,所需的套件安裝在 VM 上並配置了 NTP,請將 VM 加入 Azure AD DS 託管域。

1. 使用`realm discover`命令發現 Azure AD DS 託管域。 下面的示例發現*AADDSCONTOSO.COM*的域。 在「所有上寫」中指定您自己的 Azure AD DS 託管網域名稱:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   如果`realm discover`命令找不到 Azure AD DS 託管域,請查看以下故障排除步驟:

    * 確保可以從 VM 聯繫到域。 嘗試`ping aaddscontoso.com`查看是否返回了肯定的答覆。
    * 檢查 VM 是否部署到 Azure AD DS 託管域可用的同一虛擬網路或對等虛擬網路。
    * 確認虛擬網路的 DNS 伺服器設定已更新以指向 Azure AD DS 託管域的域控制器。

1. 現在使用`kinit`指令初始化 Kerberos。 指定屬於 Azure AD DS 託管域的使用者。 如果需要[,將使用者帳戶新增到 Azure AD 中的群組](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同樣,必須在所有上部操作中輸入 Azure AD DS 託管域名稱。 在下面的範例中,命名的`contosoadmin@aaddscontoso.com`帳戶用於初始化 Kerberos。 輸入屬於 Azure AD DS 託管域的使用者帳戶:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. 最後,使用命令`realm join`將電腦加入 Azure AD DS 託管域。 使用上一指令中`kinit`指定的 Azure AD DS 託管域的一部分的相同使用者帳戶`contosoadmin@AADDSCONTOSO.COM`,例如:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

將 VM 加入 Azure AD DS 託管域需要一些時間。 以下範例輸出顯示 VM 已成功加入 Azure AD DS 託管域:

```output
Successfully enrolled machine in realm
```

如果 VM 無法成功完成域加入過程,請確保 VM 的網路安全組允許在 TCP + UDP 連接埠 464 上向 Azure AD DS 託管域的虛擬網路子網進行出站 Kerberos 流量。

如果收到錯誤*未指定的 GSS 失敗。 次要代碼可能會提供更多資訊(Kerberos 資料庫中找不到伺服器),* 開啟檔案 */etc/krb5.conf*並在`[libdefaults]`節中添加以下代碼,然後重試:

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>更新 SSSD 設定

上一步中安裝的套裝軟體包之一是系統安全服務守護程式 (SSSD)。 當用戶嘗試使用網域認證到 VM 時,SSSD 會將請求中繼到身份驗證提供者。 在這種情況下,SSSD 使用 Azure AD DS 對請求進行身份驗證。

1. 使用編輯器開啟*ssd.conf*檔案:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. 註釋掉*use_fully_qualified_names*行,如下所示:

    ```console
    # use_fully_qualified_names = True
    ```

    完成後,使用編輯器`:wq`的命令儲存並退出*ssd.conf*檔案。

1. 要套用變更,請重新啟動 SSSD 服務:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>設定使用者帳戶與群組設定

將 VM 連接到 Azure AD DS 託管域並配置為身份驗證後,需要完成一些使用者配置選項。 這些配置更改包括允許基於密碼的身份驗證,並在域使用者首次登錄時自動在本地 VM 上創建主目錄。

### <a name="allow-password-authentication-for-ssh"></a>允許 SSH 的密碼認證

默認情況下,使用者只能使用基於 SSH 公鑰的身份驗證登錄到 VM。 基於密碼的身份驗證失敗。 將 VM 加入 Azure AD DS 託管域時,這些域帳戶需要使用基於密碼的身份驗證。 更新 SSH 配置以允許基於密碼的身份驗證,如下所示。

1. 使用編輯器開啟*sshd_conf*檔案:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 將*密碼身份驗證*行更新為 *「是*」 :

    ```console
    PasswordAuthentication yes
    ```

    完成後,使用編輯器`:wq`的命令儲存並退出*sshd_conf*檔。

1. 要套用變更並允許使用者使用密碼登入,請重新啟動 SSH 服務:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>設定自動主目錄建立

要在使用者首次登錄時啟用自動建立家目錄,請完成以下步驟:

1. 在編輯器中開啟 */etc/pam.d/公用工作階段*檔:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. 在此檔中添加以下行`session optional pam_sss.so`,

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    完成後,使用編輯器`:wq`的命令保存並退出*公用作業階段*檔。

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>授與 'AAD DC Administrators' 群組 sudo 權限

要授予 Ubuntu VM 上的*AAD DC 管理員*組管理許可權,您需要向 */etc/sudoers*添加一個條目。 添加後 *,AAD DC 管理員*組的成員可以在 Ubuntu VM 上使用`sudo`該命令。

1. 開啟*sudoers*檔進行編輯:

    ```console
    sudo visudo
    ```

1. 將以下項目加入 */etc/sudoers*檔案的末尾:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    完成後,使用`Ctrl-X`命令保存並退出編輯器。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用網域帳號登入 VM

要驗證 VM 已成功加入 Azure AD DS 託管域,請使用網域使用者帳戶啟動新的 SSH 連接。 確認已創建主目錄,並應用域中的組成員身份。

1. 從主控台創建新的 SSH 連接。 使用`ssh -l`指令使用屬於託管域的網域帳號,例如`contosoadmin@aaddscontoso.com`,然後輸入 VM 的位址,如*ubuntu.aaddscontoso.com*。 如果使用 Azure 雲外殼,請使用 VM 的公共 IP 位址,而不是內部 DNS 名稱。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. 成功連線到 VM 後,請驗證家目錄是否正確初始化:

    ```console
    pwd
    ```

    您應該位於 */home*目錄中,有自己的目錄與使用者帳戶匹配。

1. 當檢查群組成員的身份是否得到正確解決:

    ```console
    id
    ```

    您應該從 Azure AD DS 託管域中看到組成員身份。

1. 如果您以*AAD DC 管理員*群組的成員身份登入 VM,請檢查`sudo`是否可以正確使用以下 指令:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>後續步驟

如果在將 VM 連接到 Azure AD DS 託管域或使用網域帳戶登入時遇到問題,請參閱[排除網域聯接問題](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
