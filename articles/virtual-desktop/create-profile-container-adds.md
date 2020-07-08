---
title: 建立 FSLogix 設定檔容器 Azure 檔案儲存體 Active Directory Domain Services-Azure
description: 本文說明如何使用 Azure 檔案儲存體和 Azure Active Directory Domain Services 建立 FSLogix 設定檔容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4ee1b8d849051b9192e53f761050f1c4b6480e1b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362436"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>建立具有 Azure 檔案儲存體和 Azure AD DS 的設定檔容器

本文將說明如何使用 Azure 檔案儲存體和 Azure Active Directory Domain Services （AD DS）來建立 FSLogix 設定檔容器。

## <a name="prerequisites"></a>必要條件

本文假設您已設定 Azure AD DS 實例。 如果您還沒有帳戶，請遵循[建立基本受控網域](../active-directory-domain-services/tutorial-create-instance.md)中的指示，然後回到這裡。

## <a name="add-azure-ad-ds-admins"></a>新增 Azure AD DS 系統管理員

若要新增其他系統管理員，您可以建立新的使用者，並授與他們許可權。

若要新增系統管理員：

1. 從側邊欄選取 [ **Azure Active Directory** ]，然後選取 [**所有使用者**]，再選取 [**新增使用者**]。

2.  在欄位中輸入使用者詳細資料。

3. 在畫面左側的 [Azure Active Directory] 窗格中，選取 [**群組**]。

4. 選取**AAD DC 系統管理員**群組。

5. 在左窗格中選取 [**成員**]，然後選取主窗格中的 [**新增成員**]。 這會顯示 Azure AD 中可用的所有使用者清單。 選取您剛才建立之使用者設定檔的名稱。

## <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

現在可以啟用透過伺服器訊息區（SMB）進行 Azure AD DS 驗證的時機。

若要啟用驗證：

1. 如果您還沒有這麼做，請遵循[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)中的指示，來設定及部署一般用途 v2 Azure 儲存體帳戶。

2. 完成帳戶設定後，請選取 [**前往資源**]。

3. 從畫面左側的**窗格中選取**[設定]，然後在主窗格中啟用**Azure 檔案儲存體的 Azure Active Directory 驗證**。 完成時，選取 [儲存]。

4. 在畫面左側的窗格中選取 **[總覽**]，然後在主窗格**中選取 [** 檔案]。

5. 選取 [檔案**共用**]，然後在畫面右側出現的欄位中輸入**名稱**和**配額**。

## <a name="assign-access-permissions-to-an-identity"></a>將存取權限指派給身分識別

其他使用者則需要存取權限，才能存取您的檔案共用。 若要這麼做，您必須為每位使用者指派具有適當存取權限的角色。

若要指派使用者存取權限：

1. 從 [Azure 入口網站] 中，開啟您在[設定 Azure 儲存體帳戶](#set-up-an-azure-storage-account)中所建立的檔案共用。

2. 選取 [存取控制 (IAM)]。

3. 選取 [**新增角色指派**]。

4. 在 [**新增角色指派**] 索引標籤中，從 [角色] 清單中選取適當的內建角色。 您至少需要為帳戶選取 [**儲存體檔案資料] [SMB 共用參與者**]，才能取得適當的許可權。

5. 針對 [**指派存取**權]，選取 [ **Azure Active Directory 使用者]、[群組] 或 [服務主體**]。

6. 選取目標 Azure Active Directory 身分識別的名稱或電子郵件地址。

7. 選取 [儲存]。

## <a name="get-the-storage-account-access-key"></a>取得儲存體帳戶存取金鑰

接下來，您必須取得儲存體帳戶的存取金鑰。

若要取得儲存體帳戶存取金鑰：

1. 從 [Azure 入口網站] 提要欄位中，選取 [**儲存體帳戶**]。

2. 從儲存體帳戶清單中，選取您在上述步驟中啟用 Azure AD DS 並建立自訂角色的帳戶。

3. 在 [**設定**] 底下，選取 [**存取金鑰**]，並從**key1**複製金鑰。

4. 移至 [**虛擬機器**] 索引標籤，並找出將會成為您主機集區一部分的任何 VM。

5. 在**虛擬機器（adVM）** 下選取虛擬機器（VM）的名稱，然後選取 **[連線]**

    這會下載可讓您使用自己的認證登入 VM 的 RDP 檔案。

    > [!div class="mx-imgBorder"]
    > ![[連線至虛擬機器] 視窗的 [RDP] 索引標籤螢幕擷取畫面。](media/rdp-tab.png)

6. 當您已登入 VM 時，請以系統管理員身分執行命令提示字元。

7. 執行下列命令：

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - `<desired-drive-letter>`將取代為您選擇的磁碟機號（例如 `y:` ）。
    - 將的所有實例取代 `<storage-account-name>` 為您稍早指定的儲存體帳戶名稱。
    - 將取代 `<share-name>` 為您稍早建立的共用名稱。
    - `<storage-account-key>`將取代為 Azure 中的儲存體帳戶金鑰。

    例如：

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. 執行下列命令，將 Azure 檔案儲存體共用的完整存取權授與使用者。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - 將取代 `<mounted-drive-letter>` 為您想要讓使用者使用的磁片磁碟機字母。
    - 將取代為 `<user-email>` 將使用此設定檔來存取工作階段主機 vm 的使用者 UPN。

    例如：

     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>建立設定檔容器

現在您的設定檔已準備就緒，讓我們來建立 FSLogix 設定檔容器。

若要設定 FSLogix 設定檔容器：

1. 登入您在本文開頭設定的工作階段主機 VM，然後[下載並安裝 FSLogix 代理程式](/fslogix/install-ht/)。

2. 將您下載的 FSLogix 代理程式檔案解壓縮，並移至**x64**  >  **版本**，然後開啟**FSLogixAppsSetup.exe**。

3. 安裝程式啟動後，請選取 **[我同意授權條款及條件]。** 如果適用的話，請提供新的金鑰。

4. 選取 [安裝]。

5. 開啟**磁片磁碟機 C**，然後移至**Program Files**  >  **FSLogix**  >  **Apps** ，以確定 FSLogix 代理程式已正確安裝。

     >[!NOTE]
     > 如果主機集區中有多個 Vm，您必須針對每個 VM 重複步驟1到5。

6. 以系統管理員身分執行**登錄編輯程式**（RegEdit）。

7. 流覽至 [ **Computer**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**]，以滑鼠右鍵按一下**FSLogix**，選取 [**新增**]，然後選取 [**金鑰**]。

8. 建立名為**Profiles**的新金鑰。

9.  以滑鼠右鍵按一下 [**設定檔**]，選取 [**新增**]，然後選取 **[DWORD （32-位）值]。** 將值命名為**Enabled** ，並將**資料**值設定為**1**。

    > [!div class="mx-imgBorder"]
    > ![設定檔索引鍵的螢幕擷取畫面。 REG_DWORD 檔案會反白顯示，且其資料值會設為1。](media/dword-value.png)

10. 以滑鼠右鍵按一下 [**設定檔**]，選取 [**新增**]，然後選取 [**多字串值**]。 將值命名為**VHDLocations** ，並將 Azure 檔案儲存體共用的 URI 輸入 `\\fsprofile.file.core.windows.net\share` 為數據值。

    > [!div class="mx-imgBorder"]
    > ![顯示 VHDLocations 檔案的設定檔索引鍵的螢幕擷取畫面。 其資料值會顯示 Azure 檔案儲存體共用的 URI。](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>將使用者指派給工作階段主機

現在您必須將使用者指派給您的工作階段主機。

若要指派使用者：

1. 以系統管理員身分執行 Windows PowerShell，然後執行下列 Cmdlet 以使用 PowerShell 登入 Windows 虛擬桌面：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   當系統提示您提供認證時，請輸入在 Windows 虛擬桌面租使用者上被授與 TenantCreator、RDS 擁有者或 RDS 參與者角色的相同使用者。

2. 執行下列 Cmdlet，將使用者指派給遠端桌面群組：

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    如同先前的 Cmdlet，請務必將 `<your-wvd-tenant>` 、和取代為 `<wvd-pool>` `<user-principal>` 相關的值。

    例如：

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>請確定您的設定檔可運作

現在您只需要確定您所建立的設定檔已存在，並如預期運作。

若要驗證您的設定檔：

1. 開啟瀏覽器並移至[Windows 虛擬桌面 web 用戶端](https://rdweb.wvd.microsoft.com/arm/webclient)。

2. 使用指派給遠端桌面群組的使用者帳戶登入。

3. 一旦建立使用者會話，請開啟 Azure 入口網站，然後使用系統管理帳戶登入。

4. 從提要欄位中，選取 [**儲存體帳戶**]。

5. 選取您設定為工作階段主機集區之檔案共用的儲存體帳戶，並使用 Azure AD DS 啟用。

6. 選取 [**檔案**] 圖示，然後展開您的共用。

    如果一切都設定正確，您應該會看到名為的**目錄**，其格式如下： `<user SID>-<username>` 。

## <a name="next-steps"></a>後續步驟

如果您要尋找建立 FSLogix 設定檔容器的替代方式，請參閱下列文章：

- [使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md)。
- [使用 Azure NetApp Files 建立主機集區的 FSLogix 設定檔容器](create-fslogix-profile-container.md)

您可以在[FSlogix 設定檔容器和 Azure 檔案](fslogix-containers-azure-files.md)中找到更多有關 FSlogix Azure 檔案容器之概念的詳細資訊。
