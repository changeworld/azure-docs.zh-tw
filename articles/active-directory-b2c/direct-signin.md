---
title: 使用 Azure Active Directory B2C 設定直接登入 | Microsoft Docs
description: 了解如何預先填入登入名稱，或直接重新導向至社交識別提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2342ed978204284bee4d2be0f1c983aa10ade36
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585032"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定直接登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

使用 Azure Active Directory (AD) B2C 為應用程式設定登入時，您可以預先填入登入名稱或直接登入至特定的社交識別提供者，例如 Facebook、LinkedIn 或 Microsoft 帳戶。

## <a name="prepopulate-the-sign-in-name"></a>預先填入登入名稱

在登入使用者旅程圖期間，信賴憑證者應用程式可以將特定使用者或網域名稱作為目標。 以使用者作為目標時，應用程式可以在授權要求中指定 `login_hint` 查詢參數與使用者登入名稱。 Azure AD B2C 會自動填入登入名稱，使用者只需要提供密碼。

![在 URL 中醒目提示 login_hint query 參數的註冊登入頁面](./media/direct-signin/login-hint.png)

使用者可以在登入文字方塊中變更值。

::: zone pivot="b2c-custom-policy"

若要支援登入提示參數，請覆寫 `SelfAsserted-LocalAccountSignin-Email` 技術設定檔。 在 `<InputClaims>` 區段中，將 signInName 宣告的 DefaultValue 設定為 `{OIDC:LoginHint}`。 `{OIDC:LoginHint}` 變數包含 `login_hint` 參數的值。 Azure AD B2C 會讀取 signInName 宣告的值，並預先填入 signInName 文字方塊。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>將登入重新導向至社交提供者

如果您已將應用程式的登入旅程圖設定為包含社交帳戶 (例如 Facebook、LinkedIn 或 Google)，您可以指定 `domain_hint` 參數。 此查詢參數會向 Azure AD B2C 提供應該用於登入的社交識別提供者相關提示。 例如，如果應用程式指定 `domain_hint=facebook.com`，則登入時會直接移至 Facebook 登入頁面。

![在 URL 中醒目提示 domain_hint query 參數的註冊登入頁面](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

網域提示查詢字串參數可以設定為下列其中一個網域：

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- 如需 [一般 OpenID Connect](identity-provider-generic-openid-connect.md)，請參閱 [網域提示](identity-provider-generic-openid-connect.md#response-mode)。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要支援 domain hing 參數，您可以使用 any 的 XML 元素來設定功能變數名稱 `<Domain>domain name</Domain>` `<ClaimsProvider>` 。

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

