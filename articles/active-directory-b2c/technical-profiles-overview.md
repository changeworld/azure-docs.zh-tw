---
title: 自訂原則中的技術設定檔總覽
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 的自訂原則中使用技術設定檔。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/11/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3b0e59912d740e30b0e29fb882542f1995ab6f54
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505667"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>關於 Azure Active Directory B2C 自訂原則中的技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

技術設定檔提供一個架構，具有內建的機制，可使用 Azure Active Directory B2C （Azure AD B2C）中的自訂原則與不同類型的合作物件通訊。 技術設定檔可用來與您的 Azure AD B2C 租用戶進行通訊，以建立使用者或讀取使用者設定檔。 技術設定檔可以自我判斷來啟用與使用者的互動。 例如，收集使用者的認證以進行登入，然後呈現註冊頁面或密碼重設頁面。

## <a name="type-of-technical-profiles"></a>技術設定檔的類型

技術設定檔支援下列類型的案例：

- [Azure Active Directory](active-directory-technical-profile.md) - 針對 Azure Active Directory B2C 使用者管理提供支援。
- [JWT 權杖簽發者](jwt-issuer-technical-profile.md) - 發出會傳回給信賴憑證者應用程式的 JWT 權杖。
- **電話要素提供者** - 多重要素驗證。
- [OAuth1](oauth1-technical-profile.md) - 與任何 OAuth 1.0 通訊協定識別提供者建立同盟。
- [OAuth2](oauth2-technical-profile.md) - 與任何 OAuth 2.0 通訊協定識別提供者建立同盟。
- [Openid connect](openid-connect-technical-profile.md) -與任何 OpenID connect 通訊協定識別提供者的同盟。
- [宣告轉換](claims-transformation-technical-profile.md) - 呼叫輸出宣告轉換以操作宣告值、驗證宣告，或為一組輸出宣告設定預設值。
- [RESTful 提供者](restful-technical-profile.md)-呼叫 REST API 服務，例如驗證使用者輸入、擴充使用者資料，或與企業營運應用程式整合。
- [SAML2](saml-technical-profile.md) - 與任何 SAML 通訊協定識別提供者建立同盟。
- [自我判斷](self-asserted-technical-profile.md) - 與使用者進行互動。 例如，收集使用者的認證以進行登入、呈現註冊頁面或密碼重設。
- [工作階段管理](custom-policy-reference-sso.md) - 處理各種不同類型的工作階段。
- [Application Insights](../azure-monitor/app/usage-overview.md)
- 單次[密碼](one-time-password-technical-profile.md)-提供管理單次密碼的產生和驗證的支援。 

## <a name="technical-profile-flow"></a>技術設定檔流程

所有類型的技術設定檔都共用相同的概念。 您傳送輸入宣告、執行宣告轉換，然後與已設定的對象 (例如識別提供者、REST API 或 Azure AD 目錄服務) 進行通訊。 完成此程式之後，技術設定檔會傳回輸出宣告，而且可能會執行輸出宣告轉換。 下圖說明技術設定檔中所參考轉換和對應的處理方式。 不論技術設定檔的互動對象是誰，在執行任何宣告轉換之後，技術設定檔的輸出宣告都會立即儲存在宣告包中。

![說明技術設定檔流程的圖表](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **單一登入（SSO）會話管理**-使用[SSO 會話管理](custom-policy-reference-sso.md)來還原技術設定檔的會話狀態。 
1. **輸入宣告轉換**-每個輸入[宣告轉換](claimstransformations.md)的輸入宣告都會從宣告包中挑選。  輸入宣告轉換的輸出宣告可以作為後續輸入宣告轉換的輸入宣告。
1. **輸入宣告**-從宣告包中挑選宣告，並用於技術設定檔。 例如，[自我判斷技術設定檔](self-asserted-technical-profile.md)會使用輸入宣告來預先填入使用者所提供的輸出宣告。 REST API 技術設定檔會使用輸入宣告將輸入參數傳送給 REST API 端點。 Azure Active Directory 會使用輸入宣告作為唯一識別碼來讀取、更新或刪除帳戶。
1. **技術設定檔執行** - 技術設定檔會與已設定的對象交換宣告。 例如：
    - 將使用者重新導向到識別提供者來完成登入。 成功登入之後，使用者會返回，而技術設定檔則會繼續執行。
    - 呼叫 REST API，並傳送參數作為 InputClaims 及取回資訊作為 OutputClaims。
    - 建立或更新使用者帳戶。
    - 傳送並驗證 MFA 文字訊息。
1. **驗證技術配置**檔-[自我判斷技術設定檔](self-asserted-technical-profile.md)可以呼叫[驗證技術](validation-technical-profile.md)設定檔。 驗證技術設定檔會驗證使用者所分析的資料，並傳回錯誤訊息或良好，其中會包含或不含輸出宣告。 例如，在 Azure AD B2C 建立新帳戶之前，它會檢查目錄服務中是否已經有該使用者存在。 您可以呼叫 REST API 技術設定檔來新增自己的商務邏輯。<p>驗證技術設定檔的輸出宣告範圍僅限於叫用驗證技術設定檔的技術設定檔。 和其他驗證技術設定檔位於相同的技術設定檔下。 如果您想要在下一個協調流程步驟中使用輸出宣告，就必須將輸出宣告新增至叫用驗證技術設定檔的技術設定檔。
1. **輸出宣告**-宣告會傳回給宣告包。 您可以在下一個協調流程步驟或輸出宣告轉換中使用這些宣告。
1. **輸出宣告轉換**-每個輸出[宣告轉換](claimstransformations.md)的輸入宣告都會從宣告包中挑選。 先前步驟的技術設定檔輸出宣告可以作為輸出宣告轉換的輸入宣告。 執行之後，輸出宣告會被放回宣告包中。 輸出宣告轉換的輸出宣告也可以作為後續輸出宣告轉換的輸入宣告。
1. **單一登入（SSO）會話管理**-使用[SSO 會話管理](custom-policy-reference-sso.md)，將技術設定檔的資料保存到會話。


## <a name="technical-profile-inclusion"></a>包含技術設定檔

技術設定檔可以包含另一個技術設定檔，以變更設定或新增功能。  `IncludeTechnicalProfile` 專案是基礎技術設定檔的參考，其中衍生技術設定檔。 在層數上並無限制。 

例如，**AAD-UserReadUsingAlternativeSecurityId-NoError** 技術設定檔包含 **AAD-UserReadUsingAlternativeSecurityId**。 此技術設定檔會將 `RaiseErrorIfClaimsPrincipalDoesNotExist` 中繼資料專案設定為 `true`，而且如果社交帳戶不存在於目錄中，則會引發錯誤。 **AAD-aad-userreadusingalternativesecurityid-noerror-aad-userreadusingalternativesecurityid-noerror**會覆寫此行為，並停用該錯誤訊息。

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** 包含 `AAD-Common` 技術設定檔。

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**Aad-aad-userreadusingalternativesecurityid-noerror-aad-userreadusingalternativesecurityid-noerror**和**aad aad-userreadusingalternativesecurityid-noerror**不會指定所需的**通訊協定**元素，因為它是在**AAD 通用**技術設定檔中指定。

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
