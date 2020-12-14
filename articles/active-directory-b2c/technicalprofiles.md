---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: 指定 Azure Active Directory B2C 中自訂原則的 TechnicalProfiles 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99ed7d3ad81202ab6fe67bf52888bbdbf0b28d2a
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387083"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

技術設定檔提供一個架構，內含內建機制，可使用 Azure Active Directory B2C (Azure AD B2C) 中的自訂原則與不同類型的合作物件進行通訊。 技術設定檔可用來與您的 Azure AD B2C 租用戶進行通訊，以建立使用者或讀取使用者設定檔。 技術設定檔可以自我判斷來啟用與使用者的互動。 例如，收集使用者的認證以進行登入，然後呈現註冊頁面或密碼重設頁面。

## <a name="type-of-technical-profiles"></a>技術設定檔的類型

技術設定檔支援下列類型的案例：

- [Application Insights](application-insights-technical-profile.md) 將事件資料傳送至 [Application Insights](../azure-monitor/app/app-insights-overview.md)。
- [Azure Active Directory](active-directory-technical-profile.md) - 針對 Azure Active Directory B2C 使用者管理提供支援。
- [Azure AD Multi-Factor Authentication](multi-factor-auth-technical-profile.md) -使用 Azure AD MULTI-FACTOR AUTHENTICATION (MFA) 提供驗證電話號碼的支援。 
- [宣告轉換](claims-transformation-technical-profile.md) - 呼叫輸出宣告轉換以操作宣告值、驗證宣告，或為一組輸出宣告設定預設值。
- [識別碼權杖提示](id-token-hint.md) -驗證 `id_token_hint` JWT 權杖簽章、簽發者名稱和權杖物件，並從輸入權杖中解壓縮宣告。
- [JWT 權杖簽發者](jwt-issuer-technical-profile.md) - 發出會傳回給信賴憑證者應用程式的 JWT 權杖。
- [OAuth1](oauth1-technical-profile.md) - 與任何 OAuth 1.0 通訊協定識別提供者建立同盟。
- [OAuth2](oauth2-technical-profile.md) - 與任何 OAuth 2.0 通訊協定識別提供者建立同盟。
- 單次[密碼](one-time-password-technical-profile.md)：提供管理單次密碼產生和驗證的支援。
- [OpenID Connect](openid-connect-technical-profile.md) -與任何 OpenID Connect 通訊協定身分識別提供者同盟。
- [電話要素](phone-factor-technical-profile.md) -支援註冊和驗證電話號碼。
- [RESTful 提供者](restful-technical-profile.md) -呼叫 REST API 服務，例如驗證使用者輸入、擴充使用者資料，或與企業營運應用程式整合。
- [Saml 身分識別提供者](saml-identity-provider-technical-profile.md) -與任何 SAML 通訊協定識別提供者同盟。
- [Saml 權杖簽發者](saml-issuer-technical-profile.md) -發出傳回給信賴憑證者應用程式的 saml 權杖。
- [自我判斷](self-asserted-technical-profile.md) - 與使用者進行互動。 例如，收集使用者的認證以進行登入、呈現註冊頁面或密碼重設。
- [工作階段管理](custom-policy-reference-sso.md) - 處理各種不同類型的工作階段。

## <a name="technical-profile-flow"></a>技術設定檔流程

所有類型的技術設定檔都共用相同的概念。 您傳送輸入宣告、執行宣告轉換，然後與已設定的對象 (例如識別提供者、REST API 或 Azure AD 目錄服務) 進行通訊。 程式完成之後，技術設定檔會傳回輸出宣告，而且可能會執行輸出宣告轉換。 下圖說明技術設定檔中所參考轉換和對應的處理方式。 不論技術設定檔的互動對象是誰，在執行任何宣告轉換之後，技術設定檔的輸出宣告都會立即儲存在宣告包中。

![說明技術設定檔流程的圖表](./media/technical-profiles/technical-profile-flow.png)

1. **單一登入 (sso) 會話管理** -使用 [sso 會話管理](custom-policy-reference-sso.md)來還原技術設定檔的會話狀態。
1. **輸入宣告轉換** -在啟動技術設定檔之前，Azure AD B2C 執行輸入 [宣告轉換]。 (claimstransformations.md) 。
1. **輸入宣告** -宣告會從用於技術設定檔的宣告包中挑選。
1. **技術設定檔執行** - 技術設定檔會與已設定的對象交換宣告。 例如：
    - 將使用者重新導向到識別提供者來完成登入。 成功登入之後，使用者會返回，而技術設定檔則會繼續執行。
    - 呼叫 REST API，並傳送參數作為 InputClaims 及取回資訊作為 OutputClaims。
    - 建立或更新使用者帳戶。
    - 傳送並驗證 MFA 文字訊息。
1. **驗證技術設定檔** ： [自我判斷的技術設定檔](self-asserted-technical-profile.md) 可呼叫 [驗證技術](validation-technical-profile.md) 設定檔，以驗證使用者所分析的資料。
1. **輸出宣告** -宣告會傳回給宣告包。 您可以在下一個協調流程步驟或輸出宣告轉換中使用這些宣告。
1. **輸出宣告轉換** -完成技術設定檔之後，Azure AD B2C 執行輸出 [宣告轉換](claimstransformations.md)。 
1. **單一登入 (sso) 會話管理** -使用 [sso 會話管理](custom-policy-reference-sso.md)將技術設定檔的資料保存到會話中。

**TechnicalProfiles** 元素包含受到宣告提供者支援的一組技術設定檔。 每個宣告提供者必須有一或多個技術設定檔，以決定與該宣告提供者進行通訊所需的端點以及通訊協定。 宣告提供者可以有多個技術設定檔。

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
|---------|---------|---------|
| 識別碼 | 是 | 技術設定檔的唯一識別碼。 可使用此識別碼，從原則設定檔的其他元素參考此技術設定檔。 例如，**OrchestrationSteps** 和 **ValidationTechnicalProfile**。 |

**TechnicalProfile** 包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 網域 | 0:1 | 技術設定檔的網域名稱。 例如，如果您的技術設定檔指定 Facebook 識別提供者，則網域名稱為 Facebook.com。 |
| DisplayName | 1:1 | 技術設定檔的顯示名稱。 |
| 描述 | 0:1 | 技術設定檔的描述。 |
| 通訊協定 | 1:1 | 用於與其他合作對象進行通訊的通訊協定。 |
| 中繼資料 | 0:1 | 通訊協定使用的機碼值組，用於在交易過程中與端點通訊。 |
| InputTokenFormat | 0:1 | 輸入權杖的格式。 可能的值：`JSON`、`JWT`、`SAML11` 或 `SAML2`。 `JWT` 值代表以 IETF 規格為基礎的 JSON Web 權杖。 `SAML11` 值代表以 OASIS 規格為基礎的 SAML 1.1 安全性權杖。  `SAML2` 值代表以 OASIS 規格為基礎的 SAML 2.0 安全性權杖。 |
| OutputTokenFormat | 0:1 | 輸出權杖的格式。 可能的值：`JSON`、`JWT`、`SAML11` 或 `SAML2`。 |
| CryptographicKeys | 0:1 | 技術設定檔中所使用的密碼編譯金鑰清單。 |
| InputClaimsTransformations | 0:1 | 先前定義的宣告轉換參考清單，應在將任何宣告傳送至宣告提供者或信賴憑證者之前執行這些轉換。 |
| InputClaims | 0:1 | 先前定義的宣告類型參考清單，這類宣告類型會在技術設定檔中做為輸入來源。 |
| PersistedClaims | 0:1 | 先前定義的宣告類型參考清單，與技術設定檔相關的宣告提供者會保留這類宣告類型。 |
| DisplayClaims | 0:1 | 先前定義的宣告類型參考清單，與 [自我判斷技術設定檔](self-asserted-technical-profile.md)相關的宣告提供者提供。 DisplayClaims 功能目前為 **預覽** 狀態。 |
| OutputClaims | 0:1 | 先前定義的宣告類型參考清單，會在技術設定檔中輸出這類宣告類型。 |
| OutputClaimsTransformations | 0:1 | 先前定義的宣告轉換參考清單，應在來自宣告提供者的宣告受到接收後再執行。 |
| ValidationTechnicalProfiles | 0:n | 其他技術設定檔的參考清單，技術設定檔會用來進行用途的驗證。 如需詳細資訊，請參閱[驗證技術設定檔](validation-technical-profile.md)。|
| SubjectNamingInfo | 0:1 | 控制權杖中主體名稱的產生，其中主體名稱與宣告分開指定。 例如，OAuth 或 SAML。  |
| IncludeInSso | 0:1 |  使用此技術設定檔是否應該將單一登入套用至會話 (SSO) 行為，或是改為需要明確的互動。 此元素只適用于驗證技術設定檔中使用的 SelfAsserted 設定檔。 可能的值：`true` (預設) 或 `false`。 |
| IncludeClaimsFromTechnicalProfile | 0:1 | 技術設定檔的識別碼，您打算將所有輸入和輸出宣告加入此技術設定檔。 必須在相同原則檔中定義參考的技術設定檔。 |
| IncludeTechnicalProfile |0:1 | 技術設定檔的識別碼，您打算將所有資料加入此技術設定檔。 |
| UseTechnicalProfileForSessionManagement | 0:1 | 用於工作階段管理的不同技術設定檔。 |
|EnabledForUserJourneys| 0:1 |控制是否可在使用者旅程圖中執行技術設定檔。  |

## <a name="protocol"></a>通訊協定

通訊 **協定** 會指定要用於與另一方通訊的通訊協定。 **Protocol** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 名稱 | 是 | Azure AD B2C 所支援的有效通訊協定名稱，可用來作為技術設定檔的一部分。 可能的值： `OAuth1` 、 `OAuth2` 、 `SAML2` 、 `OpenIdConnect` 、 `Proprietary` 或 `None` 。 |
| 處理常式 | 否 | 當通訊協定名稱設為 `Proprietary` 時，請指定 Azure AD B2C 用於判斷通訊協定處理常式之組件的完整名稱。 |

## <a name="metadata"></a>中繼資料

**中繼資料** 元素包含特定通訊協定的相關設定選項。 支援的中繼資料清單記載于對應的 [技術設定檔](#type-of-technical-profiles) 規格中。 **中繼資料** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| Item | 0:n | 與技術設定檔相關的中繼資料。 每一種技術設定檔有一組不同的中繼資料項目。 如需詳細資訊，請參閱技術設定檔類型章節。 |

### <a name="item"></a>Item

Metadata **專案的 Item** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Key | 是 | 中繼資料金鑰。 請參閱每個 [技術配置檔案類型](#type-of-technical-profiles)，以取得中繼資料專案清單。 |

下列範例說明如何使用與 [OAuth2 技術設定檔](oauth2-technical-profile.md#metadata)相關的中繼資料。

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

下列範例說明如何使用與 [REST API 技術設定檔](restful-technical-profile.md#metadata)相關的中繼資料。

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>密碼編譯金鑰

Azure AD B2C 會以 [原則金鑰](policy-keys-overview.md) 的形式儲存秘密和憑證，以與它所整合的服務建立信任關係。 在執行技術設定檔的期間，Azure AD B2C 會從 Azure AD B2C 原則金鑰取出密碼編譯金鑰，然後使用金鑰建立信任、加密或簽署權杖。 這些信任包含：

- 具有 [OAuth1](oauth1-technical-profile.md#cryptographic-keys)、 [OAuth2](oauth2-technical-profile.md#cryptographic-keys)和 [SAML](saml-identity-provider-technical-profile.md#cryptographic-keys) 身分識別提供者的同盟
- 保護與[REST API 服務](secure-rest-api.md)的連接
- 簽署和加密 [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) 和 [SAML](saml-issuer-technical-profile.md#cryptographic-keys) 權杖

**CryptographicKeys** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| Key | 1:n | 此技術設定檔中使用的密碼編譯金鑰。 |

### <a name="key"></a>答案

**Key** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 識別碼 | 否 | 原則檔中的其他元素所參考之特定金鑰組的唯一識別碼。 |
| StorageReferenceId | 是 | 原則檔中的其他元素所參考之儲存體金鑰容器的識別碼。 |

## <a name="input-claims-transformations"></a>輸入宣告轉換

**InputClaimsTransformations** 元素可以包含輸入宣告轉換專案的集合，這些專案是用來修改輸入宣告或產生新的宣告。 

宣告轉換集合中先前宣告轉換的輸出宣告可以是後續輸入宣告轉換的輸入宣告，讓您可以根據彼此來擁有一連串的宣告轉換。

**InputClaimsTransformations** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 宣告轉換的識別碼，應在將任何宣告傳送至宣告提供者或信賴憑證者之前執行此轉換。 宣告轉換可用於修改現有的 ClaimsSchema 宣告或產生新的宣告。 |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則檔或父原則檔中定義之宣告轉換的識別碼。 |

下列技術設定檔參考 **CreateOtherMailsFromEmail** 宣告轉換。 宣告轉換會將宣告的值新增 `email` 至 `otherMails` 集合，然後將資料保存到目錄。

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>輸入宣告

**InputClaims** 會從宣告包中挑選宣告，並用於技術設定檔。 例如，[自我判斷技術設定檔](self-asserted-technical-profile.md)會使用輸入宣告來預先填入使用者所提供的輸出宣告。 REST API 技術設定檔會使用輸入宣告將輸入參數傳送給 REST API 端點。 Azure Active Directory 會使用輸入宣告作為唯一識別碼來讀取、更新或刪除帳戶。

**InputClaims** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 預期的輸入宣告類型。 |

### <a name="inputclaim"></a>InputClaim

**InputClaim** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在原則檔或父代原則檔之 ClaimsSchema 區段中定義的宣告類型識別碼。 |
| DefaultValue | 否 | 當 ClaimTypeReferenceId 指示的宣告不存在時，此預設值可用於建立宣告，以便產生的宣告可由技術設定檔用來作為 InputClaim。 |
| PartnerClaimType | 否 | 指定的原則宣告類型對應之外部夥伴的宣告類型識別碼。 如果未指定 PartnerClaimType 屬性，則指定的原則宣告類型會對應至相同名稱的夥伴宣告類型。 當您的宣告類型名稱不同於其他合作對象時，請使用這個屬性。 例如，第一個宣告名稱是 'givenName'，而夥伴會使用名稱為 'first_name' 的宣告。 |

## <a name="display-claims"></a>顯示宣告

**DisplayClaims** 元素包含自行判斷提示 [技術設定檔](self-asserted-technical-profile.md)所定義的宣告清單，可在畫面上呈現以從使用者收集資料。 在 [顯示宣告] 集合中，您可以包含宣告 [類型](claimsschema.md)的參考，或您已建立的 [控制項](display-controls.md) 。 

- 宣告類型是要顯示在螢幕上的宣告參考。 
  - 若要強制使用者提供特定宣告的值，請將 **DisplayClaim** 元素的 **必要** 屬性設定為 `true` 。
  - 若要預先填入顯示宣告的值，請使用先前所述的輸入宣告。 元素可能也包含預設值。
  - **DisplayClaims** 集合中的 **ClaimType** 元素必須將 **>userinputtype** 元素設定為 Azure AD B2C 所支援的任何使用者輸入類型。 例如，`TextBox` 或 `DropdownSingleSelect`。
- 顯示控制項是具有特殊功能並與 Azure AD B2C 後端服務互動的使用者介面元素。 它可讓使用者在頁面上執行動作，該頁面會在後端叫用驗證技術設定檔。 例如，確認電子郵件地址、電話號碼或客戶忠誠度號碼。

**DisplayClaims** 中的專案順序會指定 Azure AD B2C 在螢幕上呈現宣告的順序。 

**DisplayClaims** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | 預期的輸入宣告類型。 |

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 否 | 已在原則檔或父代原則檔之 ClaimsSchema 區段中定義的宣告類型識別碼。 |
| DisplayControlReferenceId | 否 | 已在原則檔或父原則檔之 ClaimsSchema 區段中定義的 [顯示控制項](display-controls.md) 識別碼。 |
| 必要 | 否 | 指出是否需要顯示宣告。 |

下列範例說明如何在自我判斷技術設定檔中使用顯示宣告和顯示控制項。

![具有顯示宣告的自我判斷技術設定檔](./media/technical-profiles/display-claims.png)

在下列技術設定檔中：

- 第一個顯示宣告會參考 `emailVerificationControl` 顯示控制項，以收集並驗證電子郵件地址。
- 第五個顯示宣告會參考 `phoneVerificationControl` 顯示控制項，以收集並驗證電話號碼。
- 系統會 Claimtype 其他顯示宣告，以便從使用者收集。

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

### <a name="persisted-claims"></a>保存的宣告

**PersistedClaims** 元素包含的所有值都應該由 [Azure AD 技術設定檔](active-directory-technical-profile.md)，以及在原則的 [ClaimsSchema](claimsschema.md)區段中已定義的對應資訊，以及 Azure AD 屬性名稱之間的可能對應資訊來保存。

宣告的名稱是 [Azure AD 屬性](user-profile-attributes.md) 的名稱，除非指定了 **PartnerClaimType** 屬性，其中包含 Azure AD 的屬性名稱。

**PersistedClaims** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 要保存的宣告類型。 |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在原則檔或父代原則檔之 ClaimsSchema 區段中定義的宣告類型識別碼。 |
| DefaultValue | 否 | 當 ClaimTypeReferenceId 指示的宣告不存在時，此預設值可用於建立宣告，以便產生的宣告可由技術設定檔用來作為 InputClaim。 |
| PartnerClaimType | 否 | 指定的原則宣告類型對應之外部夥伴的宣告類型識別碼。 如果未指定 PartnerClaimType 屬性，則指定的原則宣告類型會對應至相同名稱的夥伴宣告類型。 當您的宣告類型名稱不同於其他合作對象時，請使用這個屬性。 例如，第一個宣告名稱是 'givenName'，而夥伴會使用名稱為 'first_name' 的宣告。 |

在下列範例中，會建立新的本機帳戶的 **AAD >aad-userwriteusinglogonemail** 技術設定檔或 [入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)會保存下列宣告：

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>輸出宣告

**OutputClaims** 是技術設定檔完成後傳回宣告包的宣告集合。 您可以在下一個協調流程步驟或輸出宣告轉換中使用這些宣告。 **OutputClaims** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 預期的輸出宣告類型。 |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在原則檔或父代原則檔之 ClaimsSchema 區段中定義的宣告類型識別碼。 |
| DefaultValue | 否 | 當 ClaimTypeReferenceId 指示的宣告不存在時，此預設值可用於建立宣告，以便產生的宣告可由技術設定檔用來作為 InputClaim。 |
|AlwaysUseDefaultValue |否 |強制使用預設值。  |
| PartnerClaimType | 否 | 指定的原則宣告類型對應之外部夥伴的宣告類型識別碼。 如果未指定 PartnerClaimType 屬性，則指定的原則宣告類型會對應至相同名稱的夥伴宣告類型。 當您的宣告類型名稱不同於其他合作對象時，請使用這個屬性。 例如，第一個宣告名稱是 'givenName'，而夥伴會使用名稱為 'first_name' 的宣告。 |

## <a name="output-claims-transformations"></a>輸出宣告轉換

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。 執行之後，輸出宣告會被放回宣告包中。 您可以在下一個協調流程步驟中使用這些宣告。

宣告轉換集合中先前宣告轉換的輸出宣告可以是後續輸入宣告轉換的輸入宣告，讓您可以根據彼此來擁有一連串的宣告轉換。

**OutputClaimsTransformations** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 宣告轉換的識別碼，應在將任何宣告傳送至宣告提供者或信賴憑證者之前執行這些轉換。 宣告轉換可用於修改現有的 ClaimsSchema 宣告或產生新的宣告。 |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則檔或父原則檔中定義之宣告轉換的識別碼。 |

下列技術設定檔會參考 AssertAccountEnabledIsTrue 宣告轉換，以評估帳戶在從目錄讀取宣告之後是否啟用 `accountEnabled` 。    

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>驗證技術設定檔

驗證技術設定檔是用來驗證 [自我判斷技術設定檔](self-asserted-technical-profile.md#validation-technical-profiles)中參考的部分或所有輸出宣告。 驗證技術設定檔是任何通訊協定的一般技術設定檔，例如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md)。 驗證技術設定檔會傳回輸出宣告，或傳回錯誤碼。 錯誤訊息會呈現給使用者的螢幕上，讓使用者可以重試。

下圖說明 Azure AD B2C 如何使用驗證技術設定檔來驗證使用者認證

![圖表驗證技術設定檔流程](./media/technical-profiles/validation-technical-profile.png) 

**ValidationTechnicalProfiles** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 使用的技術設定檔識別碼可驗證參考技術設定檔的部分或所有輸出宣告。 參考技術設定檔的所有輸入宣告必須出現在參考技術設定檔的輸出宣告中。 |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則檔或父原則檔中定義之技術設定檔的識別碼。 |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** 會定義信賴憑證者 [原則](relyingparty.md#subjectnaminginfo)中用於權杖的主體名稱。 **SubjectNamingInfo** 包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClaimType | 是 | 已在原則檔之 ClaimsSchema 區段中定義的宣告類型識別碼。 |

## <a name="include-technical-profile"></a>包含技術設定檔

技術設定檔可包含另一個技術設定檔，以變更設定或加入新功能。 **IncludeTechnicalProfile** 元素是衍生技術設定檔之一般技術設定檔的參考。 如果您有多個共用核心元素的技術設定檔，請使用 [包含] 來降低原則元素的重複和複雜度。 使用通用的技術設定檔搭配一組通用的設定，以及包含常見技術設定檔的特定工作技術設定檔。 例如，假設您有一個 [REST API 的技術設定檔](restful-technical-profile.md) ，其中包含單一端點，您需要在不同的案例中傳送不同的宣告集。 使用共用功能建立一般技術設定檔，例如 REST API 端點 URI、中繼資料、驗證類型和密碼編譯金鑰。 然後建立特定的工作技術設定檔，其中包含常見技術設定檔、新增輸入宣告、輸出宣告，或覆寫與該技術設定檔相關的 REST API 端點 URI。

**IncludeTechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則檔或父原則檔中定義之技術設定檔的識別碼。 |


下列範例說明如何使用包含：

- *REST-API-常見* -使用基本設定的常見技術設定檔。
- *Rest-ValidateProfile* -包含 *REST API Commom* 技術設定檔，並指定輸入和輸出宣告。
- *Rest-UpdateProfile* -包含 *REST API Commom* 技術設定檔、指定輸入宣告，以及覆寫 `ServiceUrl` 中繼資料。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Commom">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Commom" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Commom" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>包含多層級 

技術設定檔可包含單一技術設定檔。 包含的層級數目沒有任何限制。 例如，**AAD-UserReadUsingAlternativeSecurityId-NoError** 技術設定檔包含 **AAD-UserReadUsingAlternativeSecurityId**。 此技術設定檔 `RaiseErrorIfClaimsPrincipalDoesNotExist` 會將中繼資料專案設定為 `true` ，並在目錄中不存在社交帳戶時引發錯誤。 **AAD->aad-userreadusingalternativesecurityid->aad-userreadusingalternativesecurityid-noerror** 會覆寫此行為，並停用該錯誤訊息。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** 包含 `AAD-Common` 技術設定檔。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**Aad->aad-userreadusingalternativesecurityid->aad-userreadusingalternativesecurityid-noerror** 和 **aad >aad-userreadusingalternativesecurityid** 都不會指定所需的 **通訊協定** 元素，因為它是在 **AAD 通用** 技術設定檔中指定。

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>使用技術設定檔進行會話管理

[單一登入會話技術設定檔](custom-policy-reference-sso.md)的 **UseTechnicalProfileForSessionManagement** 專案參考。 **UseTechnicalProfileForSessionManagement** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在原則檔或父原則檔中定義之技術設定檔的識別碼。 |

## <a name="enabled-for-user-journeys"></a>為使用者旅程啟用

使用者旅程圖中的 [ClaimsProviderSelections](userjourneys.md#claimsproviderselection) 會定義宣告提供者選項清單及其順序。 使用您篩選的 **EnabledForUserJourneys** 元素，其宣告提供者可供使用者使用。 **EnabledForUserJourneys** 元素包含下列其中一個值：

- **Always**，執行技術設定檔。
- **Never**，跳過技術設定檔。
- **OnClaimsExistence**，僅於存在技術設定檔中指定的特定宣告時執行。
- **OnItemExistenceInStringCollectionClaim**，僅在項目存在於字串集合宣告中時執行。
- **OnItemAbsenceInStringCollectionClaim**，僅在項目存在於字串集合宣告中時執行。

使用 **OnClaimsExistence****OnItemExistenceInStringCollectionClaim** 或 **OnItemAbsenceInStringCollectionClaim**，要求您提供下列中繼資料：**ClaimTypeOnWhichToEnable** 可指定要評估的宣告類型，**ClaimValueOnWhichToEnable** 可指定要比較的值。

只有在 **identityProviders** 字串集合包含 `facebook.com` 的值時，才會執行下列技術設定檔：

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
