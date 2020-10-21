---
title: 將 SLE VM 加入 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何設定 SUSE Linux Enterprise 虛擬機器，並將其加入 Azure AD Domain Services 受控網域。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: joflore
ms.openlocfilehash: 607d3bc8eca3bd969f0f47ca95923040fb22591e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275853"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>將 SUSE Linux Enterprise 虛擬機器加入 Azure Active Directory Domain Services 受控網域

若要讓使用者使用一組認證來登入 Azure 中的虛擬機器 (Vm) ，您可以將 Vm 加入至 Azure Active Directory Domain Services (Azure AD DS) 受控網域。 當您將 VM 加入 Azure AD DS 受控網域時，網域中的使用者帳戶和認證可以用來登入及管理伺服器。 也會套用來自受控網域的群組成員資格，讓您控制對 VM 上檔案或服務的存取。

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

如果您在 Azure 中有現有的 SLE Linux VM，請使用 SSH 連接到該 VM，然後繼續進行下一個步驟，以 [開始設定 VM](#configure-the-hosts-file)。

如果您需要建立 SLE Linux VM，或想要建立測試 VM 以便與本文搭配使用，您可以使用下列其中一種方法：

* [Azure 入口網站](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

當您建立 VM 時，請注意虛擬網路設定，以確保 VM 可以與受控網域通訊：

* 將 VM 部署到您已在其中啟用 Azure AD Domain Services 的相同或對等互連虛擬網路。
* 將 VM 部署到與您 Azure AD Domain Services 受控網域不同的子網中。

部署 VM 之後，請遵循下列步驟以使用 SSH 連線到 VM。

## <a name="configure-the-hosts-file"></a>設定 hosts 檔案

若要確定已正確設定受控網域的 VM 主機名稱，請編輯 */etc/hosts* 檔案，並設定主機名稱：

```console
sudo vi /etc/hosts
```

在 *主機* 檔案中，更新 *localhost* 位址。 在下例中︰

* *aaddscontoso.com* 是受控網域的 DNS 功能變數名稱。
* *linux-q2gr* 是您要加入受控網域之 SLE VM 的主機名稱。

使用您自己的值來更新這些名稱：

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

完成時，請使用編輯器的命令儲存和結束 *主機* 檔案 `:wq` 。

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>使用 SSSD 將 VM 加入受控網域

若要使用 **SSSD** 和 YaST 的 *使用者登入管理* 模組來加入受控網域，請完成下列步驟：

1. 安裝 *使用者登入管理* YaST 模組：

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. 開啟 YaST。

1. 若要在稍後成功使用 DNS 自動探索，請將受控網域 IP 位址設定 (*Active Directory 伺服器*) 作為用戶端的名稱伺服器。

    在 YaST 中，選取 [ **系統 > 網路設定**]。

1. 選取 [ *主機名稱/DNS* ] 索引標籤，然後在文字方塊 *名稱伺服器 1*中輸入受控網域的 IP 位址 (es) 。 這些 IP 位址會顯示在受控網域 Azure 入口網站的 [ *屬性* ] 視窗中，例如 *10.0.2.4* 和 *10.0.2.5*。

    新增您自己的受控網域 IP 位址，然後選取 **[確定]**。

1. 從 YaST 主視窗中，選擇 [*網路服務*  >  *使用者登入管理*]。

    此課程模組隨即開啟，其中會顯示您電腦的不同網路屬性和目前使用中的驗證方法，如下列範例螢幕擷取畫面所示：

    ![YaST 中 [使用者登入管理] 視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/overview-window.png)

    若要開始編輯，請選取 [ **變更設定**]。

若要將 VM 加入受控網域，請完成下列步驟：

1. 在對話方塊中，選取 [ **加入定義域**]。

1. 指定正確的 *功能變數名稱*（例如 *aaddscontoso.com*），然後指定用於識別資料和驗證的服務。 針對兩者選取 [ *Microsoft Active Directory* ]。

    請確定已選取 [ *啟用網域* ] 的選項。

1. 在準備就緒時，選取 [確定]。

1. 接受下列對話方塊中的預設設定，然後選取 **[確定]**。

1. VM 會視需要安裝其他軟體，然後檢查受控網域是否可用。

    如果一切都正確，則會顯示下列範例對話方塊，指出 VM 已探索到受控網域，但您尚未 *註冊*。

    ![YaST 中 Active Directory 註冊視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/enroll-window.png)

1. 在對話方塊中，指定屬於受控網域一部分之使用者的使用者 *名稱* 和 *密碼* 。 如有需要，請 [將使用者帳戶新增至 Azure AD 中的群組](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    若要確定已針對 Samba 啟用目前的網域，請啟用 *覆寫 samba 設定以與此 AD 搭配運作*。

1. 若要註冊，請選取 **[確定]**。

1. 系統會顯示一則訊息，確認您已成功註冊。 若要完成，請選取 **[確定]**。

在受控網域中註冊 VM 之後，請使用 [ *管理網域使用者登*入] 設定用戶端，如下列範例螢幕擷取畫面所示：

![YaST 中 [管理網域使用者登入] 視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. 若要允許使用受控網域提供的資料進行登入，請核取 [ *允許網域使用者登*入] 的核取方塊。

1. （選擇性）在 [ *啟用網域資料來源*] 下，視您的環境需要檢查額外的資料來源。 這些選項包括允許使用 **sudo** 的使用者，或可使用的網路磁碟機機。

1. 若要允許受控網域中的使用者具有 VM 上的主目錄，請核取 [ *建立主目錄*] 的方塊。

1. 從側邊列選取 [ **服務選項›名稱切換**]，然後選取 [ *擴充選項*]。 在該視窗中，選取 [ *fallback_homedir* ] 或 [ *override_homedir*]，然後選取 [ **新增**]。

1. 指定主目錄位置的值。 若要讓主目錄遵循 */home/USER_NAME*的格式，請使用 */home/%u*。 如需可能變數的詳細資訊，請參閱 sssd 中的「 (」 `man 5 sssd.conf` 一節 *override_homedir*的) 。

1. 選取 [確定]  。

1. 若要儲存變更，請選取 **[確定]**。 然後，請確定顯示的值是正確的。 若要離開對話方塊，請選取 [ **取消**]。

1. 如果您想要同時執行 SSSD 和 Winbind (例如透過 SSSD 聯結，然後執行 Samba 檔案伺服器) ，則必須將 Samba 選項 *kerberos 方法* 設定為 *秘密，並* 在中設定 keytab。 SSSD 選項 *ad_update_samba_machine_account_password* 在 SSSD 中也應該設定為 *true* 。 這些選項會防止系統 keytab 同步。

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>使用 Winbind 將 VM 加入受控網域

若要使用 **winbind** 和 YaST 的 *Windows 網域成員資格* 模組來加入受控網域，請完成下列步驟：

1. 在 YaST 中，選取 [ **網路服務] > Windows 網域成員資格**]。

1. 在 [ *Windows 網域成員資格*] 畫面中，輸入網域*或工作組*要加入的網域。 輸入受控功能變數名稱，例如 *aaddscontoso.com*。

    ![YaST 中 Windows 網域成員資格視窗的範例螢幕擷取畫面](./media/join-suse-linux-vm/samba-client-window.png)

1. 若要使用 SMB 來源進行 Linux 驗證，請核取 [ *使用 Smb 資訊進行 Linux 驗證*] 的選項。

1. 若要在 VM 上自動建立受控網域使用者的本機主目錄，請核取 [ *登入時建立主目錄*] 的選項。

1. 檢查 *離線驗證* 的選項，以允許您的網域使用者登入，即使受控網域暫時無法使用。

1. 如果您想要變更 Samba 使用者和群組的 UID 和 GID 範圍，請選取 [ *專家設定*]。

1. 選取 [ *ntp*設定]，為受控網域設定網路時間通訊協定 (NTP) 時間同步處理。 輸入受控網域的 IP 位址。 這些 IP 位址會顯示在受控網域 Azure 入口網站的 [ *屬性* ] 視窗中，例如 *10.0.2.4* 和 *10.0.2.5*。

1. 選取 **[確定]** ，並在出現提示時確認加入網域。

1. 提供受控網域中的系統管理員密碼，然後選取 **[確定]**。

    ![當您將 SLE VM 加入受控網域時，驗證對話提示的範例螢幕擷取畫面](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

加入受控網域之後，您可以使用桌面或主控台的顯示管理員，從您的工作站登入。

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>從 YaST 命令列介面使用 Winbind 將 VM 加入受控網域

若要使用 **winbind** 和 *YaST 命令列介面*加入受控網域：

* 加入網域：

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>從終端機使用 Winbind 將 VM 加入受控網域

若要使用**winbind**和* `samba net` 命令*加入受控網域：

1. 安裝 kerberos 用戶端和 samba-winbind：

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. 編輯設定檔：

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/security/pam_winbind 的會議
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. 檢查 Azure AD 和 Linux 中的日期和時間是否同步。若要這麼做，您可以將 Azure AD 伺服器新增至 NTP 服務：
   
   1. 將下列程式程式碼新增至/etc/ntp.conf：
     
      ```console
      server aaddscontoso.com
      ```

   1. 重新開機 NTP 服務：
     
      ```console
      sudo systemctl restart ntpd
      ```

4. 加入網域：

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. 在 (PAM) 的 Linux 插即用驗證模組中，將 winbind 啟用為登入來源：

   ```console
   pam-config --add --winbind
   ```

6. 啟用主目錄的自動建立，讓使用者可以登入：

   ```console
   pam-config -a --mkhomedir
   ```

7. 啟動並啟用 winbind 服務：

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

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

若要將 SLE VM 的系統管理許可權授與 *AAD DC 系統管理員* 群組的成員，請將專案新增至 */etc/sudoers*。 新增之後， *AAD DC 系統管理員* 群組的成員可以使用 `sudo` SLE VM 上的命令。

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

若要確認已成功將 VM 加入受控網域，請使用網域使用者帳戶啟動新的 SSH 連線。 確認已建立主目錄，並套用網域的群組成員資格。

1. 從您的主控台建立新的 SSH 連線。 使用屬於受控網域的網域帳戶 `ssh -l` （例如）， `contosoadmin@aaddscontoso.com` 然後輸入 VM 的位址，例如 *linux-q2gr.aaddscontoso.com*。 如果您使用 Azure Cloud Shell，請使用 VM 的公用 IP 位址，而不是內部 DNS 名稱。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. 當您成功連線至 VM 時，請確認已正確初始化主目錄：

    ```console
    pwd
    ```

    您應該會在 */home* 目錄中，並在您自己的目錄中與使用者帳戶相符。

3. 現在，請檢查是否已正確解析群組成員資格：

    ```console
    id
    ```

    您應該會看到來自受控網域的群組成員資格。

4. 如果您以 *AAD DC 系統管理員* 群組的成員身分登入 VM，請確認您可以正確地使用 `sudo` 下列命令：

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>後續步驟

如果您在將 VM 連接到受控網域或以網域帳戶登入時發生問題，請參閱 [疑難排解網域加入問題](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
