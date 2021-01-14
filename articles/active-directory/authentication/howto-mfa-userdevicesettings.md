---
title: 管理 Azure AD Multi-Factor Authentication 的驗證方法-Azure Active Directory
description: 瞭解如何設定 Azure AD 的 Azure Active Directory 使用者設定 Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0be7e50c5cce511fafd7d8b407626bd57659bd
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183127"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>管理 Azure AD Multi-Factor Authentication 的使用者驗證方法

Azure AD 中的使用者有兩組不同的連絡人資訊：  

- 公用設定檔連絡人資訊，它是在使用者設定檔中進行管理，並可讓您的組織成員看見。 針對從內部部署 Active Directory 同步處理的使用者，這項資訊會在內部部署 Windows Server Active Directory Domain Services 中進行管理。
- 驗證方法一律會保持私用，且僅用於驗證，包括 (MFA) 的多重要素驗證。 系統管理員可以在使用者的 [驗證方法] 分頁中管理這些方法，而使用者可以在 [我的帳戶] 的 [安全性資訊] 頁面中管理他們的方法。

當您為使用者管理 Azure AD Multi-Factor Authentication 方法時，驗證系統管理員可以： 

1. 為特定使用者新增驗證方法，包括用於 MFA 的電話號碼。
1. 重設使用者的密碼。
1. 需要使用者重新註冊 MFA。
1. 撤銷現有的 MFA 會話。
1. 刪除使用者現有的應用程式密碼  

## <a name="add-authentication-methods-for-a-user"></a>為使用者新增驗證方法 

您可以透過 Azure 入口網站或 Microsoft Graph 為使用者新增驗證方法。  

> [!NOTE]
> 基於安全性理由，不應該使用公用使用者連絡人資訊欄位來執行 MFA。 相反地，使用者應填入其驗證方法編號以用於 MFA。  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="從 Azure 入口網站新增驗證方法":::

若要透過 Azure 入口網站為使用者新增驗證方法：  

1. 登入 **Azure 入口網站**。 
1. 流覽至 **Azure Active Directory**  >  **使用者**  >  的 **所有使用者**。 
1. 選擇您想要新增驗證方法的使用者，然後選取 [ **驗證方法**]。  
1. 在視窗頂端，選取 [ **+ 新增驗證方法**]。
   1. 選取 (電話號碼或電子郵件) 的方法。 電子郵件可用於自我密碼重設，但不能用於驗證。 新增電話號碼時，請選取手機類型，然後輸入有效格式的電話號碼 (例如 + 1 4255551234) 。
   1. 選取 [新增]。

> [!NOTE]
> 預覽體驗可讓系統管理員為使用者新增任何可用的驗證方法，而原始體驗只允許更新電話和備用電話方法。

### <a name="manage-methods-using-powershell"></a>使用 PowerShell 管理方法：  

使用下列命令來安裝 Signins PowerShell 模組。 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

針對特定使用者列出以電話為基礎的驗證方法。

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

為特定使用者建立行動電話驗證方法。

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

移除使用者的特定電話方法

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

您也可以使用 Microsoft Graph Api 來管理驗證方法，您可以在檔中找到詳細資訊 [Azure AD 驗證方法 API 總覽](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>管理使用者驗證選項

如果您已獲指派 *驗證系統管理員* 角色，您可以要求使用者重設其密碼、重新註冊 MFA，或從其使用者物件撤銷現有的 mfa 會話。 若要管理使用者設定，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左邊的 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 選擇您想要對其執行動作的使用者，然後選取 [ **驗證方法**]。 在視窗頂端，選擇下列其中一個使用者選項：
   - **重設密碼** 會重設使用者的密碼，並指派暫時密碼，必須在下次登入時變更。
   - **需要重新註冊 MFA** 才能讓使用者下次登入時，要求他們設定新的 MFA 驗證方法。
   
      > [!NOTE]
      > 當系統管理員需要重新註冊 MFA 時，不會刪除使用者目前註冊的驗證方法。 在使用者重新註冊 MFA 之後，我們建議他們檢查安全性資訊，並刪除任何先前註冊的驗證方法，而這些方法不再可用。
   
   - **撤銷 MFA 會話** 會清除使用者記住的 mfa 會話，並要求他們在下次裝置上的原則需要時執行 mfa。
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="從 Azure 入口網站管理驗證方法":::

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼

針對已定義應用程式密碼的使用者，系統管理員也可以選擇刪除這些密碼，導致這些應用程式中的舊版驗證失敗。 如果您需要為使用者提供協助，或需要重設其驗證方法，則可能需要執行這些動作。 與這些應用程式密碼相關聯的非瀏覽器應用程式將會停止運作，直到建立新的應用程式密碼為止。 

若要刪除使用者的應用程式密碼，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側的 [ **Azure Active Directory**  >  **使用者**  >  **所有使用者**]。
1. 選取 [多重要素驗證]。 您可能必須捲動到右邊才能看到此功能表選項。 選取下方的範例螢幕擷取畫面，以查看完整的 Azure 入口網站視窗和功能表位置： [ ![ 從 Azure AD 的 [使用者] 視窗中選取 [Multi-Factor Authentication]。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 勾選您想要管理之使用者旁邊的方塊。 快速步驟選項的清單會出現在右側。
1. 選取 [ **管理使用者設定**]，然後核取 [ **刪除所選使用者產生的所有現有應用程式密碼**] 核取方塊，如下列範例所示： ![ 刪除所有現有的應用程式密碼](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. 選取 [ **儲存**]，然後 **關閉**。

## <a name="next-steps"></a>後續步驟

本文說明如何設定個別的使用者設定。 若要設定整體 Azure AD Multi-Factor Authentication 服務設定，請參閱設定 [Azure AD Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)。

如果您的使用者需要協助，請參閱 [Azure AD Multi-Factor Authentication 的使用者指南](../user-help/multi-factor-authentication-end-user-first-time.md)。
