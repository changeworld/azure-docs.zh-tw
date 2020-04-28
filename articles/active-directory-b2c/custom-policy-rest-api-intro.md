---
title: B2C 自訂原則中的 REST API 宣告交換
titleSuffix: Azure AD B2C
description: 介紹如何建立與 RESTful 服務互動的 Azure AD B2C 使用者旅程圖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337411"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>在您的 Azure AD B2C 自訂原則中整合 REST API 宣告交換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Identity Experience Framework （Azure Active Directory B2C （Azure AD B2C）的基礎，可以與使用者旅程圖中的 RESTful Api 整合。 本文說明如何使用[RESTful 技術設定檔](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)來建立與 RESTful 服務互動的使用者旅程圖。

使用 Azure AD B2C，您可以藉由呼叫自己的 RESTful 服務，將自己的商務邏輯新增至使用者旅程圖。 Identity Experience Framework 可以從您的 RESTful 服務傳送和接收資料，以交換宣告。 例如，您可以：

- **驗證使用者輸入資料**。 例如，您可以驗證使用者提供的電子郵件地址是否存在於您的客戶資料庫中，如果沒有，則會顯示錯誤。
- **處理宣告**。 如果使用者輸入全部小寫或全部大寫的名字，您的 REST API 只能使用大寫的第一個字母來設定名稱格式，並將它傳回給 Azure AD B2C。
- **進一步整合公司企業營運應用程式，以豐富使用者資料**。 您的 RESTful 服務可以接收使用者的電子郵件地址、查詢客戶的資料庫，以及將使用者的忠誠度號碼傳回給 Azure AD B2C。 然後，傳回宣告可以儲存在使用者的 Azure AD 帳戶中、在下一個協調流程步驟中評估，或包含在存取權杖中。
- **執行自訂商務邏輯**。 您可以傳送推播通知、更新公司資料庫、執行使用者遷移程式、管理許可權、審核資料庫，以及執行任何其他工作流程。

![RESTful 服務宣告交換的圖表](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>呼叫 RESTful 服務

互動包括 REST API 宣告和 Azure AD B2C 之間的宣告交換資訊。 您可以使用下列方式來設計與 RESTful 服務的整合：

- **驗證技術設定檔**。 RESTful 服務的呼叫發生在指定之[自我判斷技術設定檔](self-asserted-technical-profile.md)的[驗證技術設定檔](validation-technical-profile.md)中，或是[顯示控制項](display-controls.md)的[驗證顯示控制項](display-control-verification.md)。 在使用者旅程圖前進之前，驗證技術設定檔會驗證使用者提供的資料。 您可以使用驗證技術設定檔：

  - 將宣告傳送至您的 REST API。
  - 驗證宣告，並擲回向使用者顯示的自訂錯誤訊息。
  - 從 REST API 將宣告傳送至後續的協調流程步驟。

- **宣告交換**。 直接宣告交換可以藉由直接從[使用者旅程](userjourneys.md)圖的協調流程步驟呼叫 REST API 技術設定檔來設定。 這個定義僅限於：

  - 將宣告傳送至您的 REST API。
  - 驗證宣告，並擲回傳回給應用程式的自訂錯誤訊息。
  - 從 REST API 將宣告傳送至後續的協調流程步驟。

您可以在自訂原則所定義的使用者旅程圖中，於任何步驟新增 REST API 呼叫。 例如，您可以呼叫 REST API：

- 在登入期間，Azure AD B2C 驗證認證之前。
- 在登入之後立即進行。
- Azure AD B2C 會在目錄中建立新的帳戶。
- 之後 Azure AD B2C 在目錄中建立新的帳戶。
- Azure AD B2C 發出存取權杖之前。

![驗證技術設定檔集合](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>傳送資料

在[RESTful 技術設定檔](restful-technical-profile.md)中， `InputClaims`元素包含要傳送至 RESTful 服務的宣告清單。 您可以將您的宣告名稱對應至 RESTful 服務中定義的名稱、設定預設值，以及使用[宣告解析](claim-resolver-overview.md)程式。

您可以使用 SendClaimsIn 屬性來設定如何將輸入宣告傳送至 RESTful 宣告提供者。 可能的值包括：

- **主體**，以 JSON 格式在 HTTP POST 要求主體中傳送。
- **表單**，在 HTTP POST 要求本文中，以連字號 ' & ' 分隔的索引鍵值格式傳送。
- **標頭**，在 HTTP GET 要求標頭中傳送。
- **QueryString**，在 HTTP GET 要求查詢字串中傳送。

設定**Body**選項時，REST API 技術設定檔可讓您將複雜的 JSON 承載傳送至端點。 如需詳細資訊，請參閱[傳送 JSON](restful-technical-profile.md#send-a-json-payload)承載。

## <a name="receiving-data"></a>接收資料

`OutputClaims` [RESTful 技術設定檔](restful-technical-profile.md)的元素包含 REST API 所傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至 REST API 中定義的名稱。 只要您設定了 DefaultValue 屬性，您也可以包含 REST API 識別提供者未傳回的宣告。

RESTful 宣告提供者所剖析的輸出宣告，一律會預期剖析一般 JSON 主體回應，例如：

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

輸出宣告看起來應該如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

若要剖析嵌套的 JSON 主體回應，請將 ResolveJsonPathsInJsonTokens 中繼資料設定為 true。 在輸出宣告中，將 PartnerClaimType 設定為您要輸出的 JSON 路徑元素。

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


輸出宣告看起來應該如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>安全性考量

您必須保護 REST API 端點，如此一來，只有經過驗證的用戶端才能與其通訊。 REST API 必須使用 HTTPS 端點。 將 AuthenticationType 中繼資料設定為下列其中一個驗證方法：

- **用戶端憑證**會使用用戶端憑證驗證來限制存取。 只有具有適當憑證的服務可以存取您的 API。 您會將用戶端憑證儲存在 Azure AD B2C 原則金鑰中。 深入瞭解如何[使用用戶端憑證保護您的 RESTful 服務](secure-rest-api.md#https-client-certificate-authentication)。
- **基本**：使用 HTTP 基本驗證來保護 REST API。 只有經過驗證的使用者 (包括 Azure AD B2C) 才能存取您的 API。 使用者名稱和密碼會儲存在 Azure AD B2C 的原則金鑰中。 瞭解如何[使用 HTTP 基本驗證來保護您的 RESTful 服務](secure-rest-api.md#http-basic-authentication)。
- **持有**者會使用用戶端 OAuth2 存取權杖來限制存取。 存取權杖會儲存在 Azure AD B2C 原則金鑰中。 深入瞭解如何[使用持有人權杖來保護您的 RESTful 服務](secure-rest-api.md#oauth2-bearer-authentication)。

## <a name="rest-api-platform"></a>REST API 平臺
您的 REST API 可以根據任何平臺，並以任何程式設計語言撰寫，只要它是安全的，而且可以傳送和接收宣告，如[RESTful 技術設定檔](restful-technical-profile.md)中所指定。

## <a name="localize-the-rest-api"></a>將 REST API 當地語系化
在 RESTful 技術設定檔中，您可能會想要傳送目前會話的語言/地區設定，並在必要時引發當地語系化的錯誤訊息。 使用[宣告解析程式](claim-resolver-overview.md)，您可以傳送內容相關宣告，例如使用者語言。 下列範例顯示示範此案例的 RESTful 技術設定檔。

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>處理錯誤訊息

您的 REST API 可能需要傳回錯誤訊息，例如「在 CRM 系統中找不到使用者」。 如果發生錯誤，REST API 應該會傳回 HTTP 409 錯誤訊息（衝突回應狀態碼）。 如需詳細資訊，請參閱[RESTful 技術設定檔](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message)。

這只能藉由從驗證技術設定檔呼叫 REST API 技術設定檔來達成。 這可讓使用者更正頁面上的資料，並在頁面提交時再次執行驗證。

需要 HTTP 409 回應，以防止在此協調流程步驟中處理任何後續驗證技術設定檔。

如果您直接從使用者旅程處參考 REST API 技術設定檔，則會將使用者重新導向回具有相關錯誤訊息的信賴憑證者應用程式。

## <a name="publishing-your-rest-api"></a>發佈您的 REST API

REST API 服務的要求來自 Azure AD B2C 伺服器。 REST API 服務必須發行至可公開存取的 HTTPS 端點。 REST API 呼叫將會從 Azure 資料中心 IP 位址抵達。

將您的 REST API 服務及其基礎元件（例如資料庫和檔案系統）設計為高可用性。

## <a name="next-steps"></a>後續步驟

如需使用 RESTful 技術設定檔的範例，請參閱下列文章：

- [逐步解說：在您的 Azure AD B2C 使用者旅程圖中整合 REST API 宣告交換，做為使用者輸入的驗證](custom-policy-rest-api-claims-validation.md)
- [逐步解說：在 Azure Active Directory B2C 中將 REST API 宣告交換新增至自訂原則](custom-policy-rest-api-claims-validation.md)
- [保護您的 REST API 服務](secure-rest-api.md)
- [參考： RESTful 技術設定檔](restful-technical-profile.md)