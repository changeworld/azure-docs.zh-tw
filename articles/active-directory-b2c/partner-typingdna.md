---
title: Azure Active Directory B2C 的 TypingDNA
titleSuffix: Azure AD B2C
description: 瞭解如何將 Azure AD B2C authentication 與 TypingDNA 整合，以根據使用者輸入模式來協助進行身分識別驗證和驗證、提供識別碼驗證解決方案來強制執行多重要素驗證，以及協助符合適用于付款服務指示詞 2 (PSD2) 的 SCA 需求。
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: edbc944e77d2483d32574f8044c72fc3d1292e2a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840429"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 TypingDNA 的教學課程

在本逐步解說中，您將瞭解如何在 Azure Active Directory B2C 中將範例 online 付款應用程式與 TypingDNA 應用程式整合。 藉由使用 TypingDNA 應用程式，Azure AD B2C 客戶可以符合「 [付款服務](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) 」指示詞 2 (PSD2 透過擊鍵動態和強式客戶驗證) 交易需求。 [在這裡](https://www.typingdna.com/)尋找更多關於 TypingDNA 的資訊。

 Azure AD B2C 使用 TypingDNA 的技術來捕捉使用者鍵入特性，並記錄和分析這些使用者是否熟悉每個驗證。 這會增加與驗證風險相關的一層保護，並評估風險層級。 Azure AD B2C 可以叫用其他機制，藉由叫用 Azure AD MFA、強制執行電子郵件驗證，或您的案例的任何其他自訂邏輯，來提供更進一步的信賴使用者。

>[!NOTE]
> 此範例原則是以 [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 入門套件為基礎。

## <a name="scenario-description"></a>案例描述

![TypingDNA 架構圖表的螢幕擷取畫面](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>註冊

1. Azure AD B2C 頁面會使用 TypingDNA 的 JavaScript 程式庫來記錄使用者的輸入模式。 例如，使用者名稱和密碼會記錄在註冊以進行初始註冊，然後在每次登入時進行驗證。

2. 當使用者提交頁面時，TypingDNA 程式庫會計算使用者的輸入特性。 之後，將資訊插入 Azure AD B2C 已轉譯的隱藏文字欄位中。 此欄位會隱藏在 CSS 中。  

    此 [範例包含](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) 具有 JAVASCRIPT 和 CSS 修改的 HTML 檔案，而且是由 `api.selfasserted.tdnasignin` 和 `api.selfasserted.tdnasignup` 內容定義所參考。 請參閱 [裝載頁面內容](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#hosting-the-page-content) 來裝載您的 HTML 檔案。

3. 當使用者提交認證時，Azure AD B2C 現在會在宣告包內有輸入模式。 它必須 (您的) 呼叫 API，才能將此資料傳遞給 TypingDNA REST API 端點。 此 API 包含在 [範例 (typingDNA-API 介面) ](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)中。
4. 中介層 API 接著會將輸入模式資料傳遞給 TypingDNA REST API。 註冊時，會呼叫 [檢查使用者端點](https://api.typingdna.com/index.html#api-API_Services-GetUser) 來確認使用者不存在，然後呼叫 [儲存模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 端點以儲存使用者的第一個輸入模式。

> [!NOTE]
> 所有對 TypingDNA REST API 端點的呼叫都會傳送 UserId。 這必須是雜湊值。 Azure AD B2C 使用 `HashObjectIdWithEmail` 宣告轉換來雜湊具有隨機 salt 和密碼的電子郵件。  

REST API 呼叫會在中使用模型化 `validationTechnicalProfiles` `LocalAccountSignUpWithLogonEmail-TDNA` ：

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>登入

在後續登入時，使用者的輸入模式的計算方式與使用 [自訂 HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml)註冊時的方式相同。 一旦輸入設定檔在 Azure AD B2C 宣告包內，Azure AD B2C 會呼叫您的 API 來呼叫 TypingDNA 的 REST API 端點。 會呼叫 [檢查使用者](https://api.typingdna.com/index.html#api-API_Services-GetUser) 端點來確認使用者是否存在。 接下來， [請確認](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) 已呼叫模式端點以傳回 `net_score` 。 這 `net_score` 是指出輸入模式在註冊時如何接近原始類型的指示。

這種類型模式的模型化方式 `validationTechnicalProfiles` `SelfAsserted-LocalAccountSignin-Email-TDNA` 如下：

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

 如果使用者取得較高的輸入模式 `net_score` ，您可以使用 TypingDNA [儲存輸入模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 端點來儲存。  

如果您想要透過  `saveTypingPattern` 您的 api) Azure AD B2C (來呼叫 TypingDNA 儲存輸入模式端點，則您的 api 必須傳回宣告。

存放庫中的範例包含 (TypingDNA api) 的 API，此 API 是使用下列屬性所設定。

- 定型模式-如果使用者儲存的模式少於兩個，一律會提示 MFA。

- 如果使用者已儲存2-5 模式，且小於 `net_score` 50，則會提示 MFA。

- 如果使用者已儲存5個以上的模式，且小於 `net_score` 65，則會提示 MFA。

您應在使用案例上調整這些閾值。

- 在您的 API 評估之後 `net_score` ，它應該會將布林值宣告傳回給 B2C `promptMFA` 。

- 宣告 `promptMFA` 會在前置條件中使用，以有條件地執行 AZURE AD MFA。

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

## <a name="onboard-with-typingdna"></a>使用 TypingDNA 上線

1. [在這裡](https://www.typingdna.com/)註冊 TypingDNA
2. 登入 TypingDNA 儀表板，並取得 **api 金鑰** 和 **api 秘密**。 稍後會在 API 介面設定中需要這項功能

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>整合 TypingDNA 與 Azure AD B2C

1. 在您選擇的主機服務提供者裝載[TYPINGDNA API 介面](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)
2. `apiKey` `apiSecret` 使用 TypingDNA 儀表板中的認證，將 TypingDNA 中的所有實例和取代為[API 介面](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)的解決方案
3. 遵循[此處](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors)的 CORS 需求，在您選擇的提供者上裝載 HTML 檔案
4. 將檔案 `api.selfasserted.tdnasignup` 中的和內容定義的 LoadURI 專案 `api.selfasserted.tdnasignin` 分別取代 `TrustFrameworkExtensions.xml` 為託管的 HTML 檔案的 URI。
5. 在 **Azure 入口網站** 的 Azure AD 分頁中的身分識別體驗架構下建立 B2C 原則金鑰。 使用 `Generate` 選項並命名此機碼 `tdnaHashedId` 。
6. 取代原則檔案中的 TenantId
7. 將所有 TypingDNA REST API 技術設定檔中的 ServiceURLs 取代 (REST-TDNA-VerifyUser、REST-TDNA-SaveUser、REST TDNA-CheckUser) 以及 [TYPINGDNA Api 介面 api](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)的端點。
8. 將 [原則檔](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) 上傳至您的租使用者。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 B2C 租使用者，然後選擇 [身分識別體驗架構]。
2. 選取您先前建立的 **使用者流程**。
3. 選取 [ **執行** 使用者流程]

    a. **應用程式** -選取已註冊的應用程式 (範例為 JWT) 

    b. **回復 url** -選取重新導向 url

    c. 選取 [執行使用者流程]。
  
4. 進行註冊流程並建立帳戶
5. 登出
6. 登入流程
7. 產生的 JWT 結果會顯示 TypingDNA 結果

## <a name="live-version"></a>Live 版本

•已在此測試版本中停用 MFA，但您可以看到在驗證之後是否已由宣告提示 MFA 的結果 `promptMFA` 。

•[在此註冊並登](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)入[here](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [AAD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [AAD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
