---
title: 使用自訂策略管理 SSO 和權杖自訂
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中使用自訂原則來管理 SSO 和權杖自訂。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9f57af92c50c0df6f628113bd9490ca83e1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189288"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來管理 SSO 和權杖自訂

本文提供有關如何使用 Azure 活動目錄 B2C （Azure AD B2C） 中的[自訂策略](custom-policy-overview.md)管理權杖、會話和單一登入 （SSO） 配置的資訊。

## <a name="token-lifetimes-and-claims-configuration"></a>權杖存留期和宣告組態

若要變更權杖存留期的設定，您可以在想要影響的原則信賴憑證者中，新增 [ClaimsProviders](claimsproviders.md) 元素。  **ClaimsProviders** 元素是 [TrustFrameworkPolicy](trustframeworkpolicy.md) 元素的子系。

在依賴方檔的基本策略元素和依賴方元素之間插入聲明提供程式元素。

您必須在其中放入會影響權杖存留期的資訊。 XML 看起來像這個範例：

```XML
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

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    在您的 **OutputClaims** 元素中，新增此元素：

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    針對 ACR，請移除 **AuthenticationContextReferenceClaimPattern** 項目。

- **主體 (sub) 宣告** - 此選項的預設值為 ObjectID，如果您想要將此設定切換為 `Not Supported`，請將下列這行：

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    改用這行︰

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>工作階段行為和 SSO

若要變更工作階段行為和 SSO 組態，您可以在 [RelyingParty](relyingparty.md) 元素內新增 **UserJourneyBehaviors** 元素。  **UserJourneyBehaviors** 元素必須緊跟著 **DefaultUserJourney**。 **UserJourneyBehavors** 元素的內部應如下列範例所示：

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

以下是在上述範例中設定的值：

- **單一登入 (SSO)** - 單一登入會透過 **SingleSignOn** 來設定。 適用的值為 `Tenant`、`Application`、`Policy`和 `Suppressed`。
- **Web 應用程式工作階段逾時** - Web 應用程式工作階段逾時會透過 **SessionExpiryType** 元素來設定。 適用的值為 `Absolute` 和 `Rolling`。
- **Web 應用會話存留期**- Web 應用會話存留期設置與**會話過期秒**元元素。 預設值為 86400 秒 (1440 分鐘)。
