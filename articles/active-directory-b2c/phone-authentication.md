---
title: 使用自訂原則進行電話註冊和登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 的自訂原則，將文字訊息中的單次密碼 (OTP) 傳送給應用程式使用者的手機。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9d4fa80f669493e4dc47a9ad0f9bfe9390d4ab24
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953571"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>使用 Azure AD B2C 中的自訂原則設定電話註冊和登入

Azure Active Directory B2C (Azure AD B2C) 中的手機註冊和登入，可讓您的使用者使用一次性密碼 (OTP) 將文字訊息傳送到他們的電話，來註冊和登入您的應用程式。 單次密碼可協助將您的使用者忘記或密碼遭到入侵的風險降至最低。

遵循本文中的步驟，使用自訂原則讓您的客戶能夠使用傳送至電話的一次性密碼來註冊和登入您的應用程式。

## <a name="pricing"></a>定價

單次密碼會使用 SMS 文字訊息傳送給您的使用者，而您可能會針對每個傳送的訊息付費。 如需定價資訊，請參閱 [Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)的 **個別費用** 區段。

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>手機註冊與登入的使用者體驗

使用電話註冊和登入，使用者就可以使用電話號碼作為其主要識別碼來註冊應用程式。 以下說明使用者在註冊和登入期間的體驗。

> [!NOTE]
> 強烈建議您在註冊和登入體驗中包含同意資訊，類似于下列範例文字。 此範例文字僅供參考之用。 請參閱 [CTIA 網站](https://www.ctia.org/programs) 上的簡短程式碼監視手冊，並洽詢您自己的法律或合規性專家，以取得最終文字和功能設定的指引，以符合您自己的合規性需求：
>
> *藉由提供您的電話號碼，您同意收到文字訊息所傳送的單次密碼，協助您登入 *&lt; 插入：您的應用程式名稱 &gt;*。可能適用標準訊息和資料費率。*
>
> *&lt;插入：隱私權聲明的連結&gt;*<br/>*&lt;插入：服務條款的連結&gt;*

若要新增您自己的同意資訊，請自訂下列範例，並將它包含在 LocalizedResources 中，以供自我判斷頁面的 ContentDefinition 所使用的顯示控制項， (手機註冊 & 登入入門套件) 中的 Phone-Email-Base.xml 檔案：

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard messsage and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>電話註冊體驗

如果使用者還沒有您應用程式的帳戶，可以選擇 [ **立即註冊** ] 連結建立一個帳戶。 註冊頁面隨即出現，使用者會在其中選取其 **國家/地區**、輸入其電話號碼，然後選取 [ **傳送程式碼**]。

![使用者開始電話註冊](media/phone-authentication/phone-signup-start.png)

一次性驗證碼會傳送至使用者的電話號碼。 使用者在註冊頁面上輸入 **驗證碼** ，然後選取 [ **驗證程式代碼**]。  (如果使用者無法取得程式碼，則可以選取 [ **傳送新程式碼**]。 ) 

![使用者在電話註冊期間驗證程式代碼](media/phone-authentication/phone-signup-verify-code.png)

 使用者會在註冊頁面上輸入任何其他所要求的資訊，例如 **顯示名稱**、 **名字** 和 **姓氏** (國家/地區與電話號碼仍會填入) 。 如果使用者想要使用不同的電話號碼，可以選擇 [ **變更號碼** ] 重新開機註冊。 完成時，使用者會選取 [ **繼續**]。

![使用者提供其他資訊](media/phone-authentication/phone-signup-additional-info.png)

接下來，系統會要求使用者提供修復電子郵件。 使用者輸入其電子郵件地址，然後選取 [ **傳送驗證碼**]。 系統會將程式碼傳送至使用者的電子郵件收件匣，並在 [ **驗證碼** ] 方塊中輸入。 然後，使用者選取 [ **驗證程式代碼**]。 

驗證程式代碼之後，使用者會選取 [ **建立** ] 以建立他們的帳戶。 或者，如果使用者想要使用不同的電子郵件地址，他們可以選擇 [ **變更電子郵件**]。

![使用者建立帳戶](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>手機登入體驗

如果使用者有一個具有電話號碼的現有帳戶做為其識別碼，使用者會輸入他們的電話號碼，然後選取 [ **繼續**]。 他們會選取 [ **繼續**] 來確認國家/地區和電話號碼，並將一次性驗證碼傳送到其電話。 使用者輸入驗證碼，然後選取 [ **繼續** 登入]。

![手機登入使用者體驗](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>刪除使用者帳戶

在某些情況下，您可能需要從 Azure AD B2C 目錄刪除使用者和相關聯的資料。 如需有關如何透過 Azure 入口網站刪除使用者帳戶的詳細資訊，請參閱 [這些指示](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete)。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Prerequisites

設定 OTP 之前，您需要有下列資源。

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 在您的租使用者中[註冊的 Web 應用程式](tutorial-register-applications.md)
* 已上傳至您租使用者的[自訂原則](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>取得電話註冊 & 登入入門套件

首先，更新電話註冊和登入自訂原則檔案，以搭配您的 Azure AD B2C 租使用者使用。

下列步驟假設您已完成 [必要條件](#prerequisites) ，並已將 [自訂原則入門套件][starter-pack] 存放庫複製到您的本機電腦。

1. 在您的「入門套件」存放庫的本機複製中尋找 [電話註冊和登入自訂原則][starter-pack-phone] 檔案，或直接下載。 XML 原則檔位於下列目錄：

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 在每個檔案中，將字串取代 `yourtenant` 為您 Azure AD B2C 租使用者的名稱。 例如，如果您的 B2C 租使用者名稱是 *contosob2c*，則的所有實例都是 `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` 。

1. 完成「[開始使用自訂](custom-policy-get-started.md)原則」 Azure Active Directory B2C 中的 [[將應用程式識別碼新增至自訂原則](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)] 區段中的步驟。 在此情況下，請更新 `/phone-number-passwordless/` **`Phone_Email_Base.xml`** **應用程式 (用戶端)** 您在完成必要條件、 *IdentityExperienceFramework* 和 *ProxyIdentityExperienceFramework* 時所註冊的兩個應用程式的識別碼。

## <a name="upload-the-policy-files"></a>上傳原則檔案

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的 Azure AD B2C 租使用者。
1. 在 [原則] 之下，選取 [Identity Experience Framework]。
1. 選取 [上傳自訂原則]。
1. 依下列順序上傳原則檔案：
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

當您上傳每個檔案時，Azure 會新增前置詞 `B2C_1A_` 。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在 [ **自訂原則**] 底下，選取 [ **B2C_1A_SignUpOrSignInWithPhone**]。
1. 在 [ **選取應用程式**] 下，選取您在完成必要條件時所註冊的 *webapp1* 應用程式。
1. 在 [ **選取回復 url**] 中，選擇 `https://jwt.ms` 。
1. 選取 [ **立即執行** ]，然後使用電子郵件地址或電話號碼註冊。
1. 選取 [ **立即執行** 一次] 並使用相同的帳戶登入，以確認您有正確的設定。

## <a name="get-user-account-by-phone-number"></a>依電話號碼取得使用者帳戶

使用電話號碼註冊但未提供復原電子郵件地址的使用者，會記錄在您的 Azure AD B2C 目錄中，並以其電話號碼作為其登入名稱。 如果使用者接著想要變更他們的電話號碼，您的支援人員或支援小組必須先找出他們的帳戶，然後更新他們的電話號碼。

您可以使用 [Microsoft Graph](manage-user-accounts-graph-api.md)來尋找使用者的電話號碼 (登入名稱) ：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

例如：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到電話註冊與登入自訂原則入門套件 (以及其他入門套件) ： [Azure 範例/active directory-b2c-active-directory-b2c-custom-policy-starterpack/案例/電話號碼-無密碼][starter-pack-phone] 入門套件原則檔使用多重要素驗證技術設定檔和電話號碼宣告轉換：
* [定義 Azure AD Multi-Factor Authentication 技術設定檔](multi-factor-auth-technical-profile.md)
* [定義電話號碼宣告轉換](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless