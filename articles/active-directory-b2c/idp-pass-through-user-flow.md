---
title: 將身分識別提供者存取權杖傳遞至您的應用程式
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 的使用者流程中，以宣告的形式傳遞 OAuth 2.0 身分識別提供者的存取權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e379b3fc77716eeea28b3dbeb9c3a022f0f16106
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516245"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中將身分識別提供者存取權杖傳遞至您的應用程式

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) 中的 [使用者流程](user-flow-overview.md) ，可讓您的應用程式使用者有機會註冊或使用身分識別提供者登入。 當發生這種情況時，Azure AD B2C 會從身分識別提供者處收到[存取權杖](tokens-overview.md)。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 您在使用者流程中啟用宣告，以將權杖傳遞至您在 Azure AD B2C 中註冊的應用程式。

::: zone pivot="b2c-user-flow"

Azure AD B2C 支援傳遞 [OAuth 2.0](add-identity-provider.md) 身分識別提供者（包括 [Facebook](identity-provider-facebook.md) 和 [Google](identity-provider-google.md)）的存取權杖。 對於所有其他識別提供者，宣告會傳回空白。

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C 支援傳遞 [OAuth 2.0](authorization-code-flow.md) 的存取金鑰和 [OpenID Connect](openid-connect.md) 識別提供者。 對於所有其他識別提供者，宣告會傳回空白。

::: zone-end

下圖顯示身分識別提供者權杖如何返回您的應用程式： 

![身分識別提供者通過 flow](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>啟用宣告

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [ **使用者流程] (原則)**，然後選取您的使用者流程。 例如，B2C_1_signupsignin1  。
5. 選取 [應用程式宣告]。
6. 啟用身分 **識別提供者存取權杖** 宣告。

    ![啟用身分識別提供者存取權杖宣告](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. 按一下 [儲存] 以儲存使用者流程。

## <a name="test-the-user-flow"></a>測試使用者流程

在 Azure AD B2C 中測試應用程式時，將 Azure AD B2C 權杖傳回到 `https://jwt.ms` 以檢閱其中的宣告很有用。

1. 在使用者流程的 [概觀] 頁面中，選取 [執行使用者流程]。
2. 針對 **應用程式**，請選取您先前註冊的應用程式。 若要查看下面範例中的權杖，**回覆 URL** 應該會顯示 `https://jwt.ms`。
3. 按一下 [執行使用者流程]，然後使用您的帳戶認證登入。 您應該會在 **idp_access_token** 宣告中看到識別提供者的存取權杖。

    您應該會看到類似下列範例的內容：

    ![已反白顯示 idp_access_token 區塊的 jwt.ms 中已解碼 token](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>新增宣告項目

1. 開啟 TrustframeworkExtensions.xml 檔案，並將下列識別碼為 `identityProviderAccessToken` 的 **ClaimType** 元素新增到 **ClaimsSchema** 元素：

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. 針對您想要存取權杖的每個 OAuth 2.0 識別提供者，將 **OutputClaim** 元素新增到 **TechnicalProfile** 元素。 下列範例顯示新增至 Facebook 技術設定檔的元素：

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. 儲存 TrustframeworkExtensions.xml 檔案。
4. 開啟信賴憑證者原則檔案，例如 *SignUpOrSignIn.xml*，並將 **OutputClaim** 元素新增到 **TechnicalProfile**：

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. 儲存原則檔案。

## <a name="test-your-policy"></a>測試您的原則

在 Azure AD B2C 中測試應用程式時，將 Azure AD B2C 權杖傳回到 `https://jwt.ms` 以檢閱其中的宣告很有用。

### <a name="upload-the-files"></a>上傳檔案

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [識別體驗架構]。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]。
6. 選取 [覆寫現有的原則]，然後搜尋並選取 TrustframeworkExtensions.xml 檔案。
7. 選取 [上傳] 。
8. 對信賴憑證者檔案重複步驟 5 到 7，例如 *SignUpOrSignIn.xml*。

### <a name="run-the-policy"></a>執行原則

1. 開啟您所變更的原則。 例如，*B2C_1A_signup_signin*。
2. 針對 **應用程式**，請選取您先前註冊的應用程式。 若要查看下面範例中的權杖，**回覆 URL** 應該會顯示 `https://jwt.ms`。
3. 選取 [立即執行]。

    您應該會看到類似下列範例的內容：

    ![已反白顯示 idp_access_token 區塊的 jwt.ms 中已解碼 token](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>後續步驟

若要深入瞭解 [Azure AD B2C 權杖的總覽](tokens-overview.md)，請深入瞭解。
