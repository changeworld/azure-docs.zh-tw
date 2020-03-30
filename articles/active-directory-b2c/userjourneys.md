---
title: UserJourneys | Microsoft Docs
description: 指定 Azure Active Directory B2C 中自訂原則的 UserJourneys 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227004"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

使用者旅程圖會指定明確的路徑，原則可透過這類路徑來讓信賴憑證者應用程式取得使用者所需的宣告。 使用者會透過這些路徑來擷取要呈現給信賴憑證者的宣告。 換句話說，使用者旅程圖會定義當 Azure AD B2C 識別體驗架構處理要求時，使用者要經歷的商務邏輯。

這些使用者旅程可視為範本，以滿足利益共同體各依賴方的核心需求。 使用者旅程有助於定義策略的依賴方部分。 原則可以定義多個使用者旅程圖。 每個使用者旅程圖都是一連串的協調流程步驟。

為了定義原則所支援的使用者旅程圖，會將 **UserJourneys** 元素新增到原則檔的最上層元素下方。

**UserJourneys** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 使用者旅程圖，可定義完整使用者流程所需的所有建構。 |

**UserJourney** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 使用者旅程圖的識別碼，可用來從原則中的其他元素參考它。 [信賴憑證者原則](relyingparty.md)的 **DefaultUserJourney** 元素會指向這個屬性。 |

**UserJourney** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 必須遵循才能獲得成功交易的協調流程序列。 每個使用者旅程圖都由依序執行的已排序協調流程步驟清單所組成。 如果有任何步驟失敗，交易就會失敗。 |

## <a name="orchestrationsteps"></a>OrchestrationSteps

使用者旅程圖會表示為必須遵循才能獲得成功交易的協調流程序列。 如果有任何步驟失敗，交易就會失敗。 這些協調流程步驟會參考這兩個建置組塊以及原則檔中所允許的宣告提供者。 任何負責顯示或呈現使用者體驗的協調流程步驟，也會參考對應的內容定義識別碼。

可以根據業務流程步驟元素中定義的先決條件有條件地執行業務流程步驟。 例如，只有在存在特定聲明或聲明等於或不等於指定值時，才能檢查以執行業務流程步驟。

為了指定已排序的協調流程步驟清單，會新增 **OrchestrationSteps** 元素作為原則的一部分。 這個元素是必要的。

**OrchestrationSteps** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 已排序的協調流程步驟。 |

**OrchestrationStep** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| `Order` | 是 | 協調流程步驟的順序。 |
| `Type` | 是 | 協調流程步驟的類型。 可能的值： <ul><li>**ClaimsProviderSelection**：指出協調流程步驟會向使用者呈現各種宣告提供者，使其可選取其中一個。</li><li>**CombinedSignInAndSignUp**：指出協調流程步驟會呈現社交提供者登入和本機帳戶註冊的組合頁面。</li><li>**ClaimsExchange**：指出協調流程步驟會與宣告提供者交換宣告。</li><li>**獲取聲明**- 指示業務流程步驟讀取輸入聲明。</li><li>**SendClaims**：指出協調流程步驟會使用宣告簽發者所發出的權杖，將宣告傳送到信賴憑證者。</li></ul> |
| ContentDefinitionReferenceId | 否 | 與此協調流程步驟相關聯的[內容定義](contentdefinitions.md)識別碼。 內容定義參考識別碼通常定義於自我判斷技術設定檔中。 但是，在某些案例中，Azure AD B2C 需要在沒有技術設定檔的情況下顯示某些內容。 有兩個示例 - 如果業務流程步驟的類型是以下內容之一：`ClaimsProviderSelection`或者`CombinedSignInAndSignUp`，Azure AD B2C 需要顯示標識提供程式選擇，而無需使用技術設定檔。 |
| CpimIssuerTechnicalProfileReferenceId | 否 | 協調流程步驟的類型為 `SendClaims`。 這個屬性會定義宣告提供者的技術設定檔識別碼，此宣告提供者會發出適用於信賴憑證者的權杖。  如果不存在，就不會建立任何信賴憑證者權杖。 |


**OrchestrationStep** 元素可以包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 先決條件 | 0:n | 必須基於要執行的協調流程步驟滿足的先決條件清單。 |
| ClaimsProviderSelections | 0:n | 適用於協調流程步驟的宣告提供者選取項目清單。 |
| ClaimsExchanges | 0:n | 適用於協調流程步驟的宣告交換清單。 |

### <a name="preconditions"></a>先決條件

**Preconditions** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 先決條件 | 1:n | 根據所使用的技術設定檔，系統會依據宣告提供者選取項目來將用戶端重新導向，或進行伺服器呼叫來交換宣告。 |


#### <a name="precondition"></a>先決條件

**先決條件**元素包含以下屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| `Type` | 是 | 要針對此先決條件執行的檢查或查詢類型。 值可以是 **ClaimsExist** (指定如果指定的宣告存在於使用者目前的宣告組中，就應執行動作) 或 **ClaimEquals** (指定如果指定宣告存在且其值等於指定的值，就應執行動作)。 |
| `ExecuteActionsIf` | 是 | 使用 True 或 False 測試，來決定是否應執行先決條件中的動作。 |

**Precondition** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 值 | 1:n | 要查詢的 ClaimTypeReferenceId。 另一個值元素包含要檢查的值。</li></ul>|
| 動作 | 1:1 | 當協調流程步驟內的先決條件檢查為 True 時應執行的動作。 如果將 `Action` 的值設定為 `SkipThisOrchestrationStep`，就不應執行相關聯的 `OrchestrationStep`。 |

#### <a name="preconditions-examples"></a>先決條件範例

下列先決條件會檢查使用者的 objectId 是否存在。 在使用者旅程圖中，使用者已選取來使用本機帳戶登入。 如果 objectId 存在，請略過此協調流程步驟。

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

下列先決條件會檢查使用者是否已使用社交帳戶登入。 嘗試在目錄中尋找使用者帳戶。 如果使用者登錄或使用本地帳戶註冊，請跳過此業務流程步驟。

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

先決條件可以檢查多個先決條件。 下列範例會檢查 'objectId' 或 'email' 是否存在。 如果第一個條件為 true，則旅程將跳到下一個業務流程步驟。

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

類型 `ClaimsProviderSelection` 或 `CombinedSignInAndSignUp` 的協調流程步驟可能包含使用者可用以登入的宣告提供者清單。 `ClaimsProviderSelections` 元素內部的元素順序會控制呈現給使用者的識別提供者順序。

**聲明提供程式選擇**元素包含以下元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 提供可選取的宣告提供者清單。|

**聲明提供程式選擇**元素包含以下屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 顯示選項| 否 | 控制單個聲明提供程式選擇可用的案例的行為。 可能的值：（ `DoNotShowSingleProvider` 預設），使用者將立即重定向到聯合標識提供程式。 或者 `ShowSingleProvider` Azure AD B2C 顯示帶有單個標識提供程式選擇的登錄頁。 要使用此屬性，[內容定義版本](page-layout.md)必須 `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0`高於和上方。|

**ClaimsProviderSelection** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | 否 | 宣告交換的識別碼，這會在宣告提供者選取項目的下一個協調流程步驟中執行。 您必須指定這個屬性或 ValidationClaimsExchangeId 屬性，但不需同時指定。 |
| ValidationClaimsExchangeId | 否 | 宣告交換的識別碼，這會在目前協調流程步驟中執行以驗證宣告提供者選取項目。 您必須指定這個屬性或 TargetClaimsExchangeId 屬性，但不需同時指定。 |

### <a name="claimsproviderselection-example"></a>ClaimsProviderSelection 範例

在下面的業務流程步驟中，使用者可以選擇使用 Facebook、LinkedIn、Twitter、Google 或本地帳戶登錄。 如果使用者選取其中一個社交識別提供者，第二個協調流程步驟就會使用 `TargetClaimsExchangeId` 屬性中指定的所選取宣告交換來執行。 第二個協調流程步驟會將使用者重新導向到該社交識別提供者，以完成登入程序。 如果使用者選擇使用本機帳戶登入，Azure AD B2C 就會停留在同一個協調流程步驟 (相同的註冊頁面或登入頁面)，並略過第二個協調流程步驟。

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 根據所使用的技術設定檔，系統會依據所選取的 ClaimsProviderSelection 來將用戶端重新導向，或進行伺服器呼叫來交換宣告。 |

**ClaimsExchange** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 宣告交換步驟的識別碼。 識別碼會用於參考原則中來自宣告提供者選取步驟的宣告交換。 |
| TechnicalProfileReferenceId | 是 | 要執行的技術設定檔識別碼。 |
