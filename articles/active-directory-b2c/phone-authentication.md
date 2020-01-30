---
title: 使用自訂原則進行手機註冊和登入
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 中使用自訂原則，將文字訊息中的單次密碼傳送給應用程式使用者的手機。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8cb0340d9e04db2bfbf088bce9505351d7588cd9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840327"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>在 Azure AD B2C 中使用自訂原則設定電話註冊和登入

Azure Active Directory B2C （Azure AD B2C）中的電話註冊和登入可讓您的使用者透過使用文字訊息傳送至其電話的一次性密碼（OTP）來註冊並登入您的應用程式。 單次密碼可協助將使用者忘記或破解密碼的風險降至最低。

請遵循本文中的步驟來使用自訂原則，讓您的客戶可以使用傳送到其電話的一次性密碼來註冊和登入您的應用程式。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>必要條件

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 在您的租使用者中[註冊的 Web 應用程式](tutorial-register-applications.md)
* 已上傳至您租使用者的[自訂原則](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>取得電話註冊 & 登入入門套件

一開始請先更新電話註冊和登入自訂原則檔案，以與您的 Azure AD B2C 租使用者搭配使用。

下列步驟假設您已完成[必要條件](#prerequisites)，並已將[自訂原則入門套件][starter-pack]存放庫複製到本機電腦。

1. 在您的入門套件存放庫的本機複本中尋找[電話註冊和登入自訂原則][starter-pack-phone]檔案，或直接下載。 XML 原則檔案位於下列目錄：

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 在每個檔案中，將字串 `yourtenant` 取代為 Azure AD B2C 租使用者的名稱。 例如，如果您的 B2C 租使用者名稱是*contosob2c*，則 `yourtenant.onmicrosoft.com` 的所有實例都會 `contosob2c.onmicrosoft.com`。

1. 完成在[Azure Active Directory B2C 中開始使用自訂](custom-policy-get-started.md)原則中的[將應用程式識別碼新增至自訂原則](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)一節中的步驟。 在此情況下，請使用完成必要條件*IdentityExperienceFramework*和*ProxyIdentityExperienceFramework*時所註冊的兩個應用程式的**應用程式（用戶端）識別碼**，更新 `/phone-number-passwordless/` **`Phone_Email_Base.xml`** 。

## <a name="upload-the-policy-files"></a>上傳原則檔案

1. 登入[Azure 入口網站](https://portal.azure.com)，然後流覽至您的 Azure AD B2C 租使用者。
1. 在 [**原則**] 底下，選取 [ **Identity Experience Framework**]。
1. 選取 **[上傳自訂原則**]。
1. 依下列順序上傳原則檔案：
    1. *Phone_Email_Base .xml*
    1. *SignUpOrSignInWithPhone .xml*
    1. *SignUpOrSignInWithPhoneOrEmail .xml*
    1. *ProfileEditPhoneOnly .xml*
    1. *ProfileEditPhoneEmail .xml*
    1. *ChangePhoneNumber .xml*
    1. *PasswordResetEmail .xml*

當您上傳每個檔案時，Azure 會在 `B2C_1A_`加上前置詞。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在 [**自訂原則**] 底下，選取 [ **B2C_1A_SignUpOrSignInWithPhone**]。
1. 在 [**選取應用程式**] 下，選取您在完成必要條件時註冊的*webapp1*應用程式。
1. 針對 [**選取回復 url**]，選擇 [`https://jwt.ms`]。
1. 選取 [**立即執行**]，然後使用電子郵件地址或電話號碼進行註冊。
1. 再次選取 [**立即執行**]，然後使用相同的帳戶登入，以確認您擁有正確的設定。

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到電話註冊和登入自訂原則入門套件（以及其他入門套件）：

[Azure-範例/active directory-b2c-自訂原則-active-directory-b2c-custom-policy-starterpack/案例/電話號碼-無密碼][starter-pack-phone]

入門套件原則檔案會使用多重要素驗證技術設定檔和電話號碼宣告轉換：

* [定義 Azure 多因素驗證技術設定檔](multi-factor-auth-technical-profile.md)
* [定義電話號碼宣告轉換](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
