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
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a62cd4ad6d992d8994fbd3e66bd0b90e45aa213
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83636991"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>在您的 Azure AD B2C 自訂原則中整合 REST API 宣告交換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

構成 Azure Active Directory B2C (Azure AD B2C) 基礎的 Identity Experience Framework，可與使用者旅程圖中的 RESTful API 整合。 本文說明如何使用 [RESTful 技術設定檔](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)，建立與 RESTful 服務互動的使用者旅程圖。

您可以使用 Azure AD B2C，呼叫自己的 RESTful 服務，將自己的商務邏輯新增至使用者旅程圖。 Identity Experience Framework 可以從您的 RESTful 服務傳送和接收資料，以交換宣告。 例如，您可以：

- **驗證使用者輸入資料**。 例如，您可能會驗證使用者提供的電子郵件地址是否存在於客戶資料庫中；如果不存在，則顯示錯誤。
- **處理宣告**。 如果使用者輸入全部小寫或全部大寫的名字，您的 REST API 可以將名稱格式設定為只有第一個字母大寫，並傳回 Azure AD B2C。
- **與公司的企業營運應用程式進一步整合來擴充使用者資料**。 您的 RESTful 服務可以接收使用者的電子郵件地址、查詢客戶的資料庫，以及將使用者的忠誠度號碼傳回給 Azure AD B2C。 傳回宣告可能會儲存在使用者的 Azure AD 帳戶中，於下一個協調流程步驟中評估，或包含在存取權杖中。
- **執行自訂商務邏輯**。 您可以傳送推播通知、更新公司資料庫、執行使用者移轉程序、管理權限、稽核資料庫，以及執行其他工作流程。

![RESTful 服務宣告交換的圖表](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>呼叫 RESTful 服務

互動包括 REST API 宣告和 Azure AD B2C 之間的宣告交換資訊。 您可以使用下列方式來設計與 RESTful 服務的整合：

- **驗證技術設定檔**。 RESTful 服務的呼叫會發生在指定[自我判斷技術設定檔](self-asserted-technical-profile.md)的[驗證技術設定檔](validation-technical-profile.md)，或[顯示控制項](display-control-verification.md)的[驗證顯示控制項](display-controls.md)。 在使用者旅程圖前進之前，驗證技術設定檔會驗證使用者提供的資料。 您可以使用驗證技術設定檔：

  - 將宣告傳送至您的 REST API。
  - 驗證宣告，並擲回向使用者顯示的自訂錯誤訊息。
  - 從 REST API 將宣告傳送至後續的協調流程步驟。

- **宣告交換**。 您可以直接從[使用者旅程圖](userjourneys.md)的協調流程步驟，呼叫 REST API 技術設定檔來設定直接宣告交換。 這個定義僅限於：

  - 將宣告傳送至您的 REST API。
  - 驗證宣告，並擲回傳回給應用程式的自訂錯誤訊息。
  - 從 REST API 將宣告傳送至後續的協調流程步驟。

您可以在自訂原則所定義的使用者旅程圖中，於任何步驟新增 REST API 呼叫。 例如，您可以呼叫 REST API：

- 在登入期間，就在 Azure AD B2C 驗證認證之前。
- 緊接在登入之後。
- Azure AD B2C 在目錄中建立新帳戶之前。
- Azure AD B2C 在目錄中建立新帳戶之後。
- Azure AD B2C 發出存取權杖之前。

![驗證技術設定檔集合](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>傳送資料

在 [RESTful 技術設定檔](restful-technical-profile.md)中，`InputClaims` 元素包含要傳送至 RESTful 服務的宣告清單。 您可以將宣告的名稱對應至 RESTful 服務中所定義的名稱、設定預設值，並使用[宣告解析程式](claim-resolver-overview.md)。

您可以使用 SendClaimsIn 屬性來設定如何將輸入宣告傳送給 RESTful 宣告提供者。 可能的值包括：

- **主體**，以 JSON 格式在 HTTP POST 要求本文中傳送。
- **表單**，以符號 '&' 分隔的金鑰值格式在 HTTP POST 要求本文中傳送。
- **標題**，在 HTTP GET 要求標題中傳送。
- **QueryString**，會在 HTTP GET 要求查詢字串中傳送。

設定**主體**選項時，REST API 技術設定檔可讓您將複雜的 JSON 承載傳送至端點。 如需相關資訊，請參閱[傳送 JSON 承載](restful-technical-profile.md#send-a-json-payload)。

## <a name="receiving-data"></a>接收資料

[RESTful 技術設定檔](restful-technical-profile.md)的 `OutputClaims` 元素包含 REST API 傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至 REST API 中定義的名稱。 只要設定 DefaultValue 屬性，您也可以包含 REST API 識別提供者未傳回的宣告。

RESTful 宣告提供者所剖析的輸出宣告，一律會預期剖析一般 JSON 主體回應，例如：

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

輸出宣告應該看起來如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

若要剖析巢狀的 JSON 主體回應，請將 ResolveJsonPathsInJsonTokens 中繼資料設定為 True。 在輸出宣告中，將 PartnerClaimType 設定為您要輸出的 JSON 路徑元素。

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


輸出宣告應該看起來如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>安全性考量

您必須保護 REST API 端點，如此一來，只有經過驗證的用戶端才能與其通訊。 REST API 必須使用 HTTPS 端點。 將 AuthenticationType 中繼資料設定為下列其中一個驗證方法：

- **用戶端憑證**會使用用戶端憑證驗證來限制存取。 只有具有適當憑證的服務才能存取您的 API。 您會將用戶端憑證儲存在 Azure AD B2C 原則金鑰中。 深入了解如何[使用用戶端憑證保護您的 RESTful 服務](secure-rest-api.md#https-client-certificate-authentication)。
- **基本**會使用 HTTP 基本驗證來保護 REST API。 只有經過驗證的使用者 (包括 Azure AD B2C) 才能存取您的 API。 使用者名稱和密碼會儲存在 Azure AD B2C 的原則金鑰中。 了解如何[使用 HTTP 基本驗證保護 RESTful 服務](secure-rest-api.md#http-basic-authentication)。
- **持有人**會使用用戶端 OAuth2 存取權杖來限制存取。 存取權杖會儲存在 Azure AD B2C 原則金鑰中。 深入了解如何[使用持有人權杖保護您的 RESTful 服務](secure-rest-api.md#oauth2-bearer-authentication)。

## <a name="rest-api-platform"></a>REST API 平台
您的 REST API 可以根據任何平台，使用安全且可以傳送和接收宣告的任何程式設計語言撰寫，如 [RESTful 技術設定檔](restful-technical-profile.md)中所指定的程式設計語言。

## <a name="localize-the-rest-api"></a>將 REST API 當地語系化
在 RESTful 技術設定檔中，建議您傳送目前工作階段的語言/地區設定，並在必要時引發當地語系化的錯誤訊息。 使用[宣告解析程式](claim-resolver-overview.md)，您可以傳送內容相關宣告，例如使用者語言。 下列範例顯示的是示範此案例的 RESTful 技術設定檔。

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

您的 REST API 可能需要傳回錯誤訊息，例如「CRM 系統中找不到使用者」。 若發生錯誤，REST API 應會傳回 HTTP 409 錯誤訊息 (衝突回應狀態碼)。 如需詳細資訊，請參閱 [RESTful 技術設定檔](restful-technical-profile.md#returning-error-message)。

您只能從驗證技術設定檔呼叫 REST API 技術設定檔來達成此目的。 這可讓使用者更正頁面上的資料，並在頁面提交時再次執行驗證。

需要 HTTP 409 回應，以避免在此協調流程步驟中處理任何後續驗證技術設定檔。

如果您直接從使用者旅程圖參考 REST API 技術設定檔，則系統會將使用者重新導向回具有相關錯誤訊息的信賴憑證者應用程式。

## <a name="publishing-your-rest-api"></a>發佈您的 REST API

REST API 服務的要求來自 Azure AD B2C 伺服器。 REST API 服務必須發行至可公開存取的 HTTPS 端點。 REST API 呼叫將會從 Azure 資料中心 IP 位址抵達。

將您的 REST API 服務及其基礎元件 (例如資料庫和檔案系統) 設計為高可用性。

## <a name="next-steps"></a>後續步驟

如需使用 RESTful 技術設定檔的範例，請參閱下列文章：

- [逐步解說：將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為使用者輸入的驗證](custom-policy-rest-api-claims-validation.md)
- [逐步解說：將 REST API 宣告交換新增至 Azure Active Directory B2C 中的自訂原則](custom-policy-rest-api-claims-validation.md)
- [保護您的 REST API 服務](secure-rest-api.md)
- [參考：RESTful 技術設定檔](restful-technical-profile.md)
