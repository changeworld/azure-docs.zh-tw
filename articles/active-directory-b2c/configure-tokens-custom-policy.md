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
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9f3cd5c3280308f6da15a52361857fa02567d595
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505456"
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

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure AD B2C 會話](session-overview.md)。
- 瞭解如何 [設定自訂原則中的會話行為](session-behavior-custom-policy.md)。
- 參考： [JwtIssuer](jwt-issuer-technical-profile.md)。
