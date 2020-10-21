---
title: 使用自訂原則來管理 SSO 和權杖自訂
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中使用自訂原則來管理 SSO 和權杖自訂。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e72bd04bb41537546191b8ceb320c0722bd10146
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340286"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來管理 SSO 和權杖自訂

本文提供有關如何使用 Azure Active Directory B2C (Azure AD B2C) 中的 [自訂原則](custom-policy-overview.md) ，來管理權杖、會話及單一登入 (SSO) 設定的資訊。

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>JWT 權杖存留期和宣告設定

若要變更權杖存留期的設定，您可以在想要影響的原則信賴憑證者中，新增 [ClaimsProviders](claimsproviders.md) 元素。  **ClaimsProviders** 元素是 [TrustFrameworkPolicy](trustframeworkpolicy.md) 元素的子系。

在 BasePolicy 元素和信賴憑證者檔案的 RelyingParty 專案之間插入 Claimsprovider 元素。

您必須在其中放入會影響權杖存留期的資訊。 XML 看起來像這個範例：

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

以下是在上述範例中設定的值：

- **存取權杖存留期** - 存取權杖存留期的值會透過 **token_lifetime_secs** 中繼資料項目來設定。 預設值為 3600 秒 (60 分鐘)。
- **識別碼權杖存留期** - 識別碼權杖存留期的值會透過 **id_token_lifetime_secs** 中繼資料項目來設定。 預設值為 3600 秒 (60 分鐘)。
- **重新整理權杖存留期** - 重新整理權杖存留期的值會透過 **refresh_token_lifetime_secs** 中繼資料項目來設定。 預設值為 1209600 秒 (14 天)。
- **重新整理權杖滑動視窗存留期** - 如果您想要對重新整理權杖設定滑動視窗存留期，請設定 **rolling_refresh_token_lifetime_secs** 中繼資料的值。 預設值為 7776000 秒 (90 天)。 如果您不想強制執行滑動視窗存留期，請以 `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` 取代此項目。
- **簽發者 (iss) 宣告** - 簽發者 (iss) 宣告會透過 **IssuanceClaimPattern** 中繼資料項目來設定。 適用的值為 `AuthorityAndTenantGuid` 和 `AuthorityWithTfp`。
- **設定代表原則識別碼的宣告** - 用來設定此值的選項為 `TFP` (信任架構原則) 和 `ACR` (驗證內容參考)。 `TFP` 是建議值。 使用 `None` 值來設定 **AuthenticationContextReferenceClaimPattern**。

    在 **ClaimsSchema** 項目中，新增此項目：

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    在您的 **OutputClaims** 元素中，新增此元素：

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    針對 ACR，請移除 **AuthenticationContextReferenceClaimPattern** 項目。

- **主體 (sub) 宣告** - 此選項的預設值為 ObjectID，如果您想要將此設定切換為 `Not Supported`，請將下列這行：

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    改用這行︰

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> 使用授權碼流程與 PKCE 的單一頁面應用程式一律會有24小時的重新整理權杖存留期。 [深入瞭解在瀏覽器中重新整理權杖的安全性含意](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)。

## <a name="provide-optional-claims-to-your-app"></a>為您的應用程式提供選擇性宣告

信賴憑證者 [原則技術設定檔](relyingparty.md#technicalprofile) 輸出宣告是傳回給應用程式的值。 新增輸出宣告會在成功的使用者旅程圖之後將宣告發出至權杖，並傳送至應用程式。 修改 [信賴憑證者] 區段內的技術設定檔元素，以新增所需的宣告作為輸出宣告。

1. 開啟您的自訂原則檔案。 例如 SignUpOrSignin.xml。
1. 尋找 OutputClaims 元素。 新增您想要包含在權杖中的 OutputClaim。 
1. 設定輸出宣告屬性。 

下列範例會新增宣告 `accountBalance` 。 AccountBalance 宣告會以餘額的形式傳送至應用程式。 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

OutputClaim 元素包含下列屬性：

  - **ClaimTypeReferenceId** -已在原則檔或父原則檔的 [ClaimsSchema](claimsschema.md) 區段中定義之宣告類型的識別碼。
  - **PartnerClaimType** -可讓您變更權杖中的宣告名稱。 
  - **DefaultValue** -預設值。 您也可以將預設值設定為宣告 [解析程式](claim-resolver-overview.md)，例如租使用者識別碼。
  - **AlwaysUseDefaultValue** -強制使用預設值。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure AD B2C 會話](session-overview.md)。
- 瞭解如何 [設定自訂原則中的會話行為](session-behavior-custom-policy.md)。
- 參考： [JwtIssuer](jwt-issuer-technical-profile.md)。
