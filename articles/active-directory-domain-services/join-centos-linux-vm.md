---
title: 將 CentOS VM 加入 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何設定 CentOS Linux 虛擬機器，並將其加入 Azure Active Directory Domain Services 受控網域。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 76fc11384b55337f581a74239d4a40b90b284f32
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619652"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>將 CentOS Linux 虛擬機器加入 Azure Active Directory Domain Services 受控網域

若要讓使用者使用一組認證來登入 Azure 中的虛擬機器 (Vm) ，您可以將 Vm 加入至 Azure Active Directory Domain Services (Azure AD DS) 受控網域。 當您將 VM 加入 Azure AD DS 受控網域時，網域中的使用者帳戶和認證可以用來登入及管理伺服器。 也會套用來自受控網域的群組成員資格，讓您控制對 VM 上檔案或服務的存取。

本文說明如何將 CentOS Linux VM 加入受控網域。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 屬於受控網域的使用者帳戶。

## <a name="create-and-connect-to-a-centos-linux-vm"></a>建立並連接至 CentOS Linux VM

如果您在 Azure 中有現有的 CentOS Linux VM，請使用 SSH 連接到該 VM，然後繼續進行下一個步驟，以 [開始設定 VM](#configure-the-hosts-file)。

如果您需要建立 CentOS Linux VM，或想要建立測試 VM 以便與本文搭配使用，您可以使用下列其中一種方法：

* [Azure 入口網站](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

當您建立 VM 時，請注意虛擬網路設定，以確保 VM 可以與受控網域通訊：

* 將 VM 部署到您已在其中啟用 Azure AD Domain Services 的相同或對等互連虛擬網路。
* 將 VM 部署至與受控網域不同的子網。

部署 VM 之後，請遵循下列步驟以使用 SSH 連線到 VM。

## <a name="configure-the-hosts-file"></a>設定 hosts 檔案

若要確定已正確設定受控網域的 VM 主機名稱，請編輯 */etc/hosts* 檔案，並設定主機名稱：

```console
sudo vi /etc/hosts
```

在 *主機* 檔案中，更新 *localhost* 位址。 在下例中︰

* *aaddscontoso.com* 是受控網域的 DNS 功能變數名稱。
* *centos* 是您要加入受控網域之 centos VM 的主機名稱。

使用您自己的值來更新這些名稱：

```console
127.0.0.1 centos.aaddscontoso.com centos
```

完成時，請使用編輯器的命令儲存和結束 *主機* 檔案 `:wq` 。

## <a name="install-required-packages"></a>安裝必要的套件

VM 需要一些額外的套件才能將 VM 加入受控網域。 若要安裝和設定這些套件，請使用下列內容更新和安裝網域加入工具 `yum` ：

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>將 VM 加入受控網域

既然 VM 上已安裝必要的套件，請將 VM 加入受控網域。

1. 使用 `realm discover` 命令來探索受控網域。 下列範例會探索領域 *AADDSCONTOSO.COM*。 以全部大寫指定您自己的受控功能變數名稱：

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   如果 `realm discover` 命令找不到您的受控網域，請參閱下列疑難排解步驟：

    * 請確定可以從 VM 連線到該網域。 請嘗試 `ping aaddscontoso.com` 查看是否傳回正面回復。
    * 檢查 VM 是否部署至相同的虛擬網路，或可使用受控網域的對等互連虛擬網路。
    * 確認已更新虛擬網路的 DNS 伺服器設定，以指向受控網域的網域控制站。

1. 現在使用命令初始化 Kerberos `kinit` 。 指定屬於受控網域的使用者。 如有需要，請 [將使用者帳戶新增至 Azure AD 中的群組](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同樣地，必須以全部大寫輸入受控功能變數名稱。 在下列範例中，會使用名為的帳戶 `contosoadmin@aaddscontoso.com` 初始化 Kerberos。 輸入您自己的使用者帳戶，這是受控網域的一部分：

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. 最後，使用命令將 VM 加入受控網域 `realm join` 。 使用您在上一個命令中指定的受控網域所屬的相同使用者帳戶 `kinit` ，例如 `contosoadmin@AADDSCONTOSO.COM` ：

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

將 VM 加入受控網域需要幾分鐘的時間。 下列範例輸出顯示 VM 已成功加入受控網域：

```output
Successfully enrolled machine in realm
```

如果您的 VM 無法順利完成網域加入程式，請確定 VM 的網路安全性群組允許 TCP + UDP 埠464上的輸出 Kerberos 流量連至受控網域的虛擬網路子網。

## <a name="allow-password-authentication-for-ssh"></a>允許 SSH 的密碼驗證

根據預設，使用者只能使用以 SSH 公開金鑰為基礎的驗證來登入 VM。 以密碼為基礎的驗證失敗。 當您將 VM 加入受控網域時，這些網域帳戶必須使用密碼型驗證。 更新 SSH 設定以允許以密碼為基礎的驗證，如下所示。

1. 使用編輯器開啟 *sshd_conf* 檔案：

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 將 *>passwordauthentication* 的行更新為 *[是]*：

    ```console
    PasswordAuthentication yes
    ```

    完成時，請使用編輯器的命令儲存並結束 *sshd_conf* 檔案 `:wq` 。

1. 若要套用變更並讓使用者使用密碼登入，請重新開機 SSH 服務：

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>授與 'AAD DC Administrators' 群組 sudo 權限

若要授與 *AAD DC 系統管理員* 群組 CentOS VM 上系統管理許可權的成員，您可以將專案新增至 */etc/sudoers*。 新增之後， *AAD DC 系統管理員* 群組的成員可以使用 `sudo` CentOS VM 上的命令。

1. 開啟 *sudoers* 檔案進行編輯：

    ```console
    sudo visudo
    ```

1. 將下列專案新增至 */etc/sudoers* 檔案的結尾。 *AAD DC 系統管理員* 群組包含名稱中的空白字元，因此請在組名中包含反斜線 escape 字元。 新增您自己的功能變數名稱，例如 *aaddscontoso.com*：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    完成時，請使用編輯器的命令儲存並結束編輯器 `:wq` 。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用網域帳戶登入 VM

若要確認已成功將 VM 加入受控網域，請使用網域使用者帳戶啟動新的 SSH 連線。 確認已建立主目錄，並套用網域的群組成員資格。

1. 從您的主控台建立新的 SSH 連線。 使用屬於受控網域的網域帳戶 `ssh -l` （例如）， `contosoadmin@aaddscontoso.com` 然後輸入 VM 的位址，例如 *centos.aaddscontoso.com*。 如果您使用 Azure Cloud Shell，請使用 VM 的公用 IP 位址，而不是內部 DNS 名稱。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. 當您成功連線至 VM 時，請確認已正確初始化主目錄：

    ```console
    pwd
    ```

    您應該會在 */home* 目錄中，並在您自己的目錄中與使用者帳戶相符。

1. 現在，請檢查是否已正確解析群組成員資格：

    ```console
    id
    ```

    您應該會看到來自受控網域的群組成員資格。

1. 如果您以 *AAD DC 系統管理員* 群組的成員身分登入 VM，請確認您可以正確地使用 `sudo` 下列命令：

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>後續步驟

如果您在將 VM 連接到受控網域或以網域帳戶登入時發生問題，請參閱 [疑難排解網域加入問題](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
