---
title: 設定權杖 - Azure Active Directory B2C | Microsoft Docs
description: 瞭解如何在 Azure Active Directory B2C 中設定權杖存留期和相容性設定。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9b5782df01cad260852fb6ee5c00e3d7669acf47
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503519"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>設定 Azure Active Directory B2C 中的權杖

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在本文中，您將瞭解如何在 Azure Active Directory B2C (Azure AD B2C) 中設定[權杖的存留期和相容性](tokens-overview.md)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>權杖存留期行為

您可以設定權杖存留期，包括：

- **存取和識別碼權杖存留期 (分鐘)** -OAuth 2.0 持有人權杖和識別碼權杖的存留期。 預設值為60分鐘 (1 小時) 。 最小值為 5 分鐘 (包含此值)。  (內含) 的最大值為1440分鐘 (24 小時) 。
- 重新整理 **權杖存留期 (天)** -重新整理權杖可用來取得新存取權杖的最大時間週期（如果您的應用程式已被授與 `offline_access` 範圍）。 預設值為 14 天。 最小值為一天 (包含此值)。 最大 (內含) 90 天。
- 重新整理 **權杖滑動視窗存留期**-重新整理權杖滑動視窗類型。 `Bounded` 指出重新整理權杖可以擴充為 **存留期長度 (天)** 中的指定。 `No expiry` 指出重新整理權杖滑動視窗存留期永遠不會過期。
- **存留期長度 (天)** -在這段時間過後，不論應用程式所取得的最新重新整理權杖的有效期間是多少，都會強制使用者重新驗證。 值必須大於或等於重新整理 **權杖存留期** 值。

下圖顯示重新整理權杖滑動視窗存留期行為。

![重新整理權杖存留期](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> 使用授權碼流程與 PKCE 的單一頁面應用程式一律會有24小時的重新整理權杖存留期。 [深入瞭解在瀏覽器中重新整理權杖的安全性含意](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)。

## <a name="configure-token-lifetime"></a>設定權杖存留期

::: zone pivot="b2c-user-flow"

若要設定您的使用者流程權杖存留期：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂閱] 篩選，並選擇包含您 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程 (原則)]。
1. 開啟您先前建立的使用者流程。
1. 選取 [屬性] 。
1. 在 [ **權杖存留期**] 下，調整屬性以符合您應用程式的需求。
1. 按一下 **[儲存]** 。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要變更權杖相容性的設定，請在擴充功能或您想要影響之原則的信賴憑證者檔案中，設定 [權杖簽發者](jwt-issuer-technical-profile.md) 技術設定檔中繼資料。 權杖簽發者技術設定檔如下列範例所示：

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
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

以下是在上述範例中設定的值：

- **token_lifetime_secs** 存取權杖存留期 (秒) 。 預設值為 3600 (1 小時) 。 最小值為 300 (5 分鐘) 。 最大值為 86400 (24 小時) 。 
- **id_token_lifetime_secs** 識別碼權杖存留期 (秒) 。 預設值為 3600 (1 小時) 。 最小值為 300 (5 分鐘) 。 最大值為 86400 (24 小時) 。 
- **refresh_token_lifetime_secs** 重新整理權杖存留期 (秒) 。 預設值為120、9600 (14 天) 。 最小值為 86400 (24 小時) 。 最大值為 7776000 (90 天) 。 
- **rolling_refresh_token_lifetime_secs** -重新整理權杖滑動視窗存留期 (秒) 。 預設值為 7776000 (90 天) 。 最小值為 86400 (24 小時) 。 最大值為 31536000 (365 天) 。 如果您不想強制執行滑動視窗存留期，請將的值設定 `allow_infinite_rolling_refresh_token` 為 `true` 。 
- **allow_infinite_rolling_refresh_token** -重新整理權杖滑動視窗存留期永遠不會過期。 

::: zone-end


## <a name="token-compatibility-settings"></a>權杖相容性設定

您可以設定權杖相容性，包括：

- **簽發者 (iss)** 宣告-存取和識別碼權杖簽發者格式。
- **Subject (子)** 宣告-權杖判斷提示資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查，例如當權杖用於存取資源時。 根據預設，主體宣告會填入目錄中使用者的物件識別碼。
- **代表使用者流程** 的宣告-此宣告可識別已執行的使用者流程。 可能的值：`tfp` (預設) 或 `acr`。

::: zone pivot="b2c-user-flow"

若要設定您的使用者流程相容性設定：

1. 選取 [使用者流程 (原則)]。
1. 開啟您先前建立的使用者流程。
1. 選取 [屬性] 。
1. 在 [ **權杖相容性設定**] 下，調整屬性以符合您應用程式的需求。
1. 按一下 **[儲存]** 。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要變更權杖相容性的設定，請在擴充功能或您想要影響之原則的信賴憑證者檔案中，設定 [權杖簽發者](jwt-issuer-technical-profile.md) 技術設定檔中繼資料。 權杖簽發者技術設定檔如下列範例所示：

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

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

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>為您的應用程式提供選擇性宣告

應用程式宣告是傳回給應用程式的值。 更新您的使用者流程以包含所需的宣告。

::: zone pivot="b2c-user-flow"

1. 選取 [使用者流程 (原則)]。
1. 開啟您先前建立的使用者流程。
1. 選取 [應用程式宣告]。
1. 選擇您想要傳回給應用程式的宣告和屬性。
1. 按一下 **[儲存]** 。

::: zone-end

::: zone pivot="b2c-custom-policy"

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

::: zone-end

## <a name="next-steps"></a>後續步驟

深入瞭解如何[要求存取權杖](access-tokens.md)。