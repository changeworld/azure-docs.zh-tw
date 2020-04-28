---
title: 使用 Azure Active Directory B2C 設定直接登入 | Microsoft Docs
description: 了解如何預先填入登入名稱，或直接重新導向至社交識別提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a02ad3ea43ae9d91489417bc314e3c23d54a958
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188761"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定直接登入

使用 Azure Active Directory (AD) B2C 為應用程式設定登入時，您可以預先填入登入名稱或直接登入至特定的社交識別提供者，例如 Facebook、LinkedIn 或 Microsoft 帳戶。

## <a name="prepopulate-the-sign-in-name"></a>預先填入登入名稱

在登入使用者旅程圖期間，信賴憑證者應用程式可以將特定使用者或網域名稱作為目標。 以使用者作為目標時，應用程式可以在授權要求中指定 `login_hint` 查詢參數與使用者登入名稱。 Azure AD B2C 會自動填入登入名稱，使用者只需要提供密碼。

![在 URL 中反白顯示 login_hint 查詢參數的 [註冊登入] 頁面](./media/direct-signin/login-hint.png)

使用者可以在登入文字方塊中變更值。

如果您使用自訂原則，則會覆寫 `SelfAsserted-LocalAccountSignin-Email` 技術設定檔。 在 `<InputClaims>` 區段中，將 signInName 宣告的 DefaultValue 設定為 `{OIDC:LoginHint}`。 `{OIDC:LoginHint}` 變數包含 `login_hint` 參數的值。 Azure AD B2C 會讀取 signInName 宣告的值，並預先填入 signInName 文字方塊。

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

## <a name="redirect-sign-in-to-a-social-provider"></a>將登入重新導向至社交提供者

如果您已將應用程式的登入旅程圖設定為包含社交帳戶 (例如 Facebook、LinkedIn 或 Google)，您可以指定 `domain_hint` 參數。 此查詢參數會向 Azure AD B2C 提供應該用於登入的社交識別提供者相關提示。 例如，如果應用程式指定 `domain_hint=facebook.com`，則登入時會直接移至 Facebook 登入頁面。

![在 URL 中反白顯示 domain_hint 查詢參數的 [註冊登入] 頁面](./media/direct-signin/domain-hint.png)

如果您要使用自訂原則，則可以使用任何 `<ClaimsProvider>` 的 `<Domain>domain name</Domain>` XML 元素來設定網域名稱。

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


