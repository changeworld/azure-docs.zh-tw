---
title: 自訂自助服務密碼重置 - Azure 活動目錄
description: 瞭解如何自訂使用者顯示和 Azure AD 自助服務密碼重置體驗選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394250"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>自訂 Azure 活動目錄自助服務密碼重置的使用者體驗

自助服務密碼重置 (SSPR) 使 Azure 活動目錄 (Azure AD) 中的使用者無需管理員或幫助台參與即可更改或重置其密碼。 如果使用者的帳戶遭到鎖定，或使用者忘記其密碼，他們可以依照提示自行解除封鎖，並繼續工作。 當使用者無法登入其裝置或應用程式時，這項功能將可減少技術服務中心的通話量，並避免失去生產力。

要改善使用者的 SSPR 體驗,您可以自定義密碼重置頁面、電子郵件通知或登錄頁面的外觀。 這些自定義選項可讓您向使用者表明他們在正確的位置,並讓他們確信他們正在存取公司資源。
    
本文介紹如何自定義使用者、公司品牌和 AD FS 登錄頁面連結的 SSPR 電子郵件連結。

## <a name="customize-the-contact-your-administrator-link"></a>自訂 [請連絡您的系統管理員] 連結

為了幫助使用者在自助密碼重置方面尋求幫助,密碼重置門戶中將顯示"聯繫管理員"連結。 如果使用者選擇此連結,它將執行以下兩項操作之一:

* 如果此連絡人連結處於預設狀態,將向管理員發送電子郵件,並要求他們提供更改使用者密碼的説明。 以下範例電子郵件顯示此預設電子郵件:

    ![重置發送給管理員的電子郵件的範例請求](./media/howto-sspr-customization/sspr-contact-admin.png)

* 如果自定義,則將使用者發送到管理員指定的網頁或電子郵寄地址以獲取説明。
    * 如果您自定義此內容,我們建議您將此設置為使用者已熟悉的支援內容。

    > [!WARNING]
    > 如果使用需要重置密碼的電子郵寄地址和帳戶自訂此設置,使用者可能無法尋求説明。

### <a name="default-email-behavior"></a>預設電子郵件行為

預設聯絡人電子郵件按以下順序發送給收件者:

1. 如果分配了*幫助台管理員*角色或*密碼管理員*角色,則會通知具有這些角色的管理員。
1. 如果未分配説明台管理員或密碼管理員,則會通知具有*使用者管理員*角色的管理員。
1. 如果未分配以前的角色,則會通知*全域管理員*。

在所有情況下，最多 100 位收件者會收到通知。

若要深入了解不同的系統管理員角色，以及如何指派這些角色，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="disable-contact-your-administrator-emails"></a>停用 [請連絡您的系統管理員] 電子郵件

如果您的組織不想通知管理員密碼重置要求,可以使用以下設定選項:

* 自訂技術服務人員連結，以提供可讓使用者取得協助的 Web URL 或 mailto︰位址。 此選項在**密碼重置** > **自訂** > **自定義説明台電子郵件或 URL**下。
* 為所有使用者啟用自助服務密碼重置。 此選項在**密碼重置** > **屬性**下。 如果您不想讓使用者重設其自己的密碼，可以將存取範圍設定為空群組。 *我們不推薦此選項。*

## <a name="customize-the-sign-in-page-and-access-panel"></a>自訂登入頁和存取面板

您可以自定義登錄頁面,例如添加與適合公司品牌的圖像一起顯示的徽標。 有關如何設定公司品牌的詳細資訊,請參閱在 Azure [AD 中將公司品牌添加到登入頁](../fundamentals/customize-branding.md)。

您選擇的圖形會在下列情況下顯示：

* 在使用者輸入其使用者名稱之後
* 如果使用者存取自訂的 URL，則會透過下列方式：
   * 透過參數`whr`傳遞到密碼重置頁,例如`https://login.microsoftonline.com/?whr=contoso.com`
   * 透過參數`username`傳遞到密碼重置頁,例如`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>目錄名稱

為了使事情看起來更便於使用,您可以在門戶和自動通信中更改組織名稱。 要變更 Azure 入口名稱屬性,請瀏覽到**Azure 的目錄** > **屬性**。 此友好的組織名稱選項在自動電子郵件中最為明顯,如以下範例所示:

* 電子郵件中的友好名稱,例如「*微軟代表CONTOSO演示*」
* 電子郵件中的主題行,例如 *"CONTOSO 演示帳戶電子郵件驗證碼*"

## <a name="customize-the-ad-fs-sign-in-page"></a>自訂 AD FS 登入頁

如果您使用 Active 目錄來執行服務 (AD FS) 進行使用者登入事件,則可以使用文章中的指導新增指向登入頁[的連結](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)。

為使用者提供指向頁面的連結,以便他們進入 SSPR 工作流*https://passwordreset.microsoftonline.com*,例如 。 要向 AD FS 登入頁面新增連結,請使用 AD FS 伺服器上的以下指令:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>後續步驟

要瞭解 SSPR 在環境中的使用,請參閱[Azure AD 密碼管理的報告選項](howto-sspr-reporting.md)。

如果您或使用者對 SSPR 出現問題,請參閱[排除自助服務密碼重置故障](active-directory-passwords-troubleshoot.md)
