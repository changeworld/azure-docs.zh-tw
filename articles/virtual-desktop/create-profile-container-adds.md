---
title: 建立 FSLogix 設定檔案容器 Azure 檔案 活動目錄網域服務 - Azure
description: 本文介紹如何使用 Azure 檔和 Azure 活動目錄域服務創建 FSLogix 配置檔容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265768"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>使用 Azure 檔案建立 FSLogix 設定檔容器

本文將介紹如何使用 Azure 檔和 Azure 活動目錄域服務 (AD DS) 創建 FSLogix 配置檔容器。

## <a name="prerequisites"></a>Prerequisites

本文假定您已經設置了 Azure AD DS 實例。 如果還沒有,請先按照["創建基本託管域](../active-directory-domain-services/tutorial-create-instance.md)"中的說明操作,然後返回此處。

## <a name="add-azure-ad-ds-admins"></a>新增 Azure AD DS 管理員

要添加其他管理員,請創建新使用者並授予他們許可權。

要新增管理員:

1. 從邊欄中選擇**Azure 活動目錄**,然後選擇**所有使用者**,然後選擇 **「新建使用者**」。

2.  在欄位中輸入使用者詳細資訊。

3. 在螢幕左側的 Azure 活動目錄窗格中,選擇 **「組**」。

4. 選擇**AAD DC 管理員**群組。

5. 在左邊窗格中,選擇 **「成員**」,然後在主窗格中選擇 **「添加成員**」 。。 這將顯示 Azure AD 中可用的所有使用者的清單。 選擇您剛剛建立的使用者配置檔的名稱。

## <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存帳戶

現在是時候通過伺服器消息塊 (SMB) 啟用 Azure AD DS 身份驗證了。 

要啟用認證:

1. 如果尚未設置和部署通用 v2 Azure 儲存帳戶,請按照[創建 Azure 儲存帳戶](../storage/common/storage-account-create.md)中的說明進行設置和部署。

2. 設置完帳戶後,選擇 **「轉到資源**」。。

3. 從螢幕左邊的窗格中選擇 **「設定**」,然後在主窗格中為 Azure 檔案啟用**Azure 活動目錄身份驗證**。 完成時，選取 [儲存]****。

4. 在螢幕左側的窗格中選擇 **「概述**」,然後在主窗格中選擇 **「檔**」。。

5. 選擇 **「檔案共用**」,並在螢幕右側顯示的欄位中輸入 **「名稱**和**配額**」 。。

## <a name="assign-access-permissions-to-an-identity"></a>將存取權限指派給身分識別

其他使用者將需要訪問許可權才能訪問您的文件共用。 為此,您需要為每個使用者分配具有相應訪問許可權的角色。

要分配使用者訪問許可權,

1. 從 Azure 門戶打開在[設置 Azure 儲存帳戶](#set-up-an-azure-storage-account)中創建的檔案共用。

2. 選擇**訪問控制 (IAM)。**

3. 選取 [新增角色指派]****。

4. 在「**添加角色分配」** 選項卡中,從角色清單中選擇適當的內建角色。 至少需要為帳戶選擇**儲存檔案數據 SMB 共用參與者**才能獲得適當的許可權。

5. 要**配置對**的存取權限,請選擇**Azure 的目錄使用者、群組或服務主體**。

6. 為目標 Azure 活動目錄識別選擇名稱或電子郵寄地址。

7. 選取 [儲存]  。

## <a name="get-the-storage-account-access-key"></a>取得儲存帳戶存取金鑰

接下來,您需要獲取存儲帳戶的訪問密鑰。

要取得儲存帳戶存取金鑰,可以:

1. 從 Azure 門戶側邊列中,選擇**儲存帳戶**。

2. 從儲存帳戶清單中,選擇啟用 Azure AD DS 的帳戶,並在上述步驟中創建自定義角色。

3. 在 **「設定」** 下,選擇 **「存取鍵」** 並從**金鑰1**複製金鑰。

4. 跳到 **「虛擬機器」** 選項卡,並找到將成為主機池一部分的任何 VM。

5. 在**虛擬機 (adVM)** 下選擇虛擬機器 (VM) 的名稱,然後選擇 **"連接"**

    這將下載 RDP 檔,該檔允許您使用自己的認證登入到 VM。

    ![連接到虛擬機器"視窗的 RDP 選項卡的螢幕截圖。](media/rdp-tab.png)

6. 登錄到 VM 後,以管理員身份運行命令提示。

7. 執行以下命令：

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - 替換為`<desired-drive-letter>`您選擇的驅動器號(例如`y:`,
    - 將的所有`<storage-account-name>`實體替換為前面指定的儲存帳戶的名稱。
    - 取代為`<share-name>`您之前建立的共享的名稱。
    - 替換為`<storage-account-key>`Azure 中的儲存帳戶金鑰。

    例如：  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. 運行以下命令以授予使用者對 Azure 檔案共享的完全存取許可權。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - 取代為`<mounted-drive-letter>`您希望使用者使用的驅動器的字母。
    - 替換為`<user-email>`將使用此設定檔存取工作階段主機 VM 的使用者的 UPN。

    例如：
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>建立設定檔容器

現在,您的配置檔已準備就緒,讓我們創建一個 FSLogix 配置檔容器。

要設定 FSLogix 設定檔容器:

1. 登入到本文開頭設定的工作階段主機 VM,然後[下載並安裝 FSLogix 代理](/fslogix/install-ht/)。

2. 解壓縮您下載的 FSLogix 代理檔案並轉到**x64** > **版本**,然後開啟**FSLogixAppsSetup.exe**。

3. 安裝程序啟動後,選擇 **「我同意許可證條款和條件」。** 如果適用,請提供新密鑰。

4. 選取 [安裝]  。

5. 開啟**驅動器 C**,然後轉到**程式檔** > **FSLogix** > **應用程式**,以確保 FSLogix 代理已正確安裝。

     >[!NOTE]
     > 如果主機池中有多個 VM,則需要為每個 VM 重複步驟 1 到 5。

6. 以管理員身份執行**註冊表編輯器**(RegEdit)。

7. 導航到**電腦** > **HKEY_LOCAL_MACHINE** > **軟體** > **FSLogix,** 右鍵點擊**FSLogix,** 選擇 **"新建**",然後選擇 **"鍵**"。

8. 建立名為**設定檔**的新金鑰。

9.  右鍵按一下**設定檔**,選擇 **"新建**",然後選擇**DWORD(32 位元)值。** 命名「**已啟用**」值並將**資料**值設定為**1**。

    ![設定檔金鑰的螢幕截圖。 REG_DWORD檔高亮顯示,其數據值設置為 1。](media/dword-value.png)

10. 右鍵按下**設定檔**,選擇**新**項目,然後選擇**多字串值**。 命名值**VHDData,** 並將輸入 Azure 檔案共享`\\fsprofile.file.core.windows.net\share`的 URI 作為資料值。

    ![顯示 VHDLocations 檔的設定檔金鑰的螢幕截圖。 其資料值顯示 Azure 檔共享的 URI。](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>將使用者配置給工作階段主機

現在,您需要將使用者分配給會話主機。

要配置使用者:

1. 以管理員身份執行 Windows PowerShell,然後執行以下 cmdlet 以使用 PowerShell 登入 Windows 虛擬桌面:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   當提示您獲得憑據時,輸入在 Windows 虛擬桌面租戶上被授予租戶創建者、RDS 擁有者或 RDS 參與者角色的同一使用者。

2. 執行以下 cmdlet 將使用者配置給遠端桌面群組:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    與前面的 cmdlet 一樣,`<your-wvd-tenant>`請`<wvd-pool>`確保`<user-principal>`使用相關值 替換與 。

    例如：

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>確保您的個人資料

現在,您所要做的就是確保您創建的配置檔存在並按預期工作。

要認證您的個人資料:

1. 開啟瀏覽器,轉到[Windows 虛擬桌面 Web 用戶端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

2. 使用分配給遠端桌面組的使用者帳戶登錄。

3. 建立使用者會話後,打開 Azure 門戶並使用管理帳戶登錄。

4. 從邊欄中,選擇 **「存儲帳戶**」。。

5. 選擇已設定為工作階段主機池的檔案共用的儲存帳戶,並使用 Azure AD DS 啟用。

6. 選擇 **「檔」** 圖示,然後展開共用。

    如果一切設定正確,您應該會看到一個名稱為「目錄」,該**目錄**的名稱格式如下所示`<user SID>-<username>`: 。

## <a name="next-steps"></a>後續步驟

如果您要尋找建立 FSLogix 設定檔案容器的替代方法,請檢視以下文章:

- [使用檔案共享為主機池建立設定檔案容器](create-host-pools-user-profile.md)。
- [使用 Azure NetApp 檔案為主機池建立 FSLogix 設定檔容器](create-fslogix-profile-container.md)

您可以在[FSLogix 設定檔容器和 Azure 檔中](fslogix-containers-azure-files.md)找到有關與 Azure 檔 FSlogix 容器相關的概念的更多詳細資訊。
