---
title: 設定會話行為-Azure Active Directory B2C |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory B2C 中的會話行為。
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
ms.openlocfilehash: ad9bd8dec94660d94cf3a106d31dafdad06f47a8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584505"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定會話行為

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

單一登入 (SSO) 在使用者于 Azure Active Directory B2C (Azure AD B2C) 的應用程式之間登入時，增加安全性和便利性。 本文說明 Azure AD B2C 中使用的單一登入方法，並協助您在設定原則時選擇最適當的 SSO 方法。

使用單一登入時，使用者只要使用單一帳戶登入一次，就可以存取多個應用程式。 無論平臺或功能變數名稱為何，應用程式都可以是 web、行動或單頁應用程式。

當使用者一開始登入應用程式時，Azure AD B2C 保存以 cookie 為基礎的會話。 在後續的驗證要求中，Azure AD B2C 會讀取並驗證以 cookie 為基礎的會話，並在不提示使用者再次登入的情況下發出存取權杖。 如果以 cookie 為基礎的會話過期或變成無效，系統會提示使用者重新登入。  

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Azure AD B2C 會話總覽

與 Azure AD B2C 的整合牽涉到三種類型的 SSO 會話：

- **Azure AD B2C** -由 Azure AD B2C 管理的會話
- 同盟 **識別提供者**-由身分識別提供者管理的會話，例如 Facebook、Salesforce 或 Microsoft 帳戶
- Web、行動或單一頁面應用程式所管理的 **應用程式** 會話

![SSO 工作階段](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C 會話 

當使用者使用本機或社交帳戶成功驗證時，Azure AD B2C 會在使用者的瀏覽器上儲存以 cookie 為基礎的會話。 Cookie 會儲存在 Azure AD B2C 租使用者功能變數名稱下，例如 `https://contoso.b2clogin.com` 。

如果使用者一開始以同盟帳戶登入，然後在會話時間範圍內 (存留時間或 TTL) 登入相同的應用程式或不同的應用程式，Azure AD B2C 會嘗試從同盟身分識別提供者取得新的存取權杖。 如果同盟識別提供者會話已過期或無效，同盟身分識別提供者會提示使用者提供其認證。 如果會話仍在使用中 (或使用者已使用本機帳戶（而非同盟帳戶) ）登入，Azure AD B2C 授權使用者並消除進一步的提示。

您可以設定會話行為，包括會話 TTL，以及 Azure AD B2C 如何在原則和應用程式之間共用會話。

### <a name="federated-identity-provider-session"></a>同盟識別提供者會話

社交或企業身分識別提供者會管理自己的會話。 Cookie 會儲存在身分識別提供者的功能變數名稱下，例如 `https://login.salesforce.com` 。 Azure AD B2C 不會控制同盟身分識別提供者會話。 相反地，會話行為是由同盟身分識別提供者所決定。 

考慮下列案例：

1. 使用者登入 Facebook 以檢查其摘要。
2. 之後，使用者會開啟您的應用程式，並開始登入程式。 應用程式會將使用者重新導向至 Azure AD B2C，以完成登入程式。
3. 在 [Azure AD B2C 註冊或登入] 頁面上，使用者會選擇使用其 Facebook 帳戶登入。 使用者會被重新導向至 Facebook。 如果 Facebook 上有使用中的會話，系統就不會提示使用者提供其認證，並會立即以 Facebook 權杖將其重新導向至 Azure AD B2C。

### <a name="application-session"></a>應用程式會話

您可以透過 OAuth 存取、識別碼權杖或 SAML 權杖來保護 web、行動或單一頁面應用程式。 當使用者嘗試存取應用程式上的受保護資源時，應用程式會檢查應用程式端是否有作用中的會話。 如果沒有任何應用程式會話或會話已過期，應用程式將會讓使用者 Azure AD B2C 登入頁面。

應用程式會話可以是儲存在應用程式功能變數名稱下的以 cookie 為基礎的會話，例如 `https://contoso.com` 。 行動應用程式可能會以不同的方式儲存會話，但使用類似的方法。

## <a name="configure-azure-ad-b2c-session-behavior"></a>設定 Azure AD B2C 會話行為

您可以設定 Azure AD B2C 的會話行為，包括：

- **Web 應用程式會話存留期 (分鐘)** -驗證成功後，Azure AD B2C 會話 cookie 儲存在使用者瀏覽器上的時間量。 您可以將會話存留時間設定為15到720分鐘的值。

- **Web 應用程式會話超時** -指出會話的延伸方式是由會話存留時間設定或讓我保持登入的設定。
  - 輪流 **-指出** 每次使用者執行以 cookie 為基礎的驗證 (預設) 時，會將會話延伸。
  - [**絕對**]-表示在指定的時間週期之後，會強制使用者重新驗證。

- **單一登入** 設定-Azure AD B2C 會話可以設定下列範圍：
  - **Tenant** - 這項設定是預設值。 此設定可允許 B2C 租用戶中的多個應用程式和使用者流程共用同一個使用者工作階段。 例如，一旦使用者登入應用程式，使用者也可以在存取應用程式時順暢地登入另一個應用程式。
  - **應用程式** - 這項設定可讓您保留應用程式專用的使用者工作階段，不受其他應用程式所限制。 例如，如果您想要讓使用者登入 Contoso 藥房，不論使用者是否已登入 Contoso Groceries，您都可以使用此設定。
  - **原則** - 這項設定可讓您保留使用者流程專用的使用者工作階段，不論使用該使用者工作階段的應用程式為何。 例如，如果使用者已登入並完成多重要素驗證 (MFA) 步驟，則只要系結至使用者流程的會話未過期，使用者就可以存取多個應用程式的更高安全性部分。
  - **停用** -此設定會強制使用者在每次原則執行時，執行整個使用者流程。
::: zone pivot="b2c-custom-policy"
- **讓我保持登入** -透過使用持續性 cookie 來延長會話存留時間。 在使用者關閉並重新開啟瀏覽器之後，會話會保持作用中狀態。 只有當使用者登出時，才會撤銷此會話。[讓我保持登入] 功能僅適用于以本機帳戶登入。 「讓我保持登入」功能的優先順序高於會話的存留時間。 如果已啟用「讓我保持登入」功能，且使用者選取該功能，則此功能會規定會話將到期的時間。 
::: zone-end

::: zone pivot="b2c-user-flow"

若要設定會話行為：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程]。
1. 開啟您先前建立的使用者流程。
1. 選取 [屬性] 。
1. 設定 **web 應用程式會話存留期 (分鐘)**、 **Web 應用程式會話超時**、 **單一登入** 設定，以及視需要 **在登出要求中要求識別碼權杖** 。
1. 按一下 [檔案]  。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要變更工作階段行為和 SSO 組態，您可以在 [RelyingParty](relyingparty.md) 元素內新增 **UserJourneyBehaviors** 元素。  **UserJourneyBehaviors** 元素必須緊跟著 **DefaultUserJourney**。 **UserJourneyBehavors** 元素看起來應該如下列範例所示：

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="enable-keep-me-signed-in-kmsi"></a>讓我保持登入 (KMSI) 

您可以為您的 web 和原生應用程式使用者啟用「讓我保持登入」功能，您的 Azure Active Directory B2C (Azure AD B2C) 目錄中具有本機帳戶。 這項功能會將存取權授與傳回給應用程式的使用者，而不會提示他們重新輸入其使用者名稱和密碼。 當使用者登出時，即會撤銷此存取權。

![顯示「讓我保持登入」核取方塊的範例註冊登入頁面](./media/session-behavior/keep-me-signed-in.png)

使用者應該不要在公用電腦上啟用此選項。

### <a name="configure-the-page-identifier"></a>設定頁面識別碼

若要啟用 KMSI，請將內容定義 `DataUri` 元素設定為 [頁面識別碼](contentdefinitions.md#datauri) `unifiedssp` 和 [頁面版本](page-layout.md) *1.1.0* 或更新版本。

1. 開啟原則的擴充檔案。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。 此擴充檔是自訂原則入門套件中包含的其中一個原則檔案，您應該在必要條件中取得這些檔案，以 [開始使用自訂](custom-policy-get-started.md)原則。
1. 搜尋 **BuildingBlocks** 元素。 如果此元素不存在，請加以新增。
1. 將 **ContentDefinitions** 元素加入至原則的 **BuildingBlocks** 元素。

    您的自訂原則看起來應該如下列程式碼片段所示：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>將中繼資料新增至自我判斷技術設定檔

若要將 KMSI 核取方塊新增至 [註冊] 和 [登入] 頁面，請將 `setting.enableRememberMe` 中繼資料設定為 [true]。 覆寫延伸模組檔案中的 SelfAsserted-LocalAccountSignin-電子郵件技術設定檔。

1. 尋找 ClaimsProviders 元素。 如果此元素不存在，請加以新增。
1. 將下列宣告提供者新增至 Claimsprovider 元素：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 儲存擴充檔案。

### <a name="configure-a-relying-party-file"></a>設定信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 開啟您的自訂原則檔案。 例如 *SignUpOrSignin.xml*。
1. 如果尚未存在，請將 `<UserJourneyBehaviors>` 子節點加入至 `<RelyingParty>` 節點。 它必須立即位於之後 `<DefaultUserJourney ReferenceId="User journey Id" />` ，例如： `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` 。
1. 新增下列節點作為 `<UserJourneyBehaviors>` 元素的子節點。

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

建議您將 >sessionexpiryinseconds 的值設定為 (1200 秒) 的短時間，而 KeepAliveInDays 的值可以設定為 (30) 天的相對較長期間，如下列範例所示：

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>登出

當您想要將使用者登出應用程式時，並沒有足夠的方式可清除應用程式的 cookie 或結束使用者的會話。 您必須將使用者重新導向至 Azure AD B2C 才能登出。否則，使用者可能可以重新驗證您的應用程式，而不需要再次輸入認證。

登出要求時 Azure AD B2C：

1. 使 Azure AD B2C cookie 型會話失效。
::: zone pivot="b2c-user-flow"
2. 嘗試從同盟身分識別提供者登出
::: zone-end
::: zone pivot="b2c-custom-policy"
3. 嘗試從同盟身分識別提供者登出：
   - OpenId Connect-如果識別提供者的知名設定端點指定一個 `end_session_endpoint` 位置。
   - OAuth2-如果 [識別提供者中繼資料](oauth2-technical-profile.md#metadata) 包含 `end_session_endpoint` 位置。
   - SAML-如果 [識別提供者中繼資料](saml-identity-provider-technical-profile.md#metadata) 包含 `SingleLogoutService` 位置。
4. （選擇性）登出其他應用程式。 如需詳細資訊，請參閱 [單一登出](#single-sign-out) 一節。

> [!NOTE]
> 您可以將身分識別提供者技術設定檔中繼資料設定為，以停用從同盟身分識別提供者登出 `SingleLogoutEnabled` `false` 。
::: zone-end

登出會使用 Azure AD B2C 來清除使用者的單一登入狀態，但可能不會將使用者登出其社交身分識別提供者會話。 如果使用者在後續登入時選取相同的身分識別提供者，他們可能會在未輸入認證的情況下重新驗證。 如果使用者想要登出應用程式，則不一定表示他們想要登出其 Facebook 帳戶。 但是，如果使用本機帳戶，使用者的會話就會正確結束。

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>單一登出 

當您將使用者重新導向至 OAuth2 和 SAML 通訊協定) 的 Azure AD B2C 登出端點 (時，Azure AD B2C 會從瀏覽器清除使用者的會話。 不過，使用者仍可能登入使用 Azure AD B2C 進行驗證的其他應用程式。 若要讓這些應用程式同時將使用者登出，Azure AD B2C 將 HTTP GET 要求傳送至 `LogoutUrl` 使用者目前登入之所有應用程式的註冊。

應用程式必須藉由清除任何可識別使用者的工作階段並傳回 `200` 回應，以回應此要求。 如果您想要在應用程式中支援單一登出，您必須 `LogoutUrl` 在應用程式的程式碼中執行。 

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

::: zone-end

### <a name="secure-your-logout-redirect"></a>保護登出重新導向

登出之後，會將使用者重新導向至參數中所指定的 URI `post_logout_redirect_uri` ，而不論已針對應用程式指定的回復 url。 但是，如果傳遞的是有效的， `id_token_hint` 而且 **登出要求中的要求識別碼權杖** 已開啟，Azure AD B2C 會確認的值 `post_logout_redirect_uri` 符合其中一個應用程式設定的重新導向 uri，然後再執行重新導向。 如果未針對應用程式設定相符的回復 URL，則會顯示錯誤訊息，且不會重新導向使用者。 

::: zone pivot="b2c-user-flow"

若要在登出要求中要求識別碼權杖：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程]。
1. 開啟您先前建立的使用者流程。
1. 選取 [屬性] 。
1. 啟用 **登出要求中的要求識別碼權杖**。
1. 返回  **Azure AD B2C**。
1. 選取 **應用程式註冊**，然後選取您的應用程式。
1. 選取 [驗證]。
1. 在 [ **登出 URL** ] 文字方塊中，輸入您的 post 登出重新導向 URI，然後選取 [ **儲存**]。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要在登出要求中要求識別碼權杖，請在 [RelyingParty](relyingparty.md)元素內加入 **UserJourneyBehaviors** 專案。 然後將 **SingleSignOn** 元素的 **EnforceIdTokenHintOnLogout** 設定為 `true` 。 **UserJourneyBehaviors** 元素看起來應該如下列範例所示：

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

若要設定應用程式登出 URL：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 **應用程式註冊**，然後選取您的應用程式。
1. 選取 [驗證]。
1. 在 [ **登出 URL** ] 文字方塊中，輸入您的 post 登出重新導向 URI，然後選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在 Azure AD B2C 中設定權杖](configure-tokens.md)。
