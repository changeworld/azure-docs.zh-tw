---
title: 將 SLE VM 加入 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何設定 SUSE Linux Enterprise 虛擬機器並將其加入 Azure AD Domain Services 受控網域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: iainfou
ms.openlocfilehash: 9f50be95e456802c6ad403acd6a2f539780e53a2
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88251163"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>將 SUSE Linux Enterprise 虛擬機器加入 Azure Active Directory Domain Services 受控網域

若要讓使用者能夠使用一組認證來登入虛擬機器 (Azure 中) 的 Vm，您可以將 Vm 加入 Azure Active Directory Domain Services (Azure AD DS) 受控網域。 當您將 VM 加入 Azure AD DS 受控網域時，可以使用網域中的使用者帳戶和認證來登入和管理伺服器。 也會套用來自受控網域的群組成員資格，讓您控制對 VM 上檔案或服務的存取。

本文說明如何將 SUSE Linux Enterprise (SLE) VM 加入受控網域。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 屬於受控網域一部分的使用者帳戶。

## <a name="create-and-connect-to-a-sle-linux-vm"></a>建立並連接到 SLE Linux VM

如果您在 Azure 中有現有的 SLE Linux VM，請使用 SSH 連接到它，然後繼續進行下一個步驟以 [開始設定 VM](#configure-the-hosts-file)。

如果您需要建立 SLE Linux VM，或想要建立要與本文搭配使用的測試 VM，您可以使用下列其中一種方法：

* [Azure 入口網站](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

當您建立 VM 時，請注意虛擬網路設定，以確保 VM 可以與受控網域進行通訊：

* 將 VM 部署到已啟用 Azure AD Domain Services 的相同或對等互連虛擬網路中。
* 將 VM 部署到與您的 Azure AD Domain Services 受控網域不同的子網。

部署 VM 之後，請遵循使用 SSH 連接到 VM 的步驟。

## <a name="configure-the-hosts-file"></a>設定 hosts 檔案

為確保受控網域已正確設定 VM 主機名稱，請編輯 */etc/hosts* 檔案，並設定主機名稱：

```console
sudo vi /etc/hosts
```

在 *hosts* 檔案中，更新 *localhost* 位址。 在下例中︰

* *aaddscontoso.com* 是受控網域的 DNS 功能變數名稱。
* *linux-q2gr* 是您要加入受控網域之 SLE VM 的主機名稱。

使用您自己的值來更新這些名稱：

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

完成時，請使用編輯器的命令來儲存並結束 *hosts* 檔案 `:wq` 。

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>使用 SSSD 將 VM 加入受控網域

若要使用 **SSSD** 和 YaST 的 *使用者登入管理* 模組來加入受控網域，請完成下列步驟：

1. 安裝 *使用者登入管理* YaST 模組：

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. 開啟 YaST。

1. 若要在稍後成功使用 DNS 自動搜索，請將受控網域 IP 位址設定 (*Active Directory 伺服器*) 做為用戶端的名稱伺服器。

    在 [YaST] 中，選取 [ **系統 > 網路設定**]。

1. 選取 [ *主機名稱/DNS* ] 索引標籤，然後在文字方塊 *名稱伺服器 1*中輸入受控網域 (es) 的 IP 位址。 這些 IP 位址會顯示在受控網域之 Azure 入口網站的 [ *屬性* ] 視窗中，例如 *10.0.2.4* 和 *10.0.2.5*。

    新增您自己的受控網域 IP 位址，然後選取 **[確定]**。

1. 從 [YaST] 主視窗中，選擇 [*網絡服務*  >  *使用者登入管理*]。

    此模組隨即開啟，其中會顯示您電腦的不同網路屬性和目前使用中的驗證方法，如下列範例螢幕擷取畫面所示：

    ![YaST 中 [使用者登入管理] 視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/overview-window.png)

    若要開始編輯，請選取 [ **變更設定**]。

若要將 VM 加入受控網域，請完成下列步驟：

1. 在對話方塊中，選取 [ **新增網域**]。

1. 指定正確的 *功能變數名稱*（例如 *aaddscontoso.com*），然後指定要用於識別資料和驗證的服務。 同時選取 [ *Microsoft Active Directory* ]。

    請確定已選取 [ *啟用網域* ] 的選項。

1. 在準備就緒時，選取 [確定]。

1. 接受下列對話方塊中的預設設定，然後選取 **[確定]**。

1. VM 會視需要安裝其他軟體，然後檢查受控網域是否可用。

    如果所有專案都正確，則會顯示下列範例對話方塊，指出 VM 已探索到受控網域，但您尚未 *註冊*。

    ![YaST 中 [Active Directory 註冊] 視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/enroll-window.png)

1. 在對話方塊中，指定隸屬于受控網域的使用者 *名稱* 和 *密碼* 。 如有需要，請 [將使用者帳戶新增至 Azure AD 中的群組](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    若要確定已針對 Samba 啟用目前的網域，請啟用 [ *覆寫 Samba 設定] 以與此 AD 搭配*使用。

1. 若要註冊，請選取 **[確定]**。

1. 系統會顯示一則訊息，確認您已成功註冊。 若要完成，請選取 **[確定]**。

在受控網域中註冊 VM 之後，使用 [ *管理網域使用者登*入] 來設定用戶端，如下列範例螢幕擷取畫面所示：

![YaST 中 [管理網域使用者登入] 視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. 若要允許使用受控網域所提供的資料登入，請核取 [ *允許網域使用者登*入] 核取方塊。

1. （選擇性）在 [ *啟用網域資料來源*] 下，視您的環境需要檢查其他資料來源。 這些選項包括允許哪些使用者使用 **sudo** ，或哪些網路磁碟機機可供使用。

1. 若要讓受控網域中的使用者擁有 VM 上的主目錄，請核取 [ *建立主目錄*] 的方塊。

1. 從側邊列中，依序選取 [ **服務選項] [›名稱**] 和 [ *擴充選項*]。 從該視窗中，選取 [ *fallback_homedir* ] 或 [ *override_homedir*]，然後選取 [ **新增**]。

1. 指定主目錄位置的值。 若要讓主目錄遵循 */home/USER_NAME*的格式，請使用 */home/%u*。 如需可能變數的詳細資訊，請參閱 (`man 5 sssd.conf`) ，一節 *override_homedir*中的 sssd man 頁面。

1. 選取 [確定]。

1. 若要儲存變更，請選取 **[確定]**。 然後，請確定顯示的值現在是正確的。 若要離開對話方塊，請選取 [ **取消**]。

1. 如果您想要同時執行 SSSD 和 Winbind (例如透過 SSSD 聯結，但接著在) 中執行 Samba 檔案伺服器，則必須將 Samba 選項 *kerberos 方法* 設定為在 smb 中的 *秘密和 keytab* 。 SSSD 選項 *ad_update_samba_machine_account_password* 也應該在 SSSD 中設定為 *true* 。 這些選項會防止系統 keytab 同步。

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>使用 Winbind 將 VM 加入受控網域

若要使用 **winbind** 和 YaST 的 *Windows 網域成員資格* 模組來加入受控網域，請完成下列步驟：

1. 在 [YaST] 中，選取 [ **網絡服務 > Windows 網域成員資格**]。

1. 在 [ *Windows 網域成員資格*] 畫面中，輸入要加入*網域或工作組*的網域。 輸入受控功能變數名稱，例如 *aaddscontoso.com*。

    ![YaST 中 Windows 網域成員資格視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/samba-client-window.png)

1. 若要使用 SMB 來源進行 Linux 驗證，請核取 [ *使用適用于 Linux 驗證的 Smb 資訊*] 選項。

1. 若要在 VM 上自動建立受控網域使用者的本機主目錄，請核取 [ *登入時建立主目錄*] 選項。

1. 核取 [ *離線驗證* ] 的選項，以允許您的網域使用者登入，即使受控網域暫時無法使用也是如此。

1. 如果您想要變更 Samba 使用者和群組的 UID 和 GID 範圍，請選取 [ *專家設定*]。

1. 藉由選取 [ *ntp*設定]，為您的受控網域設定 ntp 時間同步處理。 輸入受控網域的 IP 位址。 這些 IP 位址會顯示在受控網域之 Azure 入口網站的 [ *屬性* ] 視窗中，例如 *10.0.2.4* 和 *10.0.2.5*。

1. 選取 **[確定]** ，並在出現提示時確認加入網域。

1. 提供受控網域中系統管理員的密碼，然後選取 **[確定]**。

    ![將 SLE VM 加入受控網域時，驗證對話方塊提示的範例螢幕擷取畫面](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

加入受控網域之後，您可以使用桌面或主控台的 [顯示管理員]，從您的工作站登入。

## <a name="allow-password-authentication-for-ssh"></a>允許 SSH 的密碼驗證

根據預設，使用者只能使用 SSH 公用金鑰驗證來登入 VM。 以密碼為基礎的驗證失敗。 當您將 VM 加入受控網域時，這些網域帳戶必須使用密碼型驗證。 將 SSH 設定更新為允許以密碼為基礎的驗證，如下所示。

1. 使用編輯器開啟 *sshd_conf* 檔案：

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 將 *passwordauthentication 開頭* 的行更新為 *[是]*：

    ```console
    PasswordAuthentication yes
    ```

    完成時，請使用編輯器的命令儲存並結束 *sshd_conf* 檔案 `:wq` 。

1. 若要套用變更並讓使用者使用密碼登入，請重新開機 SSH 服務：

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>授與 'AAD DC Administrators' 群組 sudo 權限

若要為 *AAD DC 系統管理員* 群組授與 SLE VM 上系統管理許可權的成員，請在 */etc/sudoers*中新增一個專案。 新增之後， *AAD DC 系統管理員* 群組的成員就可以 `sudo` 在 SLE VM 上使用命令。

1. 開啟 *sudoers* 檔案進行編輯：

    ```console
    sudo visudo
    ```

1. 將下列專案新增至 */etc/sudoers* 檔案的結尾。 *AAD DC 系統管理員*群組包含名稱中的空白字元，因此請在組名中包含反斜線 escape 字元。 新增您自己的功能變數名稱，例如 *aaddscontoso.com*：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    完成時，請使用編輯器的命令儲存並結束編輯器 `:wq` 。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用網域帳戶登入 VM

若要確認 VM 已成功加入受控網域，請使用網域使用者帳戶啟動新的 SSH 連線。 確認已建立主目錄，並已套用網域的群組成員資格。

1. 從您的主控台建立新的 SSH 連線。 使用屬於受控網域的網域帳戶（ `ssh -l` 例如）， `contosoadmin@aaddscontoso.com` 然後輸入您 VM 的位址，例如 *linux-q2gr.aaddscontoso.com*。 如果您使用 Azure Cloud Shell，請使用 VM 的公用 IP 位址，而不是內部 DNS 名稱。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. 當您成功連線到 VM 時，請確認已正確初始化主目錄：

    ```console
    pwd
    ```

    您應該會在 */home* 目錄中，其中包含符合使用者帳戶的專屬目錄。

3. 現在檢查是否已正確解析群組成員資格：

    ```console
    id
    ```

    您應該會看到來自受控網域的群組成員資格。

4. 如果您已以 *AAD DC 系統管理員* 群組的成員身分登入 VM，請檢查您是否可以正確地使用 `sudo` 命令：

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>後續步驟

如果您在將 VM 連線到受控網域或使用網域帳戶登入時發生問題，請參閱針對 [網域加入問題進行疑難排解](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
