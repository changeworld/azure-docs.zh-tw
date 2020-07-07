---
title: 自訂自助式密碼重設-Azure Active Directory
description: 瞭解如何自訂 Azure AD 自助式密碼重設的使用者顯示和體驗選項
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81394250"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>自訂 Azure Active Directory 自助式密碼重設的使用者體驗

自助式密碼重設（SSPR）可讓使用者 Azure Active Directory （Azure AD）變更或重設其密碼的能力，而不需要系統管理員或服務台的介入。 如果使用者的帳戶遭到鎖定，或使用者忘記其密碼，他們可以依照提示自行解除封鎖，並繼續工作。 當使用者無法登入其裝置或應用程式時，這項功能將可減少技術服務中心的通話量，並避免失去生產力。

若要改善使用者的 SSPR 體驗，您可以自訂密碼重設頁面、電子郵件通知或登入頁面的外觀與風格。 這些自訂選項可讓您清楚瞭解他們在正確位置的使用者，並讓他們能夠安心存取公司資源。
    
本文說明如何自訂使用者、公司商標和 AD FS 登入頁面連結的 SSPR 電子郵件連結。

## <a name="customize-the-contact-your-administrator-link"></a>自訂 [請連絡您的系統管理員] 連結

若要協助使用者與自助式密碼重設聯繫以取得協助，請在密碼重設入口網站中顯示「聯絡您的系統管理員」連結。 如果使用者選取此連結，則會執行兩個動作的其中一項：

* 如果此連絡人連結處於預設狀態，則會傳送電子郵件給您的系統管理員，並要求他們提供變更使用者密碼的協助。 下列範例電子郵件會顯示此預設的電子郵件訊息：

    ![重設傳送給系統管理員之電子郵件的範例要求](./media/howto-sspr-customization/sspr-contact-admin.png)

* 若自訂，則會將使用者傳送至系統管理員所指定的網頁或電子郵件地址，以取得協助。
    * 如果您自訂此專案，建議您將此設定為已熟悉支援的使用者。

    > [!WARNING]
    > 如果您使用需要密碼重設的電子郵件地址和帳戶來自訂此設定，使用者可能會無法尋求協助。

### <a name="default-email-behavior"></a>預設電子郵件行為

預設的連絡人電子郵件會依下列順序傳送給收件者：

1. 如果指派了*技術服務管理員*角色或*密碼管理員*角色，系統管理員會收到這些角色的通知。
1. 如果未指派技術支援系統管理員或密碼管理員，則會通知具有*使用者管理員*角色的系統管理員。
1. 如果未指派任何先前的角色，則會通知*全域系統管理員*。

在所有情況下，最多 100 位收件者會收到通知。

若要深入了解不同的系統管理員角色，以及如何指派這些角色，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="disable-contact-your-administrator-emails"></a>停用 [請連絡您的系統管理員] 電子郵件

如果您的組織不想要通知系統管理員有關密碼重設要求，可以使用下列設定選項：

* 自訂技術服務人員連結，以提供可讓使用者取得協助的 Web URL 或 mailto︰位址。 此選項位於 [**密碼重設**  >  **自**  >  **定義]、[技術支援電子郵件或 URL**] 下方。
* 為所有使用者啟用自助式密碼重設。 此選項位於 [**密碼重設**]  >  **屬性**底下。 如果您不想讓使用者重設其自己的密碼，可以將存取範圍設定為空群組。 *我們不建議您選擇此選項。*

## <a name="customize-the-sign-in-page-and-access-panel"></a>自訂登入頁面和存取面板

您可以自訂登入頁面，例如新增標誌，連同符合您公司商標的影像一起顯示。 如需有關如何設定公司商標的詳細資訊，請參閱[在 Azure AD 中將公司商標新增至登入頁面](../fundamentals/customize-branding.md)。

您選擇的圖形會在下列情況下顯示：

* 在使用者輸入其使用者名稱之後
* 如果使用者存取自訂的 URL，則會透過下列方式：
   * 將參數傳遞 `whr` 至密碼重設頁面，例如`https://login.microsoftonline.com/?whr=contoso.com`
   * 將參數傳遞 `username` 至密碼重設頁面，例如`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>目錄名稱

若要讓使用者更容易瞭解，您可以在入口網站和自動化通訊中變更組織名稱。 若要變更 Azure 入口網站中的 [目錄名稱] 屬性，請流覽至**Azure Active Directory**  >  **屬性**]。 這個易記的組織名稱選項最能在自動化的電子郵件中看到，如下列範例所示：

* 電子郵件中的易記名稱，例如「*Microsoft 代表 CONTOSO 示範*」
* 電子郵件中的主旨行，例如「*CONTOSO 示範帳戶電子郵件驗證碼*」

## <a name="customize-the-ad-fs-sign-in-page"></a>自訂 AD FS 登入頁面

如果您針對使用者登入事件使用 Active Directory 同盟服務（AD FS），您可以使用[新增登入頁面描述](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)文章中的指導方針，將連結新增至登入頁面。

為使用者提供頁面的連結，讓他們輸入 SSPR 工作流程，例如 *https://passwordreset.microsoftonline.com* 。 若要新增 AD FS 登入頁面的連結，請在您的 AD FS 伺服器上使用下列命令：

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>後續步驟

若要瞭解環境中 SSPR 的使用方式，請參閱[Azure AD 密碼管理的報告選項](howto-sspr-reporting.md)。

如果您或使用者有 SSPR 的問題，請參閱針對[自助式密碼重設進行疑難排解](active-directory-passwords-troubleshoot.md)
