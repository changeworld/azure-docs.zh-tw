---
title: 建立具有網域控制站的 Azure 檔案儲存體檔案共用-Azure
description: 在具有 Active Directory 網域的現有 Windows 虛擬桌面主機集區中，于 Azure 檔案共用上設定 FSLogix 設定檔容器。
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e03d496881b0d563387ee5a5943b60f456530453
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009217"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>建立具有 Azure 檔案儲存體和 AD DS 的設定檔容器

在本文中，您將瞭解如何在現有的 Windows 虛擬桌面主機集區上，建立由網域控制站驗證的 Azure 檔案共用。 您可以使用此檔案共用來儲存儲存設定檔。

此程式會使用 Active Directory Domain Services (AD DS) ，這是內部部署目錄服務。 如果您要尋找如何使用 Azure AD DS 建立 FSLogix 設定檔容器的相關資訊，請參閱[使用 Azure 檔案儲存體建立 FSLogix 設定檔容器](create-profile-container-adds.md)。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您的網域控制站已同步處理至 Azure，並可從您工作階段主機連線的 Azure 虛擬網路 (VNET) 進行解析。

## <a name="set-up-a-storage-account"></a>設定儲存體帳戶

首先，您必須設定 Azure 檔案儲存體儲存體帳戶。

若要設定儲存體帳戶：

1. 登入 Azure 入口網站。

2. 搜尋搜尋列中的 [**儲存體帳戶**]。

3. 選取 [+新增]  。

4. 在 [**建立儲存體帳戶**] 頁面中輸入下列資訊：

    - 建立新的資源群組。
    - 輸入儲存體帳戶的唯一名稱。
    - 針對 [**位置**]，建議您選擇與 Windows 虛擬桌面主機集區相同的位置。
    - 針對 [效能]，請選取 [標準]。  (取決於您的 IOPS 需求。 如需詳細資訊，請參閱[Windows 虛擬桌面中的 FSLogix 設定檔容器的儲存體選項](store-fslogix-profile.md)。 ) 
    - 針對 [**帳戶類型**]，選取 [ **StorageV2** ] 或 [ **FileStorage** ] (只有在 [效能層級]) 
    - 針對**複寫**，請選取 [**本機-多餘的儲存體] (LRS) **。

5. 當您完成時，請選取 [**審查 + 建立**]，然後選取 [**建立**]。

如果您需要更詳細的設定指示，請參閱[區域可用性](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

接下來，您必須建立 Azure 檔案共用。

若要建立檔案共用：

1. 選取 [前往資源]。

2. 在 [總覽] 頁面上，選取 [檔案**共用**]。

3. 選取 [ **+ 檔案共用**]，建立名為 [**設定檔**] 的新檔案共用，然後輸入適當的配額，或將此欄位保留空白表示無配額。

4. 選取 [建立]。

## <a name="enable-active-directory-authentication"></a>啟用 Active Directory 驗證

接下來，您必須啟用 Active Directory (AD) 驗證。 若要啟用此原則，您必須在已加入網域的電腦上遵循本節的指示。 若要啟用驗證，請遵循執行網域控制站的 VM 上的下列指示：

1. 遠端桌面通訊協定到已加入網域的 VM。

2. 請遵循[為您的 Azure 檔案共用啟用 AZURE AD DS 驗證](../storage/files/storage-files-identity-ad-ds-enable.md)中的指示，安裝 AzFilesHybrid 模組並啟用驗證。

3.  開啟 Azure 入口網站，開啟您的儲存體帳戶 **，選取 [** 設定]，然後確認**Active Directory (AD) **已設定為 [**啟用**]。

     > [!div class="mx-imgBorder"]
     > ![[設定] 頁面的螢幕擷取畫面，其中已啟用 Azure Active Directory (AD) 。](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>將 Azure RBAC 許可權指派給 Windows 虛擬桌面的使用者

所有需要儲存在儲存體帳戶上之 FSLogix 設定檔的使用者，都必須被指派儲存體檔案資料 SMB 共用參與者角色。

登入 Windows 虛擬桌面工作階段主機的使用者必須要有存取權限，才能存取您的檔案共用。 要授與 Azure 檔案共用的存取權，必須在共用層級以及 NTFS 層級上設定權限，與傳統 Windows 共用相仿。

若要設定共用層級許可權，請為每個使用者指派具有適當存取權限的角色。 許可權可以指派給個別使用者或 Azure AD 群組。 若要深入瞭解，請參閱[將存取權限指派](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)給身分識別。

>[!NOTE]
>您為其指派權限的帳戶或群組應已建立在網域中，並且與 Azure AD 同步。 在 Azure AD 中建立的帳戶將無法運作。

若要將角色型存取控制指派 (RBAC) 許可權：

1. 開啟 Azure 入口網站。

2. 開啟您在[設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

3. 選取 [檔案**共用**]，然後選取您打算使用的檔案共用名稱。

4. 選取 [存取控制 (IAM)]。

5. 選取 [**新增角色指派**]。

6. 在 [**新增角色指派**] 索引標籤中，針對系統管理員帳戶選取 [儲存體] [資料] [ **SMB 共用**]

     若要為使用者指派其 FSLogix 設定檔的權限，請依照相同的指示操作。 不過，當您進入步驟5時，請改為選取 [儲存體] [資料] [ **SMB 共用參與者**]。

7. 選取 [儲存]。

## <a name="assign-users-permissions-on-the-azure-file-share"></a>指派 Azure 檔案共用的許可權給使用者

將 RBAC 權限指派給使用者之後，接下來您必須設定 NTFS 權限。

您必須從 Azure 入口網站瞭解兩件事，才能開始使用：

- UNC 路徑。
- 儲存體帳戶金鑰。

### <a name="get-the-unc-path"></a>取得 UNC 路徑

以下是取得 UNC 路徑的方法：

1. 開啟 Azure 入口網站。

2. 開啟您在[設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

3. 選取 [**設定**]，然後選取 [**屬性**]。

4. 將 [**主要檔案服務端點**URI] 複製到您選擇的文字編輯器。

5. 複製 URI 之後，請執行下列動作，將它變更為 UNC：

    - 移除 `https://` 並取代為`\\`
    - `/`以反斜線取代正斜線 `\` 。
    - 將您在[建立 Azure 檔案共用](#create-an-azure-file-share)中建立的檔案共用名稱新增到 UNC 結尾。

        例如： `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰

若要取得儲存體帳戶金鑰：

1. 開啟 Azure 入口網站。

2. 開啟您在[設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

3. 在 [**儲存體帳戶**] 索引標籤上，選取 [**存取金鑰**]。

4. 將**key1**或**key2**複製到本機電腦上的檔案。

### <a name="configure-ntfs-permissions"></a>設定 NTFS 許可權

若要設定您的 NTFS 許可權：

1. 在已加入網域的 VM 上開啟命令提示字元。

2. 請執行下列 Cmdlet 以掛接 Azure 檔案共用，並為其指派磁碟機代號：

     ```powershell
     net use <desired-drive-letter>: <UNC-pat> <SA-key> /user:Azure\<SA-name>
     ```

3. 執行下列 Cmdlet 來檢查 Azure 檔案共用的存取權限：

    ```powershell
    icacls <mounted-drive-letter>:
    ```

    將取代為 `<mounted-drive-letter>` 您所對應的磁片磁碟機字母。

    *NT Authority\authenticated users Users*和*BUILTIN\Users*預設具有特定許可權。 這些預設許可權可讓這些使用者讀取其他使用者的設定檔容器。 不過，[設定儲存體許可權以與設定檔容器和 Office 容器搭配使用](/fslogix/fslogix-storage-config-ht)中所述的許可權，並不會讓使用者讀取其他人的設定檔容器。

4. 執行下列 Cmdlet，讓您的 Windows 虛擬桌面使用者建立自己的設定檔容器，同時封鎖其他使用者對其設定檔容器的存取。

     ```powershell
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - 以您用來對應磁片磁碟機的磁碟機號取代 <裝入的磁碟機號>。
     - 以使用者或 Active Directory 群組的 UPN 取代 <的使用者電子郵件>，其中包含需要存取共用的使用者。

     例如：

     ```powershell
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>在工作階段主機 Vm 上設定 FSLogix

本節將說明如何為 VM 設定 FSLogix。 每次設定工作階段主機時，您都必須依照這些指示操作。 開始設定之前，請依照[下載並安裝 FSLogix](/fslogix/install-ht)中的指示進行。 有數個選項可用來確保會在所有工作階段主機上設定登錄機碼。 您可以在映像中設定這些選項，或設定群組原則。

若要在工作階段主機 VM 上設定 FSLogix：

1. RDP 至 Windows 虛擬桌面主機集區的工作階段主機 VM。

2. [下載並安裝 FSLogix](/fslogix/install-ht)。

5. 請依照[設定設定檔容器登錄設定](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings)中的指示操作：

    - 流覽至 [**電腦**  >  **HKEY_LOCAL_MACHINE**  >  **軟體**  >  **FSLogix**]。

    - 建立**設定檔**索引鍵。

    - 建立**已啟用的 DWORD，** 其值為1。

    - 建立**VHDLocations，MULTI_SZ**。

    - 將**VHDLocations**的值設定為您在[取得 unc 路徑](#get-the-unc-path)中產生的 unc 路徑。

6. 重新啟動 VM。

## <a name="testing"></a>測試

安裝並設定 FSLogix 之後，您可以使用已指派應用程式群組或主機集區上的桌上型電腦的使用者帳戶登入，以測試您的部署。 請確定您用來登入的使用者帳戶具有檔案共用的許可權。

如果使用者先前已登入，他們將會擁有將在此會話期間使用的現有本機設定檔。 若要避免建立本機設定檔，請建立要用於測試的新使用者帳戶，或使用[教學課程：設定設定檔容器來重新導向使用者設定檔](/fslogix/configure-profile-container-tutorial/)中所述的設定方法。

若要檢查您對會話的許可權：

1. 在 Windows 虛擬桌面上啟動會話。

2. 開啟 Azure 入口網站。

3. 開啟您在[設定儲存體帳戶](#set-up-a-storage-account)中建立的儲存體帳戶。

4. 在 [建立 Azure 檔案共用] 頁面上，選取 [**建立共用**]。

5. 請確定包含使用者設定檔的資料夾現在存在於您的檔案中。

若要進行其他測試，請依照確認[您的設定檔運作](create-profile-container-adds.md#make-sure-your-profile-works)中的指示進行。

## <a name="next-steps"></a>後續步驟

若要對 FSLogix 進行疑難排解，請參閱[此疑難排解指南](/fslogix/fslogix-trouble-shooting-ht)。
