---
title: 使用網域控制站建立 Azure 檔案儲存體檔案共用-Azure
description: 使用您的 Active Directory 網域，在現有 Windows 虛擬桌面主機集區中的 Azure 檔案共用上設定 FSLogix 設定檔容器。
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8f8086aced26fc46fb1430df074082e8c3365baa
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746806"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>使用 Azure 檔案儲存體和 AD DS 建立設定檔容器

在本文中，您將瞭解如何建立 Azure 檔案共用，並在現有的 Windows 虛擬桌面主機集區上由網域控制站進行驗證。 您可以使用這個檔案共用來儲存儲存設定檔。

此程式會使用 Active Directory Domain Services (AD DS) ，也就是內部部署的目錄服務。 如果您要尋找如何使用 Azure AD DS 建立 FSLogix 設定檔容器的相關資訊，請參閱 [使用 Azure 檔案儲存體建立 FSLogix 設定檔容器](create-profile-container-adds.md)。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您的網域控制站已同步處理至 Azure，並可從您的工作階段主機所連線的 Azure 虛擬網路 (VNET) 解析。

## <a name="set-up-a-storage-account"></a>設定儲存體帳戶

首先，您必須設定 Azure 檔案儲存體儲存體帳戶。

若要設定儲存體帳戶：

1. 登入 Azure 入口網站。

2. 在搜尋列中搜尋 **儲存體帳戶** 。

3. 選取 [+新增]  。

4. 在 [  **建立儲存體帳戶** ] 頁面中輸入下列資訊：

    - 建立新的資源群組。
    - 輸入儲存體帳戶的唯一名稱。
    - 針對 [ **位置** ]，建議您選擇與 Windows 虛擬桌面主機集區相同的位置。
    - 針對 [效能]，請選取 [標準]。  (取決於您的 IOPS 需求。 如需詳細資訊，請參閱 [Windows 虛擬桌面中 FSLogix 設定檔容器的儲存體選項](store-fslogix-profile.md)。 ) 
    - 針對 [ **帳戶類型** ]，選取 [ **StorageV2** ] 或 [ **FileStorage** (只有在效能層級為 [高階]) 時才能使用
    - 針對 **複寫** ，請選取 [ **本機-多餘的儲存體] (LRS)** 。

5. 當您完成時，請選取 [ **審核 + 建立** ]，然後選取 [ **建立** ]。

如果您需要更詳細的設定指示，請參閱 [區域可用性](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

接下來，您必須建立 Azure 檔案共用。

若要建立檔案共用：

1. 選取 [前往資源]  。

2. 在 [總覽] 頁面上，選取 [檔案 **共用** ]。

3. 選取 [ **+ 檔案共用** ]，建立名為 **設定檔** 的新檔案共用，然後輸入適當的配額，或將欄位保留空白表示無配額。

4. 選取 [建立]。

## <a name="enable-active-directory-authentication"></a>啟用 Active Directory authentication

接下來，您必須啟用 Active Directory (AD) 驗證。 若要啟用此原則，您必須遵循此區段在已加入網域的電腦上的指示。 若要啟用驗證，請在執行網域控制站的 VM 上執行下列指示：

1. 遠端桌面通訊協定加入已加入網域的 VM。

2. 遵循 [啟用 Azure 檔案共用 AD DS authentication](../storage/files/storage-files-identity-ad-ds-enable.md) 中的指示來安裝 AzFilesHybrid 模組，並啟用驗證。

3.  開啟 Azure 入口網站，開啟您的儲存體帳戶 **，選取 [** 設定]，然後確認 **Active Directory (AD)** 設為 [ **已啟用** ]。

     > [!div class="mx-imgBorder"]
     > ![設定頁面的螢幕擷取畫面，其中已啟用 Azure Active Directory (AD) 。](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>將 Azure RBAC 許可權指派給 Windows 虛擬桌面使用者

所有需要在儲存體帳戶上儲存 FSLogix 設定檔的使用者，都必須獲指派「儲存體檔案資料 SMB 共用參與者」角色。

登入 Windows 虛擬桌面工作階段主機的使用者必須要有存取權限，才能存取您的檔案共用。 要授與 Azure 檔案共用的存取權，必須在共用層級以及 NTFS 層級上設定權限，與傳統 Windows 共用相仿。

若要設定共用層級許可權，請為每個使用者指派具有適當存取權限的角色。 您可以將許可權指派給個別使用者或 Azure AD 群組。 若要深入瞭解，請參閱 [將存取權限指派](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)給身分識別。

>[!NOTE]
>您為其指派權限的帳戶或群組應已建立在網域中，並且與 Azure AD 同步。 在 Azure AD 中建立的帳戶將無法運作。

若要將 Azure 角色型存取控制指派 (Azure RBAC) 許可權：

1. 開啟 Azure 入口網站。

2. 開啟您在 [設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

3. 選取 [檔案 **共用** ]，然後選取您打算使用的檔案共用名稱。

4. 選取 [存取控制 (IAM)]。

5. 選取 [ **新增角色指派** ]。

6. 在 [ **新增角色指派** ] 索引標籤中，選取 [ **儲存體檔案資料 SMB 共用提升許可權的參與者** ] 以取得系統管理員帳戶。

     若要為使用者指派其 FSLogix 設定檔的權限，請依照相同的指示操作。 但是，當您進入步驟5時，請改為選取 **儲存體檔案資料 SMB 共用參與者** 。

7. 選取 [儲存]。

## <a name="assign-users-permissions-on-the-azure-file-share"></a>在 Azure 檔案共用上指派使用者的許可權

當您將 Azure RBAC 許可權指派給使用者之後，接下來您必須設定 NTFS 許可權。

您必須知道 Azure 入口網站的兩個專案，才能開始使用：

- UNC 路徑。
- 儲存體帳戶金鑰。

### <a name="get-the-unc-path"></a>取得 UNC 路徑

以下是取得 UNC 路徑的方法：

1. 開啟 Azure 入口網站。

2. 開啟您在 [設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

3. 選取 [ **設定** ]，然後選取 [ **屬性** ]。

4. 將 **主要檔案服務端點** URI 複製到您選擇的文字編輯器。

5. 複製 URI 之後，請執行下列動作，以將其變更為 UNC：

    - 移除 `https://` 並取代為 `\\`
    - 將正斜線取代為 `/` 反斜線 `\` 。
    - 將您在 [建立 Azure 檔案共用](#create-an-azure-file-share) 時所建立的檔案共用名稱新增至 UNC 結尾。

        例如：`\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰

若要取得儲存體帳戶金鑰：

1. 開啟 Azure 入口網站。

2. 開啟您在 [設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

3. 在 [ **儲存體帳戶** ] 索引標籤上，選取 [ **存取金鑰** ]。

4. 將 **key1** 或 **key2** 複製到本機電腦上的檔案。

### <a name="configure-ntfs-permissions"></a>設定 NTFS 許可權

若要設定 NTFS 許可權：

1. 在已加入網域的 VM 上開啟命令提示字元。

2. 執行下列命令來掛接 Azure 檔案共用，並為其指派磁碟機號：

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. 執行下列命令來檢查 Azure 檔案共用的存取權限：

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    取代為 `<mounted-drive-letter>` 您所對應之磁片磁碟機的字母。

    *NT Authority\authenticated users 使用者* 和 *BUILTIN\Users* 預設都有特定的許可權。 這些預設許可權可讓這些使用者讀取其他使用者的設定檔容器。 不過， [設定用於設定檔容器與 Office 容器的儲存體許可權](/fslogix/fslogix-storage-config-ht) 中所述的許可權，並不會讓使用者讀取彼此的設定檔容器。

4. 執行下列命令，以允許您的 Windows 虛擬桌面使用者建立自己的設定檔容器，同時封鎖其他使用者對其設定檔容器的存取。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - 以您用來對應磁片磁碟機的磁片磁碟機磁碟機號取代 <裝入磁碟機號>。
     - 以使用者或 Active Directory 群組的 UPN 取代 <的使用者-電子郵件>，其中包含需要存取共用的使用者。

     例如：

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>在工作階段主機 Vm 上設定 FSLogix

本節將說明如何為 VM 設定 FSLogix。 每次設定工作階段主機時，您都必須依照這些指示操作。 開始設定之前，請遵循 [下載並安裝 FSLogix](/fslogix/install-ht)中的指示。 有數個選項可用來確保會在所有工作階段主機上設定登錄機碼。 您可以在映像中設定這些選項，或設定群組原則。

若要在工作階段主機 VM 上設定 FSLogix：

1. RDP 連線到 Windows 虛擬桌面主機集區的工作階段主機 VM。

2. [下載並安裝 FSLogix](/fslogix/install-ht)。

5. 請依照[設定設定檔容器登錄設定](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings)中的指示操作：

    - 流覽至 [ **電腦**  >  **HKEY_LOCAL_MACHINE**  >  **軟體**  >  **FSLogix** ]。

    - 建立 **設定檔** 金鑰。

    - Create **Enabled，DWORD** 值為1。

    - 建立 **VHDLocations、MULTI_SZ** 。

    - 將 **VHDLocations** 的值設定為您在 [取得 unc 路徑](#get-the-unc-path)時所產生的 unc 路徑。

6. 重新啟動 VM。

## <a name="testing"></a>測試

安裝並設定 FSLogix 之後，您可以使用在主機集區上指派了應用程式群組或桌面的使用者帳戶登入，以測試您的部署。 請確定您用來登入的使用者帳戶具有檔案共用的許可權。

如果使用者已登入，他們將會有一個將在此會話期間使用的現有本機設定檔。 若要避免建立本機設定檔，請建立新的使用者帳戶以用於測試，或使用 [教學課程：設定設定檔容器以重新導向使用者配置](/fslogix/configure-profile-container-tutorial/)檔中所述的設定方法。

若要檢查您的會話許可權：

1. 在 Windows 虛擬桌面上啟動會話。

2. 開啟 Azure 入口網站。

3. 開啟您在 [設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

4. 選取 [建立 Azure 檔案共用] 頁面上的 [ **建立共用** ]。

5. 請確定包含使用者設定檔的資料夾現在存在於您的檔案中。

若要進行其他測試，請依照中的指示， [確定您的設定檔可正常運作](create-profile-container-adds.md#make-sure-your-profile-works)。

## <a name="next-steps"></a>後續步驟

若要對 FSLogix 進行疑難排解，請參閱 [此疑難排解指南](/fslogix/fslogix-trouble-shooting-ht)。
