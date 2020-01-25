---
title: 將 CentOS VM 加入 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何設定 CentOS Linux 虛擬機器，並將其加入 Azure AD Domain Services 受控網域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 0c49624a28950cd3625ee5f6bb565fe8a1e7fd7a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712627"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>將 CentOS Linux 虛擬機器加入 Azure AD Domain Services 受控網域

若要讓使用者能夠使用一組認證來登入 Azure 中的虛擬機器（Vm），您可以將 Vm 加入 Azure Active Directory Domain Services （AD DS）受控網域。 當您將 VM 加入 Azure AD DS 受控網域時，可以使用網域中的使用者帳戶和認證來登入和管理伺服器。 也會套用來自 Azure AD DS 受控網域的群組成員資格，讓您控制對 VM 上檔案或服務的存取。

本文說明如何將 CentOS Linux VM 加入 Azure AD DS 受控網域。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。

## <a name="create-and-connect-to-a-centos-linux-vm"></a>建立並聯機至 CentOS Linux VM

如果您在 Azure 中有現有的 CentOS Linux VM，請使用 SSH 連接到它，然後繼續進行下一個步驟以[開始設定 VM](#configure-the-hosts-file)。

如果您需要建立 CentOS Linux VM，或想要建立要與本文搭配使用的測試 VM，您可以使用下列其中一種方法：

* [Azure 入口網站](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

當您建立 VM 時，請注意虛擬網路設定，以確保 VM 可以與 Azure AD DS 受控網域進行通訊：

* 將 VM 部署到已啟用 Azure AD Domain Services 的相同或對等互連虛擬網路中。
* 將 VM 部署到與您的 Azure AD Domain Services 實例不同的子網。

部署 VM 之後，請遵循使用 SSH 連接到 VM 的步驟。

## <a name="configure-the-hosts-file"></a>設定 hosts 檔案

為確保受控網域已正確設定 VM 主機名稱，請編輯 */etc/hosts*檔案，並設定主機名稱：

```console
sudo vi /etc/hosts
```

在*hosts*檔案中，更新*localhost*位址。 在下例中︰

* *aadds.contoso.com*是 Azure AD DS 受控網域的 DNS 功能變數名稱。
* *centos*是您要加入至受控網域之 centos VM 的主機名稱。

使用您自己的值來更新這些名稱：

```console
127.0.0.1 centos.aadds.contoso.com centos
```

完成時，請使用編輯器的 [`:wq`] 命令儲存並結束*hosts*檔案。

## <a name="install-required-packages"></a>安裝必要的套件

VM 需要一些額外的套件，才能將 VM 加入 Azure AD DS 受控網域。 若要安裝及設定這些封裝，請使用 `yum`更新並安裝網域聯結工具：

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>將 VM 加入受控網域

既然 VM 上已安裝必要的套件，請將 VM 加入 Azure AD DS 受控網域。

1. 使用 `realm discover` 命令探索 Azure AD DS 受控網域。 下列範例會探索領域*AADDS。CONTOSO.COM*。 以全部大寫指定您自己的 Azure AD DS 受控功能變數名稱：

    ```console
    sudo realm discover AADDS.CONTOSO.COM
    ```

   如果 `realm discover` 命令找不到您 Azure AD DS 受控網域，請參閱下列疑難排解步驟：

    * 請確定可從 VM 連線到該網域。 嘗試 `ping aadds.contoso.com` 以查看是否傳回正面回復。
    * 檢查 VM 是否已部署至相同或對等互連的虛擬網路，其中可使用 Azure AD DS 受控網域。
    * 確認虛擬網路的 DNS 伺服器設定已更新，以指向 Azure AD DS 受控網域的網域控制站。

1. 現在使用 `kinit` 命令來初始化 Kerberos。 指定屬於*AAD DC 系統管理員*群組的使用者。 如有需要，請[將使用者帳戶新增至 Azure AD 中的群組](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同樣地，必須以全部大寫輸入 Azure AD DS 受管理的功能變數名稱。 在下列範例中，會使用名為 `contosoadmin@aadds.contoso.com` 的帳戶來初始化 Kerberos。 輸入屬於*AAD DC 系統管理員*群組成員的您自己的使用者帳戶：

    ```console
    kinit contosoadmin@AADDS.CONTOSO.COM
    ```

1. 最後，使用 `realm join` 命令將電腦加入 Azure AD DS 受控網域。 使用與您在上一個 `kinit` 命令中指定的*AAD DC 系統管理員*群組成員相同的使用者帳戶，例如 `contosoadmin@AADDS.CONTOSO.COM`：

    ```console
    sudo realm join --verbose AADDS.CONTOSO.COM -U 'contosoadmin@AADDS.CONTOSO.COM'
    ```

將 VM 加入 Azure AD DS 受控網域需要幾分鐘的時間。 下列範例輸出顯示 VM 已成功加入 Azure AD DS 受控網域：

```output
Successfully enrolled machine in realm
```

如果您的 VM 無法順利完成網域加入程式，請確定 VM 的網路安全性群組允許 TCP + UDP 埠464上的輸出 Kerberos 流量連到您 Azure AD DS 受控網域的虛擬網路子網。

## <a name="allow-password-authentication-for-ssh"></a>允許 SSH 的密碼驗證

根據預設，使用者只能使用 SSH 公用金鑰驗證來登入 VM。 以密碼為基礎的驗證失敗。 當您將 VM 加入 Azure AD DS 受控網域時，這些網域帳戶必須使用密碼型驗證。 將 SSH 設定更新為允許以密碼為基礎的驗證，如下所示。

1. 使用編輯器開啟*sshd_conf*檔案：

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 將*passwordauthentication 開頭*的行更新為 *[是]* ：

    ```console
    PasswordAuthentication yes
    ```

    完成時，請使用編輯器的 [`:wq`] 命令儲存並結束*sshd_conf*檔案。

1. 若要套用變更並讓使用者使用密碼登入，請重新開機 SSH 服務：

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>授與 'AAD DC Administrators' 群組 sudo 權限

若要授與*AAD DC 系統管理員*群組 CentOS VM 上系統管理許可權的成員，請將專案新增至 */etc/sudoers*。 新增之後， *AAD DC 系統管理員*群組的成員就可以在 CentOS VM 上使用 `sudo` 命令。

1. 開啟*sudoers*檔案進行編輯：

    ```console
    sudo visudo
    ```

1. 將下列專案新增至 */etc/sudoers*檔案的結尾。 *AAD DC 系統管理員*群組包含名稱中的空白字元，因此請在組名中包含反斜線 escape 字元。 新增您自己的功能變數名稱，例如*aadds.contoso.com*：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aadds.contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    完成時，請使用編輯器的 [`:wq`] 命令儲存並結束編輯器。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用網域帳戶登入 VM

若要確認 VM 已成功加入 Azure AD DS 受控網域，請使用網域使用者帳戶啟動新的 SSH 連線。 確認已建立主目錄，並已套用網域的群組成員資格。

1. 從您的主控台建立新的 SSH 連線。 使用屬於受控 `ssh -l` 網域的網域帳戶（例如 `contosoadmin@contoso.com`），然後輸入您 VM 的位址，例如*centos.aadds.contoso.com*。 如果您使用 Azure Cloud Shell，請使用 VM 的公用 IP 位址，而不是內部 DNS 名稱。

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com centos.aadds.contoso.com
    ```

1. 當您成功連線到 VM 時，請確認已正確初始化主目錄：

    ```console
    pwd
    ```

    您應該會在 */home*目錄中，其中包含符合使用者帳戶的專屬目錄。

1. 現在檢查是否已正確解析群組成員資格：

    ```console
    id
    ```

    您應該會看到來自 Azure AD DS 受控網域的群組成員資格。

1. 如果您已以*AAD DC 系統管理員*群組的成員身分登入 VM，請檢查您是否可以正確地使用 `sudo` 命令：

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>後續步驟

如果您在將 VM 連線到 Azure AD DS 受控網域時發生問題，或使用網域帳戶登入，請參閱針對[網域加入問題進行疑難排解](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
