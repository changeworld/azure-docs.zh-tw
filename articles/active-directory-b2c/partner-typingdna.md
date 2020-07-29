---
title: 具有 Azure Active Directory B2C 的 TypingDNA
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 TypingDNA，以協助根據使用者輸入模式來進行身分識別驗證和證明、提供識別碼驗證解決方案來強制執行多重要素驗證，並協助符合付款服務指示詞2（PSD2）的 SCA 需求。
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: dcf80ffa26ecaeb0f4481b3997146c07bd89be10
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85397911"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 TypingDNA 的教學課程

在本逐步解說中，您將瞭解如何使用 TypingDNA 應用程式來整合 Azure Active Directory B2C 中的範例線上付款應用程式。 藉由使用 TypingDNA 應用程式，Azure AD B2C 客戶可以透過擊鍵 dynamics 和強式客戶驗證，符合[付款服務指示 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) （PSD2）交易需求。 [在這裡](https://www.typingdna.com/)找到更多有關 TypingDNA 的資訊。

 Azure AD B2C 使用 TypingDNA 的技術來捕捉使用者鍵入的特性，並加以記錄和分析，以瞭解每個驗證的熟悉度。 這會新增一層與驗證風險相關的保護，並評估風險層級。 Azure AD B2C 可以叫用其他機制，藉由叫用 Azure MFA、強制電子郵件驗證，或您案例的任何其他自訂邏輯，讓使用者更有信心。

>[!NOTE]
> 此範例原則是以[SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)入門套件為基礎。

## <a name="scenario-description"></a>案例描述

![TypingDNA 架構圖表的螢幕擷取畫面](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>註冊

1. Azure AD B2C 頁面會使用 TypingDNA 的 JavaScript 程式庫來記錄使用者的輸入模式。 例如，使用者名稱和密碼會在註冊進行初始註冊時記錄，然後在每次登入時進行驗證。

2. 當使用者提交頁面時，TypingDNA 程式庫會計算使用者的輸入特性。 之後，將資訊插入 Azure AD B2C 已轉譯的隱藏文字欄位中。 此欄位在 CSS 中是隱藏的。  

    此範例包含具有 JavaScript 和 CSS 修改的 HTML 檔案，而且是由 `api.selfasserted.tdnasignin` 和 `api.selfasserted.tdnasignup` 內容定義所參考。 請參閱[裝載頁面內容](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#hosting-the-page-content)以裝載您的 HTML 檔案。

3. 當使用者提交認證時，Azure AD B2C 現在會在宣告包內具有輸入模式。 它必須呼叫 API （您自己的），才能將此資料傳遞至 TypingDNA REST API 端點。 此 API 包含在範例中（typingDNA-API 介面）。
4. 中介層 API 接著會將輸入模式資料傳遞給 TypingDNA REST API。 在註冊時，會呼叫[檢查使用者端點](https://api.typingdna.com/index.html#api-API_Services-GetUser)來確認使用者不存在，然後呼叫[儲存模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern)端點來儲存使用者的第一個輸入模式。

> [!NOTE]
> 所有對 TypingDNA REST API 端點的呼叫都會傳送 UserId。 這必須是雜湊值。 Azure AD B2C 使用 `HashObjectIdWithEmail` 宣告轉換，以隨機的 salt 和密碼來雜湊電子郵件。  

REST API 呼叫會在中使用模型化 `validationTechnicalProfiles` `LocalAccountSignUpWithLogonEmail-TDNA` ：

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>登入

在後續登入時，使用者的輸入模式會以與使用自訂 HTML 進行註冊時的相同方式計算。 一旦輸入設定檔位於 Azure AD B2C 的宣告包內，Azure AD B2C 就會呼叫您的 API 來呼叫 TypingDNA 的 REST API 端點。 呼叫[檢查使用者](https://api.typingdna.com/index.html#api-API_Services-GetUser)端點以確認使用者存在。 接下來，請確認已呼叫[模式](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern)端點來傳回 `net_score` 。 這 `net_score` 是指出在註冊時，輸入模式與原始的接近程度。

這種類型模式會在中以模型化 `validationTechnicalProfiles` `SelfAsserted-LocalAccountSignin-Email-TDNA` ：

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 如果使用者取得具有高類型的輸入模式 `net_score` ，您可以使用 TypingDNA[儲存類型模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern)端點來儲存。  

`saveTypingPattern`如果您想要 Azure AD B2C （透過 API）來呼叫 TypingDNA 儲存輸入模式端點，您的 api 必須傳回宣告。

存放庫中的範例包含使用下列屬性設定的 API （TypingDNA-API 介面）。

- 定型模式-如果使用者儲存的模式少於兩個，一律會提示您進行 MFA。

- 如果使用者已儲存2-5 模式，而且 `net_score` 低於50，則會提示您進行 MFA。

- 如果使用者已儲存5個以上的模式，而且 `net_score` 低於65，則會提示您進行 MFA。

這些閾值應在您的使用案例中調整。

- 在您的 API 評估之後 `net_score` ，它應該會將布林值宣告傳回 B2C- `promptMFA` 。

- 宣告 `promptMFA` 會在預先條件中用來有條件地執行 AZURE MFA。

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>使用 TypingDNA 上架

1. [在這裡](https://www.typingdna.com/)註冊 TypingDNA
2. 登入 TypingDNA 儀表板，並取得**api 金鑰**和**api 密碼**。 稍後會在 API 介面設定中需要此項

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>整合 TypingDNA 與 Azure AD B2C

1. 在您所選擇的主機提供者上裝載 TypingDNA API 介面
2. `apiKey` `apiSecret` 以 TypingDNA 儀表板中的認證取代 TypingDNA 中的所有和實例-API 介面解決方案
3. 遵循[這裡](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors)的 CORS 需求，在您選擇的提供者上裝載 HTML 檔案
4. 將檔案 `api.selfasserted.tdnasignup` 中的和內容定義的 LoadURI 專案 `api.selfasserted.tdnasignin` 分別取代 `TrustFrameworkExtensions.xml` 為託管 HTML 檔案的 URI。
5. 在**Azure 入口網站**的 [Azure AD] 分頁中，于 [身分識別體驗架構] 底下建立 B2C 原則金鑰。 使用 `Generate` 選項，並將此金鑰命名為 `tdnaHashedId` 。
6. 取代原則檔案中的 TenantId
7. 將所有 TypingDNA REST API 技術設定檔（REST-TDNA-VerifyUser、REST-TDNA-SaveUser、REST-TDNA-CheckUser）中的 ServiceURLs 取代為 TypingDNA api 介面 API 的端點。
8. 將原則檔案上傳至您的租使用者。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 B2C 租使用者，然後選擇 [身分識別體驗架構]。
2. 選取您先前建立的**使用者流程**。
3. 選取 [**執行**使用者流程]

    a. **應用程式**-選取已註冊的應用程式（範例是 JWT）

    b. **回復 url** -選取 [重新導向 url]

    c. 選取 [執行使用者流程]。
  
4. 完成註冊流程並建立帳戶
5. 登出
6. 完成登入流程
7. 產生的 JWT 結果會顯示 TypingDNA 結果

## <a name="live-version"></a>即時版本

•此測試版本已停用 MFA，但是您可以看到在驗證後，宣告是否已出現 MFA 提示的結果 `promptMFA` 。

•在[這裡](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)註冊並登入[這裡](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

- [AAD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [開始使用 AAD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
