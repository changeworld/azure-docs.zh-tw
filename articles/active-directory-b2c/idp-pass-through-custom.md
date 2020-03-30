---
title: 通過自訂策略將訪問權杖傳遞給應用
titleSuffix: Azure AD B2C
description: 瞭解如何通過自訂策略將 OAuth 2.0 標識提供程式的訪問權杖作為聲明傳遞給 Azure 活動目錄 B2C 中的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187778"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>透過自訂原則將存取權杖傳遞到 Azure Active Directory B2C 中的應用程式

Azure 活動目錄 B2C（Azure AD B2C）中的[自訂策略](custom-policy-get-started.md)為應用程式使用者提供了註冊或登錄標識提供程式的機會。 當發生這種情況時，Azure AD B2C 會從識別提供者處收到[存取權杖](tokens-overview.md)。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 您將宣告類型和輸出宣告新增到自訂原則，以將權杖傳遞至您在 Azure AD B2C 中註冊的應用程式。

Azure AD B2C 支援傳遞 [OAuth 2.0](authorization-code-flow.md) 的存取金鑰和 [OpenID Connect](openid-connect.md) 識別提供者。 對於所有其他識別提供者，宣告會傳回空白。

## <a name="prerequisites"></a>Prerequisites

* 您的自訂原則是使用 OAuth 2.0 或 OpenID Connect 識別提供者設定。

## <a name="add-the-claim-elements"></a>新增宣告項目

1. 開啟 TrustframeworkExtensions.xml** 檔案，並將下列識別碼為 `identityProviderAccessToken` 的 **ClaimType** 元素新增到 **ClaimsSchema** 元素：

    ```XML
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

    ```XML
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

3. 儲存 TrustframeworkExtensions.xml** 檔案。
4. 開啟信賴憑證者原則檔案，例如 *SignUpOrSignIn.xml*，並將 **OutputClaim** 元素新增到 **TechnicalProfile**：

    ```XML
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

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 通過按一下頂部功能表中的 **"目錄 + 訂閱**"篩選器並選擇包含租戶的目錄，請確保使用的目錄包含 Azure AD B2C 租戶。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 選取 [識別體驗架構]****。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]****。
6. 選取 [覆寫現有的原則]****，然後搜尋並選取 TrustframeworkExtensions.xml** 檔案。
7. 選取 [上傳]****。
8. 對信賴憑證者檔案重複步驟 5 到 7，例如 *SignUpOrSignIn.xml*。

### <a name="run-the-policy"></a>執行原則

1. 開啟您所變更的原則。 例如，*B2C_1A_signup_signin*。
2. 針對**應用程式**，請選取您先前註冊的應用程式。 若要查看下面範例中的權杖，**回覆 URL** 應該會顯示 `https://jwt.ms`。
3. 選取 [立即執行]****。

    您應該會看到類似下列範例的內容：

    ![jwt.ms解碼的權杖，並突出顯示idp_access_token塊](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>後續步驟

瞭解有關[Azure 活動目錄 B2C 權杖引用](tokens-overview.md)中的權杖的更多內容。
