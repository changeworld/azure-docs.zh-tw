---
title: 在 B2C 自訂策略中 REST API 聲明交換
titleSuffix: Azure AD B2C
description: 創建與 RESTful 服務交互的 Azure AD B2C 使用者旅程簡介。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337411"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂策略中集成 REST API 聲明交換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

作為 Azure 活動目錄 B2C（Azure AD B2C）的基礎的標識體驗框架，可以在使用者旅程中與 RESTful API 集成。 本文演示如何使用[RESTful 技術設定檔](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)創建與 RESTful 服務交互的使用者旅程。

使用 Azure AD B2C，您可以通過調用自己的 RESTful 服務將您自己的業務邏輯添加到使用者旅程中。 身份體驗框架可以從 RESTful 服務發送和接收資料以交換聲明。 例如，您可以：

- **驗證使用者輸入資料**。 例如，您可以驗證使用者提供的電子郵件地址是否存在於客戶的資料庫中，如果沒有，則顯示錯誤。
- **處理聲明**。 如果使用者以所有小寫字母或所有大寫字母輸入其名字，則 REST API 只需將第一個字母大寫格式，即可將其返回到 Azure AD B2C。
- **通過進一步與企業業務線應用程式集成來豐富使用者資料**。 您的 RESTful 服務可以接收使用者的電子郵件地址、查詢客戶的資料庫，以及將使用者的忠誠度號碼傳回給 Azure AD B2C。 然後，返回聲明可以存儲在使用者的 Azure AD 帳戶中，在下一個業務流程步驟中計算，或包含在訪問權杖中。
- **運行自訂業務邏輯**。 您可以發送推送通知、更新公司資料庫、運行使用者遷移過程、管理許可權、審核資料庫和執行任何其他工作流。

![RESTful 服務聲明交換圖](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>調用 RESTful 服務

交互包括 REST API 聲明和 Azure AD B2C 之間的聲明資訊交換。 您可以使用下列方式來設計與 RESTful 服務的整合：

- **驗證技術設定檔**。 對 RESTful 服務的調用發生在指定[自斷言技術設定檔](self-asserted-technical-profile.md)的[驗證技術設定檔](validation-technical-profile.md)中，或[顯示控制項](display-controls.md)的[驗證顯示控制](display-control-verification.md)。 在使用者旅程圖前進之前，驗證技術設定檔會驗證使用者提供的資料。 您可以使用驗證技術設定檔：

  - 將聲明發送到 REST API。
  - 驗證聲明，並將顯示給使用者的自訂錯誤訊息。
  - 將聲明從 REST API 發送回後續業務流程步驟。

- **索賠交換**。 可以直接聲明交換可以通過直接從[使用者旅程](userjourneys.md)的業務流程步驟調用 REST API 技術設定檔來配置。 這個定義僅限於：

  - 將聲明發送到 REST API。
  - 驗證聲明，並引發返回到應用程式的自訂錯誤訊息。
  - 將聲明從 REST API 發送回後續業務流程步驟。

您可以在自訂原則所定義的使用者旅程圖中，於任何步驟新增 REST API 呼叫。 例如，您可以呼叫 REST API：

- 在登錄期間，就在 Azure AD B2C 驗證憑據之前。
- 登錄後立即登錄。
- 在 Azure AD B2C 在目錄中創建新帳戶之前。
- Azure AD B2C 在目錄中創建新帳戶後。
- 在 Azure AD B2C 發出訪問權杖之前。

![驗證技術設定檔集合](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>傳送資料

在[RESTful 技術設定檔](restful-technical-profile.md)中`InputClaims`，該元素包含要發送到 RESTful 服務的聲明清單。 您可以將聲明的名稱映射到 RESTful 服務中定義的名稱、設置預設值以及使用[聲明解析器](claim-resolver-overview.md)。

您可以使用 SendClaimsIn 屬性配置輸入聲明如何發送到 RESTful 聲明提供程式。 可能的值包括：

- **正文**，以 JSON 格式在 HTTP POST 請求正文中發送。
- **表單**，以安培和"&"分隔鍵值格式在 HTTP POST 請求正文中發送。
- **標頭**，在 HTTP GET 請求標頭中發送。
- **查詢字串**，在 HTTP GET 請求查詢字串中發送。

配置 **"正文"** 選項後，REST API 技術設定檔允許您向終結點發送複雜的 JSON 負載。 有關詳細資訊，請參閱發送[JSON 有效負載](restful-technical-profile.md#send-a-json-payload)。

## <a name="receiving-data"></a>接收資料

REST `OutputClaims` [技術設定檔](restful-technical-profile.md)的元素包含 REST API 返回的聲明清單。 您可能需要將原則中定義的宣告名稱對應至 REST API 中定義的名稱。 還可以包括 REST API 標識提供程式未返回的聲明，只要您設置了 DefaultValue 屬性。

RESTful 聲明提供程式分析的輸出聲明始終希望解析平面 JSON Body 回應，例如：

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

輸出聲明應如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

要解析嵌套的 JSON Body 回應，將 ResolveJsonPathsInJonTokens 中繼資料設置為 true。 在輸出聲明中，將合作夥伴聲明類型設置為要輸出的 JSON 路徑元素。

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


輸出聲明應如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>安全性考量

您必須保護 REST API 終結點，以便只有經過身份驗證的用戶端才能與其通信。 REST API 必須使用 HTTPS 終結點。 將身份驗證類型中繼資料設置為以下身份驗證方法之一：

- **用戶端憑證**使用用戶端憑證身份驗證限制訪問。 只有具有適當證書的服務才能訪問 API。 將用戶端憑證存儲在 Azure AD B2C 策略金鑰中。 詳細瞭解如何使用[用戶端憑證來保護 RESTful 服務](secure-rest-api.md#https-client-certificate-authentication)。
- **基本**使用 HTTP 基本驗證來保護 REST API。 只有經過驗證的使用者 (包括 Azure AD B2C) 才能存取您的 API。 使用者名和密碼存儲在 Azure AD B2C 策略金鑰中。 瞭解如何使用[HTTP 基本驗證來保護 RESTful 服務](secure-rest-api.md#http-basic-authentication)。
- **承載器**使用用戶端 OAuth2 訪問權杖限制訪問。 訪問權杖存儲在 Azure AD B2C 策略金鑰中。 詳細瞭解如何使用[承載權杖來保護 RESTful 服務](secure-rest-api.md#oauth2-bearer-authentication)。

## <a name="rest-api-platform"></a>REST API 平臺
REST API 可以基於任何平臺，並編寫任何程式設計語言，只要它是安全的，並且可以發送和接收[RESTful 技術設定檔](restful-technical-profile.md)中指定的聲明。

## <a name="localize-the-rest-api"></a>當地語系化 REST API
在 RESTful 技術設定檔中，您可能希望發送當前會話的語言/地區設定，如有必要，將引發當地語系化的錯誤訊息。 使用[聲明解析器](claim-resolver-overview.md)，可以發送上下文聲明，如使用者語言。 下面的示例顯示了演示此方案的 RESTful 技術設定檔。

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

REST API 可能需要返回一條錯誤訊息，例如"在 CRM 系統中找不到使用者"。 如果發生錯誤，REST API 應返回 HTTP 409 錯誤訊息（衝突回應狀態碼）。 有關詳細資訊，請參閱[RESTful 技術設定檔](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message)。

這只能通過從驗證技術設定檔調用 REST API 技術設定檔來實現。 這允許使用者更正頁面上的資料，並在頁面提交時再次運行驗證。

需要 HTTP 409 回應才能防止處理此業務流程步驟中的任何後續驗證技術設定檔。

如果直接從使用者旅程中引用 REST API 技術設定檔，則會將使用者重定向回依賴方應用程式，並顯示相關的錯誤訊息。

## <a name="publishing-your-rest-api"></a>發佈 REST API

對 REST API 服務的請求來自 Azure AD B2C 伺服器。 REST API 服務必須發佈到可公開訪問的 HTTPS 終結點。 REST API 呼叫將從 Azure 資料中心 IP 位址到達。

將 REST API 服務及其基礎元件（如資料庫和檔案系統）設計為高度可用。

## <a name="next-steps"></a>後續步驟

有關使用 RESTful 技術設定檔的示例，請參閱以下文章：

- [演練：在 Azure AD B2C 使用者旅程中集成 REST API 聲明交換，作為使用者輸入的驗證](custom-policy-rest-api-claims-validation.md)
- [演練：將 REST API 聲明交換添加到 Azure 活動目錄 B2C 中的自訂策略](custom-policy-rest-api-claims-validation.md)
- [保護您的 REST API 服務](secure-rest-api.md)
- [參考： RESTful 技術設定檔](restful-technical-profile.md)