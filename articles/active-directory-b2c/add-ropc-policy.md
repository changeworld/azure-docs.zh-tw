---
title: 設定資源擁有者密碼認證流程
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 中 (ROPC) 流程設定資源擁有者密碼認證。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6afe9814757d894c61ab3154bbff02791a830ddd
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895065"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定資源擁有者密碼認證流程

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在 Azure Active Directory B2C (Azure AD B2C) 中，資源擁有者密碼認證 (ROPC) 流程是一個 OAuth 標準驗證流程。 在此流程中，應用程式 (也稱為信賴憑證者) 會以有效的認證交換權杖。 認證包含使用者識別碼和密碼。 傳回的權杖為識別碼權杖、存取權杖及重新整理權杖。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>ROPC 流程注意事項

Azure Active Directory B2C (Azure AD B2C) 支援下列選項：

- **原生用戶端**：程式碼在使用者端的裝置上執行時，使用者會在驗證期間與之互動。 裝置可以是在 Android 和 iOS 等原生作業系統中執行的行動應用程式。
- **公用用戶端流程**：只會以 API 呼叫傳送應用程式所收集的使用者認證。 應用程式的認證不會傳送。
- **新增宣告**：可以變更識別碼權杖內容以新增宣告。

不支援下列流程：

- **伺服器對伺服器**：身分識別保護系統必須要有從呼叫端 (原生用戶端) 收集到的可靠 IP 位址，以用於互動的過程中。 伺服器端的 API 呼叫只會使用伺服器的 IP 位址。 如果超出失敗驗證的動態閾值，身分識別保護系統可能會將重複的 IP 位址識別為攻擊者。
- **機密用戶端流程**：會驗證應用程式用戶端識別碼，但不會驗證應用程式密碼。

使用 ROPC 流程時，請考慮下列事項：

- 當需要使用者互動的驗證流程中斷時，ROPC 無法正常運作。 例如，當密碼已過期或需要變更時，需要 [多重要素驗證](multi-factor-authentication.md) ，或在登入期間需要收集更多資訊時 (例如，使用者同意) 。
- ROPC 僅支援本機帳戶。 使用者無法使用同盟身分 [識別提供者](add-identity-provider.md) （例如 Microsoft、Google +、TWITTER、AD-FS 或 Facebook）登入。
- [會話管理](session-behavior.md)（包括 [讓我保持登入 (KMSI) ](session-behavior.md#enable-keep-me-signed-in-kmsi)）並不適用。


## <a name="register-an-application"></a>註冊應用程式

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>建立資源擁有者使用者流程

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 Azure 入口網站。
2. 若要切換為您的 Azure AD B2C 租用戶，請選取入口網站右上角的 B2C 租用戶。
3. 選取 [ **使用者流程**]，然後選取 [ **新增使用者流程**]。
4. 選取 [ **使用資源擁有者密碼認證登入] (ROPC)**。
5. 在 [ **版本**] 下，確認已選取 [ **預覽** ]，然後選取 [ **建立**]。
7. 提供使用者流程的名稱，例如 *ROPC_Auth*。
8. 在 [應用程式宣告] 底下，按一下 [顯示更多]。
9. 選取您的應用程式所需的應用程式宣告，例如 [顯示名稱]、[電子郵件地址] 和 [身分識別提供者]。
10. 選取 [確定]，然後選取 [建立]。

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>建立資源擁有者原則

1. 開啟 *TrustFrameworkExtensions.xml* 檔案。
2. 如果該檔案尚未存在，請新增 **ClaimsSchema** 元素及其子元素作為 **BuildingBlocks** 元素底下的第一個元素：

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. 在 **ClaimsSchema** 之後，將 **ClaimsTransformations** 元素及其子元素 **BuildingBlocks** 元素：

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. 找出 **DisplayName** 為 `Local Account SignIn` 的 **ClaimsProvider** 元素，然後新增下列技術設定檔：

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    以您在先決條件教學課程中所建立 ProxyIdentityExperienceFramework 應用程式的「應用程式識別碼」取代 **client_id** 的 **DefaultValue**。 然後，使用另外在先決條件教學課程中所建立 IdentityExperienceFramework 應用程式的「應用程式識別碼」取代 **resource_id** 的 **DefaultValue**。

5. 將下列 **ClaimsProvider** 元素及其技術設定檔一起新增至 **ClaimsProviders** 元素：

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. 將 **UserJourneys** 元素及其子元素新增至 **TrustFrameworkPolicy** 元素：

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
8. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
9. 按一下 [上傳] 。

## <a name="create-a-relying-party-file"></a>建立信賴憑證者檔案

接下來，更新會起始您所建立使用者旅程圖的信賴憑證者檔案：

1. 在您的工作目錄中建立一份 *SignUpOrSignin.xml* 檔案複本，然後將它重新命名為 *ROPC_Auth.xml*。
2. 開啟新檔案，然後將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性值變更成唯一值。 原則識別碼是您原則的名稱。 例如 **B2C_1A_ROPC_Auth**。
3. 將 **DefaultUserJourney** 中的 **ReferenceId** 屬性值變更成 `ResourceOwnerPasswordCredentials`。
4. 將 **OutputClaims** 元素變更成只包含下列宣告：

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
6. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *ROPC_Auth.xml* 檔案。
7. 按一下 [上傳] 。


::: zone-end

## <a name="test-the-ropc-flow"></a>測試 ROPC 流程

使用您最慣用的 API 開發應用程式產生 API 呼叫，並檢視回應以對您的原則偵錯。 請使用下列資訊作為 POST 要求的本文，以建構與此範例類似的呼叫：

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- 將 `<tenant-name>` 取代為您的 Azure AD B2C 租用戶名稱。
- 以您資源擁有者密碼認證原則的完整名稱取代 `B2C_1A_ROPC_Auth`。

| Key | 值 |
| --- | ----- |
| username | `user-account` |
| 密碼 | `password1` |
| grant_type | 密碼 |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- 以您租用戶中的使用者帳戶名稱取代 `user-account`。
- 以使用者帳戶的密碼取代 `password1`。
- 以來自 *ROPC_Auth_app* 註冊的「應用程式識別碼」取代 `application-id`。
- *Offline_access* 是您要接收重新整理權杖時可選用的項目。

實際的 POST 要求與以下範例類似：

```https
POST /<tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1A_ROPC_Auth HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

離線存取的成功回應會類似於下列範例：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>兌換重新整理權杖

建構一個如以下所示的 POST 呼叫。 使用下表中的資訊作為要求的本文：

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- 將 `<tenant-name>` 取代為您的 Azure AD B2C 租用戶名稱。
- 以您資源擁有者密碼認證原則的完整名稱取代 `B2C_1A_ROPC_Auth`。

| Key | 值 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- 以來自 *ROPC_Auth_app* 註冊的「應用程式識別碼」取代 `application-id`。
- 以先前回應中傳回的 **refresh_token** 取代 `refresh-token`。

成功的回應看起來如以下範例：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>使用原生 SDK 或應用程式驗證

Azure AD B2C 符合公用用戶端資源擁有者密碼認證的 OAuth 2.0 標準，應該與大多數用戶端 SDK 都相容。 如需最新資訊，請參閱[實作現代化最佳做法的 OAuth 2.0 和 OpenID Connect 所適用的原生應用程式 SDK](https://appauth.io/)。

## <a name="next-steps"></a>後續步驟

從 GitHub 下載已設定用於 Azure AD B2C 的實用範例 ([Android 版](https://aka.ms/aadb2cappauthropc)和 [iOS 版](https://aka.ms/aadb2ciosappauthropc))。
