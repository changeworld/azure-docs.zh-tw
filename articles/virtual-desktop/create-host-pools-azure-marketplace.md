---
title: Windows 虛擬桌面主機集區 Azure Marketplace - Azure
description: 如何使用 Azure Marketplace 建立 Windows 虛擬桌面主機集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128005"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>教學課程：使用 Azure Marketplace 建立主機集區

在本教學課程中，您將瞭解如何使用 Microsoft Azure Marketplace 供應專案，在 Windows 虛擬桌面租使用者內建立主機集區。

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

本教學課程中的工作包括：

> [!div class="checklist"]
>
> * 在 Windows 虛擬桌面中建立主機集區。
> * 在 Azure 訂用帳戶中建立具有 VM 的資源群組。
> * 將 VM 加入 Active Directory 網域。
> * 向 Windows 虛擬桌面註冊 VM。

## <a name="prerequisites"></a>Prerequisites

* 虛擬桌面中的租使用者。 先前的[教學](tenant-setup-azure-active-directory.md)課程會建立租使用者。
* [Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)。

一旦您擁有此模組，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>執行 Azure Marketplace 供應項目來佈建新的主機集區

若要執行 Azure Marketplace 供應項目來佈建新的主機集區：

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。
1. 在 Marketplace 搜尋視窗中輸入 **Windows 虛擬桌面**。
1. 選取 [Windows 虛擬桌面 - 佈建主機集區]，然後選取 [建立]。

之後，請依照下一節中的指示，輸入適當索引標籤的資訊。

### <a name="basics"></a>基本概念

以下是您在 [**基本**] 索引標籤中所做的動作：

1. 選取 [訂用帳戶]。
1. 針對 [**資源群組**]，選取 [**建立新**的]，並提供新資源群組的名稱。
1. 選取**區域**。
1. 輸入 Windows 虛擬桌面租使用者內唯一的主機集區名稱。
1. 選取 [**桌面類型**]。 如果您選取 [**個人**]，連接到此主機集區的每個使用者都會永久指派給虛擬機器。
1. 輸入可登入 Windows 虛擬桌面用戶端並存取桌面的使用者。 使用逗號分隔清單。 例如，如果您想要指派 `user1@contoso.com` 並 `user2@contoso.com` 存取，請輸入 *`user1@contoso.com,user2@contoso.com`*
1. 在 [**服務中繼資料位置**] 中，選取與 Active Directory 伺服器連線的虛擬網路相同的位置。

   >[!IMPORTANT]
   >如果您使用純粹的 Azure Active Directory Domain Services （Azure AD DS）和 Azure Active Directory （Azure AD）解決方案，請務必在與您 Azure AD DS 相同的區域中部署您的主機集區，以避免加入網域和認證錯誤。

1. 選取 **[下一步：設定虛擬機器]** 。

### <a name="configure-virtual-machines"></a>設定虛擬機器

針對 [**設定虛擬機器**] 索引標籤：

1. 請接受預設值，或自訂虛擬機器的數目和大小。

    >[!NOTE]
    >如果您要尋找的特定虛擬機器大小並未出現在 [大小] 選取器中，這是因為我們尚未將它上架到 Azure Marketplace 工具。 若要要求大小，請在[Windows 虛擬桌面 UserVoice 論壇](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中建立要求或附議現有的要求。

1. 輸入虛擬機器的名稱前置詞。 例如，如果您輸入*prefix*，虛擬機器將會被稱為**前置詞-0**、**前置詞-1**等等。
1. 選取 **[下一步：虛擬機器設定]** 。

### <a name="virtual-machine-settings"></a>虛擬機器設定

針對 [**虛擬機器設定**] 索引標籤：

1. 針對 [映像來源]，選取來源並輸入如何進行尋找及儲存的相關資訊。 您的選項會因 Blob 儲存體、受控映射和資源庫而有所不同。

   如果您選擇不使用受控磁片，請選取包含 *.vhd*檔案的儲存體帳戶。
1. 輸入使用者主體名稱和密碼。 此帳戶必須是將虛擬機器加入 Active Directory 網域的網域帳戶。 此相同使用者名稱和密碼將會建立於虛擬機器作為本機帳戶。 您稍後可以重設這些本機帳戶。

   >[!NOTE]
   > 如果您要將虛擬機器加入 Azure AD DS 環境，請確定您的網域加入使用者是[AAD DC 系統管理員群組](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成員。
   >
   > 此帳戶也必須是 Azure AD DS 受控網域或 Azure AD 租使用者的一部分。 與您的 Azure AD 租用戶相關聯之外部目錄中的帳戶，在網域加入程序期間無法正確地進行驗證。

1. 選取具有 Active Directory 伺服器連線能力的**虛擬網路**，然後選擇要裝載虛擬機器的子網。
1. 選取**下一步： Windows 虛擬桌面資訊**。

### <a name="windows-virtual-desktop-tenant-information"></a>Windows 虛擬桌面租用戶資訊

針對 [ **Windows 虛擬桌面租使用者資訊**] 索引標籤：

1. 針對 [Windows 虛擬桌面租用戶群組名稱]，輸入包含您租用戶的租用戶群組。 除非系統將特定租用戶群組名稱提供給您，否則將它保留為預設值。
1. 針對 [Windows 虛擬桌面租用戶名稱]，輸入您將在其中建立此主機集區的租用戶名稱。
1. 指定您想要用來以 Windows 虛擬桌面租用戶 RDS 擁有者的身分進行驗證的認證類型。 輸入 UPN 或服務主體和密碼。

   如果您已完成[使用 PowerShell 建立服務主體和角色指派的教學課程](./create-service-principal-role-powershell.md)，請選取 [服務主體]。

1. 針對 [**服務主體**]，針對 [ **AZURE AD 租使用者識別碼**]，為包含服務主體的 Azure AD 實例輸入租使用者系統管理員帳戶。 僅支援具有密碼認證的服務主體。
1. 選取 **[下一步]： [檢查 + 建立]** 。

## <a name="complete-setup-and-create-the-virtual-machine"></a>完成設定並建立虛擬機器

在 [**審查並建立**] 中，檢查安裝資訊。 如果您需要變更某個專案，請返回並進行變更。 當您準備好時，請選取 [**建立**] 以部署您的主機集區。

視您建立的虛擬機器數目而定，此程式可能需要30分鐘或更久的時間才能完成。

>[!IMPORTANT]
> 為了協助保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在虛擬機器上開啟輸入埠3389。 Windows 虛擬桌面不需要開啟的輸入埠3389，使用者就能存取主機集區的虛擬機器。
>
> 如果您必須開啟埠3389以進行疑難排解，建議您使用即時存取。 如需詳細資訊，請參閱[使用即時存取保護您的管理埠](../security-center/security-center-just-in-time.md)。

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(選擇性) 將其他使用者指派給桌面應用程式群組

在 Azure Marketplace 完成建立集區之後，您可以將更多使用者指派給桌面應用程式群組。 如果您不想要新增更多，請略過本節。

若要將使用者指派給桌面應用程式群組：

1. 開啟 PowerShell 視窗。

1. 執行下列命令以登入 Windows 虛擬桌面環境：

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. 使用下列命令，將使用者新增至桌面應用程式群組：

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   使用者的 UPN 應符合 Azure AD 中的使用者身分識別，例如 *user1@contoso.com* 。 如果您想要新增多個使用者，請為每個使用者執行命令。

您新增至桌面應用程式群組的使用者，可以使用支援的遠端桌面用戶端登入 Windows 虛擬桌面，並查看會話桌面的資源。

以下是目前支援的用戶端：

* [Windows 7 和 Windows 10 上的遠端桌面用戶端](connect-windows-7-and-10.md)
* [Windows 虛擬桌面 Web 用戶端](connect-web.md)

## <a name="next-steps"></a>後續步驟

您已建立主機集區和指派的使用者來存取其桌面。 您可以使用 RemoteApp 程式填入主機集區。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱本教學課程：

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
