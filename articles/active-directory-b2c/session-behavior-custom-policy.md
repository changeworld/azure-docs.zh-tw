---
title: 使用自訂原則設定會話行為-Azure Active Directory B2C |Microsoft Docs
description: 在 Azure Active Directory B2C 中使用自訂原則來設定會話行為。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31257d795dbd06da65e3d07e18a16d9bdf7e782a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961097"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定會話行為

Azure Active Directory B2C (Azure AD B2C) 中的[單一登入 (SSO) 會話](session-overview.md)管理，可讓系統管理員在使用者已通過驗證之後，控制與使用者的互動。 例如，系統管理員可以控制是否要顯示身分識別提供者的選擇，或是否需要再次輸入帳戶詳細資料。 本文說明如何設定 Azure AD B2C 的 SSO 設定。

## <a name="session-behavior-properties"></a>會話行為屬性

您可以使用下列屬性管理 Web 應用程式工作階段︰

- **Web 應用程式工作階段存留期 (分鐘)** - 在成功通過驗證時，使用者瀏覽器中所儲存 Azure AD B2C 工作階段 Cookie 的存留期。
  - 預設值 = 86400 秒 (1440 分鐘) 。
  - 最小 (包含) = 900 秒 (15 分鐘) 。
  - 最大 (內含) = 86400 秒 (1440 分鐘) 。
- **Web 應用程式會話超時** - [會話到期類型](session-overview.md#session-expiry-type)、 *回復*或 *絕對*。 
- **單一登入** 設定-單一登入的 [會話範圍](session-overview.md#session-scope) (SSO) 跨多個應用程式的行為，以及您 Azure AD B2C 租使用者中的使用者流程。 

## <a name="configure-the-properties"></a>設定屬性

若要變更工作階段行為和 SSO 組態，您可以在 [RelyingParty](relyingparty.md) 元素內新增 **UserJourneyBehaviors** 元素。  **UserJourneyBehaviors** 元素必須緊跟著 **DefaultUserJourney**。 **UserJourneyBehavors**元素看起來應該如下列範例所示：

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="configure-sign-out-behavior"></a>設定登出行為

### <a name="secure-your-logout-redirect"></a>保護登出重新導向

登出之後，會將使用者重新導向至參數中所指定的 URI `post_logout_redirect_uri` ，而不論已針對應用程式指定的回復 url。 但是，如果傳遞的是有效的， `id_token_hint` 而且 **登出要求中的要求識別碼權杖** 已開啟，Azure AD B2C 會確認的值 `post_logout_redirect_uri` 符合其中一個應用程式設定的重新導向 uri，然後再執行重新導向。 如果未針對應用程式設定相符的回復 URL，則會顯示錯誤訊息，且不會重新導向使用者。 

若要在登出要求中要求識別碼權杖，請在[RelyingParty](relyingparty.md)元素內加入**UserJourneyBehaviors**專案。 然後將**SingleSignOn**元素的**EnforceIdTokenHintOnLogout**設定為 `true` 。 **UserJourneyBehaviors**元素看起來應該如下列範例所示：

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

若要設定應用程式登出 URL：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 **應用程式註冊**，然後選取您的應用程式。
1. 選取 [驗證]。
1. 在 [ **登出 URL** ] 文字方塊中，輸入您的 post 登出重新導向 URI，然後選取 [ **儲存**]。

### <a name="single-sign-out"></a>單一登出

#### <a name="configure-the-applications"></a>設定應用程式

當您將使用者重新導向至 OAuth2 和 SAML 通訊協定) 的 Azure AD B2C 登出端點 (時，Azure AD B2C 會從瀏覽器清除使用者的會話。  若要允許 [單一登出](session-overview.md#single-sign-out)，請 `LogoutUrl` 從 Azure 入口網站設定應用程式的：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 在頁面右上角按一下您的帳戶，以選擇您的 Azure AD B2C 目錄。
1. 在左側功能表中，選擇 [ **Azure AD B2C**]，選取 [ **應用程式註冊**]，然後選取您的應用程式。
1. 選取 [驗證]。
1. 在 [ **登出 URL** ] 文字方塊中，輸入您的 post 登出重新導向 URI，然後選取 [ **儲存**]。

#### <a name="configure-the-token-issuer"></a>設定權杖簽發者 

若要支援單一登出，JWT 和 SAML 的權杖簽發者技術設定檔必須指定：

- 通訊協定名稱，例如 `<Protocol Name="OpenIdConnect" />`
- 會話技術設定檔的參考，例如 `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` 。

下列範例說明使用單一登出的 JWT 和 SAML 權杖簽發者：

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure AD B2C 會話](session-overview.md)。
