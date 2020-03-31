---
title: 自訂自助服務密碼重設 - Azure 活動目錄
description: Azure AD 自助式密碼重設的自訂選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979467"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>自訂 Azure AD 的自助式密碼重設功能

想要在 Azure Active Directory (Azure AD) 中部署自助式密碼重設 (SSPR) 的 IT 專業人員可以自訂體驗以符合其使用者需求。

## <a name="customize-the-contact-your-administrator-link"></a>自訂 [請連絡您的系統管理員] 連結

自助服務密碼重設使用者在密碼重設門戶中具有可用的"聯繫管理員"連結。 如果使用者選擇此連結，它將執行以下兩項操作之一：

* 如果處於預設狀態：
   * 電子郵件將發送給管理員，並要求他們説明更改使用者的密碼。 請參閱下面的[示例電子郵件](#sample-email)。
* 如果自訂：
   * 將使用者發送到管理員指定的網頁或電子郵件地址以獲取説明。

> [!TIP]
> 如果您自訂此內容，我們建議您將此設置為使用者已熟悉的支援內容

> [!WARNING]
> 如果使用需要重置密碼的電子郵件地址和帳戶自訂此設置，使用者可能無法尋求説明。

### <a name="sample-email"></a>範例電子郵件

![重置發送給管理員的電子郵件的示例請求][Contact]

這封連絡人電子郵件會依下列順序傳送給下列收件者︰

1. 如果分配了**説明台管理員**角色或**密碼管理員**角色，則會通知具有這些角色的管理員。
1. 如果未分配説明台管理員或密碼管理員，則會通知具有**使用者管理員**角色的管理員。
1. 如果未分配以前的角色，則會通知**全域管理員**。

在所有情況下，最多 100 位收件者會收到通知。

若要深入了解不同的系統管理員角色，以及如何指派這些角色，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="disable-contact-your-administrator-emails"></a>停用 [請連絡您的系統管理員] 電子郵件

如果您的組織不想傳送密碼重設要求通知給系統管理員，則您可以啟用下列設定：

* 對所有使用者啟用自助式密碼重設。 此選項在**密碼重設** > **屬性**下。 如果您不想讓使用者重設其自己的密碼，可以將存取範圍設定為空群組。 *我們不推薦此選項。*
* 自訂技術服務人員連結，以提供可讓使用者取得協助的 Web URL 或 mailto︰位址。 此選項在**密碼重設** > **自訂** > **自訂説明台電子郵件或 URL**下。

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>自訂 SSPR 的 AD FS 登入頁面

「Active Directory 同盟服務」(AD FS) 系統管理員可以使用[新增登入頁面描述](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) \(機器翻譯\) 一文中所提供的指引，新增其登入頁面的連結。

若要新增 AD FS 登入頁面的連結，請在您的 AD FS 伺服器上使用下列命令。 使用者可以使用此頁面來進入 SSPR 工作流程。

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>自訂登入頁面和存取面板的外觀與風格

您可以自訂登入頁面。 您可以新增與符合您公司商標的影像一起顯示的標誌。

您選擇的圖形會在下列情況下顯示：

* 在使用者輸入其使用者名稱之後
* 如果使用者存取自訂的 URL，則會透過下列方式：
   * 通過將`whr`參數傳遞到密碼重設頁，例如`https://login.microsoftonline.com/?whr=contoso.com`
   * 通過將`username`參數傳遞到密碼重設頁，例如`https://login.microsoftonline.com/?username=admin@contoso.com`

如需有關如何設定公司商標的詳細資料，請參閱[將公司商標新增至 Azure AD 中的登入頁面](../fundamentals/customize-branding.md)一文。

### <a name="directory-name"></a>目錄名稱

您可以在**Azure 活動目錄** > **屬性**下更改目錄名稱屬性。 您可以顯示一個會在入口網站及自動化通訊中顯示的易記組織名稱。 這個選項最常以下列形式出現在自動化電子郵件中：

* 電子郵件中的易記名稱，例如「Microsoft 代表 CONTOSO 示範」
* 電子郵件中的主旨列，例如「CONTOSO 示範帳戶電子郵件驗證碼」

## <a name="next-steps"></a>後續步驟

* [如何完成 SSPR 成功首度發行？](howto-sspr-deployment.md)
* [重置或更改密碼](../user-help/active-directory-passwords-update-your-own-password.md)
* [註冊進行自助服務密碼重設](../user-help/active-directory-passwords-reset-register.md)
* [您是否有許可問題？](concept-sspr-licensing.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](howto-sspr-authenticationdata.md)
* [哪些驗證方法可供使用者使用？](concept-sspr-howitworks.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](concept-sspr-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](howto-sspr-writeback.md)
* [如何回報 SSPR 中的活動？](howto-sspr-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](concept-sspr-howitworks.md)
* [我覺得有些東西壞了。如何對 SSPR 進行故障排除？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "請與管理員聯繫，瞭解重置密碼電子郵件示例的説明"
